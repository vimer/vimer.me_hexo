title: "JS脚本强制KILLOP, MONGODB慢查询"
date: 2012-07-11 10:32:52
tags: [MongoDB,javascript]
---
很多时候,有些慢查询导致MongoDB压力很大, 花了点时间写了个脚本循环kill些慢查询操作.

```C
var j=0;
var _DEF_TIME=10; //设置慢查询时间
for (var i in db.currentOP().inprog) {
	var op = "";
	var opid;
	var memProg={}
	if (typeof(undefined) == typeof(db.currentOP().inprog[i])) {
		continue;
	}
	memProg=db.currentOP().inprog[i];
	op = memProg.op;
	opid = memProg.opid;
	print(i);
	if (op=="query") {
		if (memProg.hasOwnProperty(‘secs_running’)) {
			var useTime = memProg.secs_running;
			if (useTime >= _DEF_TIME) {
				db.killOp(opid);
				j++;
				print("killed "+j+" Query Operation!");
			}
		}
	}
}
```
运行方式:

```C
./mongo 10.15.107.154:30000 < ./Self-Script/killSlow.JS
```
(PS:2012年的一篇文章)

