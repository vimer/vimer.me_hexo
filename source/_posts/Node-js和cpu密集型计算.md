title: Node.js和cpu密集型计算
date: 2014-12-27 19:35:46
tags: [Node.js, cpu密集型计算]
---

直接上代码:
```javascript
setTimeout(function () {
	for (var i = 0; i < 10000000000; i++) {
		//CPU密集
	}
}, 200);

setTimeout(function () {
	console.log('210 ms...');
}, 210);
```
这个小例子很经典的解释了Node.js遇到密集型CPU的时候问题,这个程序分别在两个时间点触发,虽然在210ms得时候回调内得程序执行非常很快,但是在200ms得时候处理了一个CPU非常密集型的任务就导致整个线程阻塞了.
至于Event Loop机制可以参考:
http://www.infoq.com/cn/articles/nodejs-weakness-cpu-intensive-tasks
这篇文章中关于**Event Loop和Tick**的一段:

>每个Node程序的主线程都有一个event loop，JavaScript代码全在这个单线程下运行。所有的I/O操作以及对本地API的调用，或者是异步的（借助程序所在平台的机制），或者运行在另外的线程中。这全都是通过libuv处理的。所以当socket上有数据过来，或本地API函数返回时，需要有种同步的方式调用对刚发生的这一特定事件感兴趣的JavaScript函数。

>在发生事件的线程中直接调用JS函数是不安全的，因为那样也会遇到常规多线程程序遇到的问题，竞态条件、非原子操作的内存访问等等。所以要以一种线程安全的方式把事件放在队列中，如果写成代码，大致应该是这样的：
```C
lock (queue) {
    queue.push(event);
}
```
然后在执行JavaScript的主线程中（即event loop的c代码）：

```c
while (true) {
    // tick开始
    lock (queue) {
        var tickEvents = copy(queue); 
		// 将当前队列中的条目复制的线程自有的内存中
        queue.empty(); // ..清空共享的队列
    }
    for (var i = 0; i < tickEvents.length; i++) {
        InvokeJSFunction(tickEvents[i]);
    }
    // tick结束
}
```
>while (true) (在真正的node源码中并不是这样的；这里只是为了说明)表示event loop。里面的for为队列中的每个事件调用JS函数。Event loop在每个tick中都会调用与外部事件相关联的零个或多个回调函数，一旦队列被清空，并且最后一个函数返回后，tick就结束了。然后回到开始（下一个tick），重新开始检查其它线程在JavaScript运行时加到队列中的事件。

>那么这个队列中的东西都是谁放进来的呢？
```
process.nextTick
setTimeout/setInterval
I/O (来自fs、net等)
crypto中的CPU密集型函数，比如crypto streams、pbkdf2和PRNG
所有使用libuv工作队列异步调用C/C++库的本地模块
```
同样下面代码当调用t1接口时也会导致t2的接口变慢.
```javascript
 app.get("/t1", function* (next) {
	for (var i=0; i<5000000000; i++) {
		//cpu密集型
	}
	console.log("t1");
});
app.get("/t2", function* (next) {
	console.log("t2");
});
```
这么看Node.js是否能够胜任cpu密集型操作呢,答案当然是否定的,Node.js虽然是单线程,但是可以开启多个Node.js实例来充分利用多核的优势,另外Node.js还支持子进程,通过子进程来计算...
for.js
```javascript
var calc = function() {
     for (var i = 0; i < 10000000000; i++) {
     }
}
process.on('message', function(m) {
  //接收主线程发来消息
     console.log("recv mesage");
     calc();
     process.send(1);
});
process.on('SIGHUP', function() {
          process.exit();//收到kill信息，进程退出
});
```

main.js
```javascript
var fork = require('child_process').fork;
setTimeout(function () {
    var worker = fork("./for.js");
    worker.on("message", function(m) {
        //接收工作进程的结果
        console.log("world");
        worker.kill();
	});
	worker.send(1);
}, 200);

setTimeout(function () {
  console.log('hello');
}, 210);
```
上面这个程序就是利用进程间来通信,for.js是子进程执行的密集型计算.main.js可以继续调度其他程序.充分利用了cpu.


