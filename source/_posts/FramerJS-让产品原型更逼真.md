title: Framer.js 让产品原型更逼真
date: 2014-12-02 13:37:34
tags: [CoffeScript, Framer.js, 原型设计, 交互设计]
---
关于Framer.js基本概念可以参考官方网站:
http://framerjs.com/
以及这篇文章:
http://www.ui.cn/project.php?id=21472

Framer.js官方出来一个Framer Studio,但是需要$79.9, 类似PlayGround的功能这个自己写一个就可以了,我需要的是phone各个size的框图.我所做的原型能在这个框内表现出来.
如:
![Framer Studio](http://ww4.sinaimg.cn/large/744e593bgw1emvce56wv7j208y0hft92.jpg)

如果你把Framer Studio导出的Framer框架代码覆盖到你自己的Framer框架下就可以实现了,毕竟Framer是开源的框架,可做任意Hack:
自己Hack过的开源地址为(支持js):
https://github.com/InfoGeeker/ig.Framerjs

当你试过之后,你会发现是framer.generated.js这个文件起的作用.
对于deviceType你可以通过变量的方式传入:
```CoffeeScript
window.Framer.Defaults.DeviceView = {
	"deviceScale" : -1,
	"orientation" : 0,
	"contentScale" : 1,
	"deviceType" : g_device || "iphone-5s-silver"
};
```
下面说下这个Framer.js生成的24PI原型有多高保真。

![24pi原型](http://ww4.sinaimg.cn/large/744e593bgw1emvc1fz8y6g208t0hk1kx.gif)

Framer.js支持CoffeeScript和javascript,别以为需要写代码效率低,其实如果你熟练js,那么上面那个交互原型在< 10分钟就可以完成.

这个原型需要三张背景图
```CoffeeScript
layerIndex = new Layer({width:650, height:1100, image:"./images/24piIndex.png"})
layerInnerA = new Layer({width:650, height:1100, image:"./images/24piInnerA.png"})
layerInnerB = new Layer({width:650, height:1100, image:"./images/24piInnerB.png"})
```
为了开始突显第一张背景图,后面两张直接toBack
```CoffeeScript
layerInnerA.sendToBack()
layerInnerB.sendToBack()
```
开始得时候需要点击中间那个球体来进入二级页面,所以要在球体上加一个遮罩.
```CoffeeScript
layerTouch = new Layer({x:0, y:0, width:300, height:300, opacity:0})
layerTouch.center()
```
这里opacity设置为0,让它隐藏在球体上.
```CoffeeScript 
layerTouch.on Events.Click, ->
	layerIndex.animate
	   properties:
	      opacity:0
	layerInnerA.animate
	   properties:
	      opacity:1
	      curve: "liner"
	layerTurnA.bringToFront()
	layerTurnB.bringToFront()
```
这段是触发遮罩层的代码,点击的时候让首页隐藏,把二级界面toFront.

关于二级界面循环切换需要一点策略.看下左侧按键(右侧同理).
```CoffeeScript
turnA = true
time = 0.25
layerTurnA.on Events.Click, ->
        if turnA is true
				#....
                turnA = false
        else
				#...
                turnA = true
```
上面的代码中是个大致切换框架,省略号部分为逻辑代码.
通过turnA这个变量来循环切换二级页面.

别看写得这么啰嗦,其实相比于Axure等原型制作软件效率更高.当然最后说一句:不要为了用工具而用工具,应该以效率和Goal为选择工具的标准.

总结一句: Framer.js在产品原型还原度上是接近100%的.

