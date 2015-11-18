title: 如何让Node.js原子性操作MongoDB
date: 2014-12-21 20:47:06
tags: [Node.js,MongoDB,原子性]
---

假设基础数据为:
```javascript
{
	"name" : "cb",
	"data" : 0,
}
```
有时候为了充分利用多核,会同时开启多个node进程,但是若部分代码若涉及到操作mongodb就会有下面现象.
"理想情况下",多个进程执行完后得数据为:
```javascript
{
	"name" : "cb",
	"data" : 6000,
}
```
其实不然 !
我们看看获得结果是怎么样的.
```javascript
co(function* () {
	for (var i=0; i<3000; i++) {
		var getTest = yield mongoCtest.findOne({"name":"cb"}, {"fields":{"_id":0}});
		getTest.data =  getTest.data+1;
		console.log(getTest);
		yield mongoCtest.update({"name":"good"}, {"$set":getTest});
	}
})();
```
上面的程序同时开两个,多个进程同时操作一个数据.
可能结果会是:
![多进程操作Mongo](http://ww1.sinaimg.cn/large/744e593bgw1enhm3w6525j20wo0n6459.jpg)

我们知道Mongo是不支持事务的,如果你能容忍上面的弱一致性,那么没问题.但是如果你不能容忍，要么考虑MySql关系型数据库,要么自己解决事务问题.

下面我来说下基于Mongo怎么解决事务问题.
我们需要对每个数据加一个version来控制.直接上代码:
```javascript
co(function* () {
	for (var i=0; i<3000; i++) {
		while (1) {
			var getTest = yield mongoCtest.findOne({"name":"cb"}, {"fields":{"_id":0}});
			getTest.data =  getTest.data+1;
			var originalVer = getTest.ver;
			getTest.ver = getTest.ver+1;
			console.log(getTest);
			var ret = yield mongoCtest.update({"name":"cb", "ver":originalVer}, {"$set":getTest});
			if (ret)  break;
		}
	}
})();
```

下面就是我们想要的结果:)

![原子性控制](http://ww1.sinaimg.cn/large/744e593bgw1enhm5tgq6qj20xt0njqbo.jpg)

PS: 个人写博,比较随性,若有哪里不清楚可以直接回复 .

