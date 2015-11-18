title: 去年开发WebApp"书心"手写应用技术部分总结
date: 2015-04-25 14:59:54
tags: [WebApp, 像素, viewport]
---

#### 一. DP、devicePixelRatio、物理像素、设备无关像素、Viewport一些知识点

首先需要知道几个概念:
* DP - 设备无关像素(为了解决物理像素适配问题)
* window.devicePixelRatio - 设备物理像素和设备无关像素(DP)的比例
window.devicePixelRatio = 物理像素 / DP

以iPhone为例子，下面是常见iPhone物理像素，设备无关像素(又名开发像素)及倍数
* iPhone6p 
物理像素 1242x2208 
开发像素 414x736 
倍数 3x
<!--more-->

* iPhone6 
物理像素 750x1334
开发像素 375x667
倍数 2x

* iPhone5,5s 
物理像素 640x1136
开发像素 320x568 
倍数 2x

* iPhone4,4s 
物理像素 640x960 
开发像素 320x480 
倍数 2x

对于Web APP这边，我们也需要了解几个概念:
> screen.width和screen.height (屏幕/设备真实分辨率, 其中视网膜设备为其开发像素,比如iphone5s真实分辨率为640*1136，而开发像素为320*568)
screen.width * window.devicePixelRatio = 物理像素值
如iPhone4: 
320*2 = 640
window.innerWidth为浏览器尺寸(包含滚动条)
document.documentElement.clientWidth也为浏览器尺寸(不包含滚动条)

下面着重说说viewport，当你创建一个页面时，在没有设置viewport的时候，为了能在浏览器上展示所有内容，最常见的办法就是缩放至到看到整个页面内容(layout viewport). 如:
![No-viewport](http://ww4.sinaimg.cn/large/744e593bgw1erhtrzpbhrj20cm05caaf.jpg)
这张截图，左上角的Adaptation字非常小, 是因为缩放导致的.

用手指在字的位置让其放大后会显示成这样:
![scale](http://ww3.sinaimg.cn/large/744e593bgw1erhtsxqpj4j20cl052dga.jpg)

页面已经超出设备外(15... 30)

meta viewport就是为了解决这个事情而来.
```html
<meta name="viewport" content="width=device-width">
```
viewport是网页默认的宽度和高度，width=device-width意思是网页宽度默认等于屏幕宽度(screen.width)
![viewport](http://ww2.sinaimg.cn/large/744e593bgw1erhttjnp36j20cn06qgm7.jpg)

其他viewport meta参数:
nitial-scale：初始缩放比例，也即是当页面第一次 load 的时候缩放比例
maximum-scale：允许用户缩放到的最大比例
minimum-scale：允许用户缩放到的最小比例
user-scalable：用户是否可以手动缩放

viewport meta开始是由Safari浏览器引入的， 详细了解可以移步:
https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariWebContent/UsingtheViewport/UsingtheViewport.html

这篇博文也非常不错:
http://www.quirksmode.org/mobile/viewports2.html

#### 二.消除在触摸UI上点击延迟可加载FastClick

#### 三.如何让分享的页面在微信上能显示logo
其实提到这，微信官方有正常用法不过那太麻烦，需要服务端支持.如果仅仅是个静态分享页面如何才能显示Logo呢?
```javascript
var img = new Image();
img.src = imgUrl;
img.style.position = 'absolute';
img.style.left = '-1000px';
img.style.top = '-1000px';
document.body.insertBefore(img, document.body.firstChild);
```
上面这段代码其实算是个技巧。

#### 四.new Image()赋src值后，需要在onload后执行相关内容
```javascript
var target = new Image();
target.src = src;
target.onload = function() {
	//...
};
```
没查资料之前，折腾了挺久.想想也是，图片不可能立即就加载完成的，肯定需要回调.

#### 五.调试移动端Web工具:
1.使用Chrome的自带的调试不同分辨率下的一个插件
2.weinre，这个是事后才知道的，很有用的工具

