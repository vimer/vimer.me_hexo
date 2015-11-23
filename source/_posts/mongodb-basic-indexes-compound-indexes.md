title: MONGODB的BASIC INDEXES 和 COMPOUND INDEXES 研究
date: 2012-07-12 01:18:41
tags: [MongoDB,索引]
categories: tech
v_category: 技术
v_categoryLink: tech
---
前几天看到一个关于 MongoDB 深入索引的PPT

[MongoDB Indexing: The Details](http://www.slideshare.net/mongodb/mongodb-indexing-the-details)

仔细的从头看到尾, 里面有个关于Compound Indexes的Range and Equality的讲解,在ppt第129页,重新回顾下这个例子.向一个collection中插入 9 条数据,像下面这样:
<pre>
<code class="javascript">
> db.good.find()
{ "_id" : ObjectId("4e8d629d8ad8bdf2ed6c1990"), "x" : 1, "y" : "b" }
{ "_id" : ObjectId("4e8d62a38ad8bdf2ed6c1991"), "x" : 3, "y" : "d" }
{ "_id" : ObjectId("4e8d62ad8ad8bdf2ed6c1992"), "x" : 4, "y" : "g" }
{ "_id" : ObjectId("4e8d62b28ad8bdf2ed6c1993"), "x" : 5, "y" : "c" }
{ "_id" : ObjectId("4e8d62ba8ad8bdf2ed6c1994"), "x" : 6, "y" : "a" }
{ "_id" : ObjectId("4e8d62c18ad8bdf2ed6c1995"), "x" : 7, "y" : "e" }
{ "_id" : ObjectId("4e8d62ce8ad8bdf2ed6c1996"), "x" : 8, "y" : "c" }
{ "_id" : ObjectId("4e8d62d38ad8bdf2ed6c1997"), "x" : 9, "y" : "f" }
{ "_id" : ObjectId("4e8d719a6cee6416a5a75a43"), "x" : 5, "y" : "d" }
</code>
</pre>
<!--more-->

然后给x 和 y进行联合索引
<pre>
<code class="javascript">
db.good.ensureIndex({x:1,y:1})
</code>
</pre>

我们来进行这样的查找
<pre>
<code class="javascript">
> db.good.find({x:{$gte:4}, y:’c’}).explain()
{
	"cursor" : "BtreeCursor x_1_y_1",
		"nscanned" : 7,
		"nscannedObjects" : 2,
		"n" : 2,
		"millis" : 0,
		"nYields" : 0,
		"nChunkSkips" : 0,
		"isMultiKey" : false,
		"indexOnly" : false,
		"indexBounds" : {
			"x" : [
				[
					4,
					1.7976931348623157e+308
				]
			],
			"y" : [
				[
					"c",
					"c"
				]
			]
		}
}
</code>
</pre>

可以看出 nscanned 非常高! 而 n只有 2 .官网上有这样一句话:

>If nscanned is much higher than nreturned, the database is scanning many objects to find the target objects. Consider creating an index to improve this.

这里nscanned可以认为是扫描的记录数.n为返回的记录数

让我们配合PPT看下 nscanned:7是怎么来的:

![BTree](http://ww3.sinaimg.cn/large/744e593bgw1end6qzl43vj20fy09t0ta.jpg)

这是MongoDB的B-tree索引树,因为x>=4 && y=’c’,所以先选择左枝搜索,左枝搜索了4/g 和 5/c ,(5/c符合条件),然后搜索 右枝 搜索了 7/e, 6/a ,8/c, 9/f ,(8/c符合条件). 任何 符合的 x都要被check一下.

###延伸

看了PPT后到此结尾了, 真遇到这种情况,效率可不乐观,于是稍微思考了下, y 在 这颗树中只有两个节点含有,也就是说 既然是 ‘与’  那就只要先把 y 筛选出来 ,搜索次数就大大减半了 .

我们在 y 上再进行Basic Indexes 的建立.

<pre>
<code class="javascript">
db.good.ensureIndex({y:1})
</code>
</pre>

这样如果搜索时会先 搜索 y  ,也就只有2次搜索了.看下实际情况:

<pre>
<code class="javascript">
> db.good.find({x:{$gte:4}, y:’c’}).explain()
{
	"cursor" : "BtreeCursor y_1",
		"nscanned" : 2,
		"nscannedObjects" : 2,
		"n" : 2,
		"millis" : 0,
		"nYields" : 0,
		"nChunkSkips" : 0,
		"isMultiKey" : false,
		"indexOnly" : false,
		"indexBounds" : {
			"y" : [
				[
					"c",
					"c"
				]
			]
		}
}
</code>
</pre>

正如预料的一样. 直接走 基本索引了.

