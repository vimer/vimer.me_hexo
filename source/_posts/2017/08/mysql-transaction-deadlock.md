title: MySql InnoDB事务死锁问题
date: 2017-08-05 23:30:36
tags: [Mysql,InnoDB,死锁]
categories: 技术


---
> 从大学时候应该是09年就开始不停的写技术博客，写博客的地方也搬了多处，很多博文基本不会搬过来，每次更新域名都会想重新去写第一篇，第二篇...... 这次博文更新也隔了一年多之久，这次更新，也是不管希望不管自己多忙能把以前的习惯一直延续下去......

> 这片博客的文章内容发生于某天晚上服务端日志报了一大堆 Deadlock found when trying to get lock; try restarting transaction，当时通过字面意思果断可以判断出是Sql事务死锁了，与此同时，MySql所在服务器CPU也远超100%，第一直觉是事务死锁导致线程阻塞，请求数瞬间过多导致CPU超100%。

> 什么情况下会导致死锁呢？根据以往经验，应该是同时操作了（update/delete等）同一条数据库的记录导致了事务阻塞，由于事务的执行时间过长导致了其他事务的等待，导致CPU攀高。

> 死锁这个问题基本上可以说涉及了InnoDB大部分特性：行锁设计，非锁定读，事务，MVCC，聚簇索引 等等，有必要写篇文章好好分析下。

<!--more-->


若要全方面解决InnoDB死锁问题，我们需要对下面几个方面了如指掌。

- MVCC
- 事务的隔离级别
- 共享锁和排它锁
- 快照读和当前读
- 聚簇索引
- INNODB_TRX，INNODB_LOCKS，INNODB_LOCK_WAITS 

#### MVCC

多版本并发控制是指InnoDB存储引擎通过行多版本的方式来读取当前执行时间数据库中的行数据，简单说就是读不加锁，读写不冲突。这样会极大的增加数据库的并发性能。有人问了，读不加锁，那么写会加锁的啊，这个时候再同时进行读能正常读取吗，答案是肯定的，读取操作不会因为锁没释放而等待，而是会去读取行的一个快照数据（不同事务的隔离级别，访问的快照数据不同）。


#### 事务的隔离级别
* Read Uncommited
* Read Çommited
* Repeatable Read
* Serializable

其中，Read Uncommited 和 Serializable 比较极端，前一个可以读取未提交的记录，后一个读写冲突，并发性低，所以两者在一般情况下都不建议使用，用的最多是RC和RR。

RC级别有关键词：最新数据
RR级别的关键词：最初数据

我们来看具体的例子，我们模拟两个并发事务请求，分别是：
Connection A:
<pre>
<code class="sql">
start TRANSACTION; 
select * from tDeadLock where id = 1;
</code>
</pre>

Connection B:
<pre>
<code class="sql">
start TRANSACTION; 
update tDeadLock set count=count+1 where id = 1;
</code>
</pre>

在说这两个请求之前，我们先看tDeadLock这个测试表的结构


| 字段名  | 字段类型         | 字段索引 |
| ------ | ----------: | :-----: |
| id     | int | Primary Key |
| count     | int |  |















