title: "Node.js异步回调池, Maximum call stack size exceeded error"
date: 2015-01-17 16:12:17
tags: [Node.js,异常]
categories: 技术
---
这种错误主要发生在js调用栈的限制,如下面递归调用的代码:

<pre>
<code class="javascript">
(function a() {
	a();
})();
</code>
</pre>

但是在Node.js中除了因为调用栈限制导致这种错误,还会因为异步回调的池满了导致这个问题,正常来说每个异步函数调用的回调函数,基本都是由事件触发回调的,这些事件触发器存在于一个地方,异步回调完成后,这个内存才会被回收.
<!--more-->

比如下面这种代码:
<pre>
<code class="javascript">
for (var i=0;i< docs.length; i++) {
	mongo.test.insert(docs[i], function(err, docs) {
		//....
	});
}
</code>
</pre>

如果length=10000,瞬间发起这么多个异步操作,异步回调池肯定就满了,但是如果这些异步操作,1个完成之后发起下一个,这样池肯定没问题.

解决这种问题得方法:

>1.可以使用队列批量回调,限制在回调池大小之内
2.同步每一个回调

有两个现成得module可以解决这种问题:

<pre>
<code class="nodejs">
/*bagpipe*/
var async = function(val, callback) {
	setTimeout(function() {}, 1000);
}
var bagpipe = new Bagpipe(500);
for (var i=0; i< docs.length; i++) {
	bagpipe.push(async, docs[i].value, function(callback) {

	});
}

/*async*/
var q = async.queue(function(task, callback) {
	setTimeout(function() {
		log.info(task);
		callback(null, {});
	}, 5000);
}, 10);
q.drain = function() {
	log.info("finish");
}
for (var i=0; i< 100; i++) {
	q.push(i, function(err, data) {

	});
}
</code>
</pre>

