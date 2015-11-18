title: iOS方法与javascript函数互相调用
date: 2015-03-12 17:54:44
tags: [iOS,javascript]
---
由于native在开发和维护及速度上相比较Web还是差了一大截，Web也日渐成熟，移动端硬件性能也越来越高. 很多App嵌入Web由原来的技巧性东西现在成为一个趋势, 更有可能native日渐会被HTML5所代替. 

第一次接触在应用中嵌入Web，是在研发输入法的时候, 有个About页面(PC端)，这个页面就是使用Web, 本地缓存+外链, 这样更新内容时就不需要升级应用了.现如今HTML5被广泛利用, 尤其数据展示方面以及 频繁改动比较大的时候可以充分利用Web的灵活性.

这篇博文主要针对两点
1.native代码调用js
2.js调用native

要实现ios和js的互调, 就必须有web代码. 比方说我们从ios调用js开始, 在web中实现一个简单的函数:
```javascript
function showDivMsg(msg) {
       $('msg').innerHTML = msg;
}
```
在ios中加上这么一行代码: 
```javascript
[self.webView stringByEvaluatingJavaScriptFromString:@"showDivMsg('iOS中调用js,Success!')"];
```
对，上面这一小行代码native就可以和js进行通信了，其中showDivMsg为js中的函数.

对于
```Objective-C
- (NSString *)stringByEvaluatingJavaScriptFromString:(NSString *)script;
```
这个方法的参数是传入一个script,  比方说你可以直接alert.
```
stringByEvaluatingJavaScriptFromString:@"showDivMsg('alert('ok')');
```
对于这个webview，script操作的内存是在10M之内. 不然webview就会抛异常了.

加载html的时候，使用loadRequest方法，该方法进行异步加载
```
NSString *path = [[NSBundle mainBundle] pathForResource:@"www/index" ofType:@"html"];
[self.webView loadRequest:[NSURLRequest requestWithURL:[NSURL fileURLWithPath: path]]];
```
从js中调用本地关键在于函数中调用window.location=uri，然后在iOS中就会收到shouldStartLoadWithRequest的消息. 在WebView加载新界面的时候会调用此方法, 所以使用了 window.location，同样使用iframe也是一样的.
```Objective-C
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType) {
 //.....
}
```
互相调用的时候，不仅要能够调用互相的方法(函数), 还要能够进行传参 ，首先NSURL有三个属性, 分别是:
>***scheme***
The scheme for the NSURL object. For example, in the URL http://www.example.com/index.html, the scheme is http.
***host	***
The host for the NSURL object (for example, www.example.com). May be the empty string.
***path	***
The path for the NSURL object (for example, /index.html). If the path begins with a tilde, you must first expand it by calling stringByExpandingTildeInPath.

操作如下:
```
(NSURLRequest *)request 

NSString* scheme = request.URL.scheme;
NSString* method = request.URL.host;
NSString* fragment = [[request.URL.fragment stringByReplacingOccurrencesOfString:@"+" withString:@" "] stringByReplacingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
```
所以思路是 host为调用的方法, fragment为传的参数. 

如下为fragment(使用jsonString来传参. ios.method为host)
```
var obj = new Object();
obj.msg = msg;
var JSONString = JSON.stringify(obj);
var uri = 'phoneGap://ios.method#' + JSONString; 
```
整体思路就是这样, 具体代码可见自己写的一个demo:

https://github.com/InfoGeeker/phoneGap

---
