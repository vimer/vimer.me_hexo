title: "玩转Framer.js, 分享tips"
date: 2015-03-29 16:31:11
tags: [Framer.js,原型设计]
---
前面有一篇博文大致介绍了[Framer.js](http://vimer.me/articles/FramerJS-%E8%AE%A9%E4%BA%A7%E5%93%81%E5%8E%9F%E5%9E%8B%E6%9B%B4%E9%80%BC%E7%9C%9F.html):

这篇主要分享一些tips,对于玩转Framer.js其实还是需要费些折腾的,尤其想表达些更好的效果的时候.

![shouldHint](http://ww1.sinaimg.cn/large/744e593bgw1eqmovg0etjg208g0d7qao.gif)

![swipeHint](http://ww2.sinaimg.cn/large/744e593bgw1eqmovqsqo9g208g0d7wjs.gif)

![clieckEvents](http://ww4.sinaimg.cn/large/744e593bgw1eqnx6kw7obg20840ekn1h.gif)

另外会间断补充些需要注意的地方:
1.在导入ps图层时间,同一层分组不重名很正常,但是可惜的是Framer不支持树形tree文件目录结构,也就是说你所有的分组名必须是一样的，不然只能操作第一个模块

2.借用第一条,其实如果重名也没关系,关键让操作的模块能唯一就好

3.分组名(应该说模块名)及文件名避免用中文及特殊字符, 对中文支持不友好


...
每个图片名就是函数名,具体代码参考下面的源码.

开源地址(不间断更新):
https://github.com/InfoGeeker/Framerjs-tips 


