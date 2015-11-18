title: 'iOS UI效率利器: Auto Layout'
date: 2015-11-16 14:44:27
tags: [iOS, Autolayout]
---

&nbsp;&nbsp;遇到几个iOS开发者，都说用手写代码进行UI布局，理由是代码灵活，有很高的重用性，另外还适合装逼，而且也会顺便鄙视下用Stroryboard、xib等布局的，我这几个都尝试了下，各有好坏，手写代码如果不是一个熟手（没有重用的代码库）那么布局一个新的UI速度其实挺慢的，且在布局过程中你是无法直观的看到UI结构，这些缺点正是Stroyboard, xib的优点，所以适当时候选择Stroyboard、xib对开发效率提升是很大的。

&nbsp;&nbsp;尤其在iOS6之后推出了一个新的解决方案Autolayout，适当的添加约束可以在不同设备上进行适配。

&nbsp;&nbsp;下面我针对Autolayout说下具体使用,打开一个新工程后拖动一个Text Field在窗体上，如下，这个时候窗体并不一定是一个竖放得iPhone或者是iPad，你可以把它看成一个容器，它可以是横排的iPhone，竖排的iPhone，iPad。

<!--more-->

<img src="http://ww3.sinaimg.cn/large/744e593bgw1ey2tcrs1xsj20km0iywen.jpg" width="600px" height="500px" />

我们看下在5s上的运行效果，发现Text Field只出现了部分在5s上，那个Geeker其实是在屏幕外面，由于我们没有做任何措施，这个容器实际长度比5s要长，所以Text Field只显示了部分。

![](http://ww1.sinaimg.cn/large/744e593bgw1ey2tg3sovoj20l90jkq3j.jpg)

现在我要让5s显示全部，就要限制他在不同的容器内自动伸缩，那么就要设置他的约束，最容易想到的是 Text Field离左右两边的约束。我们来看下效果:

![](http://ww2.sinaimg.cn/large/744e593bgw1ey2tgisrdgj20ib05p0sv.jpg)

![](http://ww1.sinaimg.cn/large/744e593bgw1ey2tgr59iij20at04xmx9.jpg)

可以看到图中有约束线是红色标出，点击左边的圆圈可以看出具体问题，我们只设置了X轴方向的约束，但是没有设置Y轴上的约束，一种方面是点击红色的圈 来Add Missing Constraints， 还有一种是 按照刚才的方法来设置距离顶部的Space。

![](http://ww3.sinaimg.cn/large/744e593bgw1ey2th4bat7j209k04nq37.jpg)

设置好后，出现蓝色的线，说明暂时没问题了，对，只是暂时。

![](http://ww4.sinaimg.cn/large/744e593bgw1ey2thgi8xzj20hy06z3yk.jpg)

我们来看下5s的运行效果, 看起来不错。

![](http://ww3.sinaimg.cn/large/744e593bgw1ey2tja6tgxj20hr07oglz.jpg)

继续折腾, 在Text Field 下面添加一个Label  和 TextField

![](http://ww2.sinaimg.cn/large/744e593bgw1ey2trpli6ej20hy06n0sr.jpg)

运行效果不用说，根据之前描述，肯定又会超出5s容器之外， 我们继续为它添加约束。

![](http://ww1.sinaimg.cn/large/744e593bgw1ey2tk0q4boj20hk07g74e.jpg)

分别为Label 和 Text Field 添加了 左，上，右的约束 ，运行效果正常。

![](http://ww4.sinaimg.cn/large/744e593bgw1ey2tkszzjij20i30ajaak.jpg)

继续折腾，建立一个大小为64*64的Label, 文字居中.

![](http://ww3.sinaimg.cn/large/744e593bgw1ey2tl3kizdj20c609at8q.jpg)

拷贝现有的label, 进行排列操作，在移动过程中出现较粗的线为准

![](http://ww2.sinaimg.cn/large/744e593bgw1ey2tlxrbqhj205m05y3yg.jpg)
![](http://ww1.sinaimg.cn/large/744e593bgw1ey2tm74zcqj204205gjr8.jpg)
![](http://ww4.sinaimg.cn/large/744e593bgw1ey2tme6rewj20b606qq3b.jpg)

我们先不设置约束，看看效果

![](http://ww1.sinaimg.cn/large/744e593bgw1ey2tmplmo6j20mn0fi75h.jpg)

![](http://ww4.sinaimg.cn/large/744e593bgw1ey2tmyul1yj20os0e7q4r.jpg)

可以看见，在竖屏情况下，水平状态下只显示3列，而横屏情况下，垂直状态下只显示2行 ，现在想办法让他自动撑满，并且显示正常，首先要借助两根辅助线（上和左）

![](http://ww2.sinaimg.cn/large/744e593bgw1ey2tn9tsrgj20dz0b2aaq.jpg)

然后通过给label设置他们都是等宽和等高，上下左右margin都是相等的。

![](http://ww4.sinaimg.cn/large/744e593bgw1ey2to6hr0ej20840bjt9v.jpg)

![](http://ww3.sinaimg.cn/large/744e593bgw1ey2toe9po3j20i30fln09.jpg)

增加约束后，会发现有几个严重的权重约束问题

![](http://ww1.sinaimg.cn/large/744e593bgw1ey2toovj8gj20ad04xt97.jpg)

设置下Text Field的宽和高就可以了，再对其它出现的黄色警告进行fix

![](http://ww4.sinaimg.cn/large/744e593bgw1ey2toykj4fj20dp08m0uf.jpg)

下面是fix后的图

![](http://ww4.sinaimg.cn/large/744e593bgw1ey2tp7ptqaj20i30iiq3d.jpg)

我们看下效果，发现Name不见了，看来是设置了宽度后把Name挡住了

![](http://ww3.sinaimg.cn/large/744e593bgw1ey2tphogzej20pf0fr0tj.jpg)

我们可以设置Width<=一定数目，让它自动伸缩

![](http://ww4.sinaimg.cn/large/744e593bgw1ey2tpq88epj20bx022mx6.jpg)

最后我们在5s上看下效果:

###横屏

![](http://ww1.sinaimg.cn/large/744e593bgw1ey2tq1r7x8j20w50j5di8.jpg)

###竖屏
![](http://ww2.sinaimg.cn/large/744e593bgw1ey2tqe2ne8j20re0j075s.jpg)

你会发现，如果熟练的话，只要几分钟之内布局一个页面。





