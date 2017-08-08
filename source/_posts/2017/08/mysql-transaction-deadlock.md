title: MySql InnoDB事务死锁问题
date: 2017-08-05 23:30:36
tags: [Mysql,InnoDB,死锁]
categories: 技术

---

> 这片博客的文章内容发生于某天晚上服务端日志报了一大堆 Deadlock found when trying to get lock; try restarting transaction，当时通过字面意思果断可以判断出是Sql事务死锁了，与此同时，MySql所在服务器CPU也远超100%，第一直觉是事务死锁导致线程阻塞，请求数瞬间过多导致CPU超100%。

> 什么情况下会导致死锁呢？根据以往经验，应该是同时操作了（update/delete等）同一条数据库的记录导致了事务阻塞，由于事务的执行时间过长导致了其他事务的等待，导致CPU攀高。

> 死锁这个问题基本上可以说涉及了InnoDB大部分特性：行锁设计，非锁定读，事务，MVCC，聚簇索引 等等，有必要写篇文章好好分析下。

<!--more-->

若要全方面解决InnoDB死锁问题，我们需要对下面几个方面了如指掌。

- MVCC
- 事务的隔离级别
- 快照读和当前读
- 幻读
- 聚簇索引
- 死锁
- INNODB_TRX，INNODB_LOCKS，INNODB_LOCK_WAITS 

### 一、MVCC

多版本并发控制是指InnoDB存储引擎通过行多版本的方式来读取当前执行时间数据库中的行数据，简单说就是读不加锁，读写不冲突。这样会极大的增加数据库的并发性能。有人问了，读不加锁，那么写会加锁的啊，这个时候再同时进行读能正常读取吗，答案是肯定的，读取操作不会因为锁没释放而等待，而是会去读取行的一个快照数据（不同事务的隔离级别，访问的快照数据不同）。

### 二、事务的隔离级别
* Read Uncommited
* Read Çommited
* Repeatable Read
* Serializable

其中，Read Uncommited 和 Serializable 比较极端，前一个可以读取未提交的记录，后一个读写冲突，并发性低，所以两者在一般情况下都不建议使用，用的最多是RC和RR。

RC级别有关键词：最新数据
RR级别的关键词：最初数据

我们来看具体的例子，我们模拟两个并发事务请求，分别是：
###### Connection A:
<pre>
<code class="sql">start TRANSACTION; 
select * from tDeadLock where id = 1;
</code>
</pre>

###### Connection B:
<pre>
<code class="sql">start TRANSACTION; 
update tDeadLock set count=count+1 where id = 1;
</code>
</pre>

在说这两个请求之前，我们先看tDeadLock这个测试表的结构


| 字段名  | 字段类型         | 索引类型 | 值 |
| :---: | :--------: | :-----: | :-----: |
| id     | int | Primary Key |1|
| count     | int | 无 |1|
<p>
我们再回头看两个Connection，A事务中已经开始事务，读取了id=1的数据，但是没有结束事务，同时B中进行并发访问，将id=1的count加1,两者事务都没有提交，因为进行了update，id=1的加了一个x锁，在A连接进行读取的过程中，RC和RR事务的隔离级别下，会使用非锁定一致性读。当A事务未关闭，B事务进行commit后，在RC和RR情况下A事务显示的结果就不一样了。

#### 1、RC

在当前两个连接开始之前先执行下面两句，强制使用RC事务隔离级别。
<pre>
<code class="sql">SET @@global.tx_isolation = 'READ-COMMITTED'; 
SET SESSION tx_isolation = 'READ-COMMITTED';
</code>
</pre>
可以使用下面命令验证是否修改成功。
<pre>
<code class="sql">SELECT @@global.tx_isolation;
SELECT @@tx_isolation;
</code>
</pre>

RC事务隔离级别的关键词是： 最新数据，所以当B事务进行了commit，在A事务中进行查询会显示count的值为2。


#### 2、RR

同理我们强制使用RR事务隔离级别。

<pre>
<code class="sql">SET @@global.tx_isolation = 'REPEATABLE-READ'; 
SET SESSION tx_isolation = 'REPEATABLE-READ'; 
</code>
</pre>

RR事务隔离级别的关键词是：最初数据，所以当B事务进行了commit，在A事务中进行查询的会显示count依然为1。

### 三、快照读和当前读
可以这么认为除select外是快照读（select for update，select lock in share mode特殊除外），其他都可以认为是当前读。读取的是记录的最新版本数据，为了保证并发的时候读取的是最新数据需要对改记录进行X锁。

我们来看下例子，在之前的tDeadLock表增加一条记录

| id  | count         | 
| :---: | :--------: | 
| 1     | 1 | 
| 2     | 1 |


#### RR事务隔离级别

我们来看具体的例子，我们模拟两个并发请求，分别是：
###### Connection A:
<pre>
<code class="sql">start TRANSACTION; 
select * from tDeadLock;
</code>
</pre>

| id  | count         | 
| :---: | :--------: | 
| 1     | 1 | 
| 2     | 1 |
<p>
###### Connection B:
<pre>
<code class="sql">update tDeadLock set count=count+1 where id = 1;
</code>
</pre>

在事务A中继续执行
<pre>
<code class="sql">select * from tDeadLock for update;
</code>
</pre>

| id  | count         | 
| :---: | :--------: | 
| 1     | 2 | 
| 2     | 1 |
<p>
当前读，查询到的是获取更新过后的数据，我们再在事务A中运行
<pre>
<code class="sql">select * from tDeadLock;
</code>
</pre>

| id  | count         | 
| :---: | :--------: | 
| 1     | 1 | 
| 2     | 1 |
<p>
快照读，获取的是当前事务之前的快照。可以看出不同的事务隔离级别下，快照读和当前读获取的数据是不一样的。（因为RC获取最新数据，RR是获取最初数据，两个概念一结合就比较好理解了。）

### 四、死锁

文章开头解释了为什么会导致CPU 100%，但是就算100%，并发访问量比较大，也只是处理会变慢而已，为什么会产生死锁呢？

