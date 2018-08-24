title: nohup和SIGHUP信号
date: 2014-12-26 11:30:46
tags: [nohup,SIGHUP]
categories: 技术
---
为什么这篇文章会提到nohup呢,主要还是最近在使用了一个叫maptail东西,它可以根据nginx的日志中IP来把IP和具体地址进行转换并显示在中国地图上.这样可以实时的查看接口的调用情况.

<!--more-->
![24pi_maptail](https://img.emacs.cn/20180824111120.jpg)

这里不讨论maptail,主要说得是maptail启用方式: 
<pre>
<code class="bash">
tail -f nohup.out | maptail -h my.host.com -p 3000 > /dev/null & 
</code>
</pre>

这条命令运行了一段时间后,进程就总是不响应了,但是进程还在.

不禁让我们对nohup和SIGHUP做了个实验.(自己写博可能有些跳跃,不明白可以直接回复:)

<pre>
<code class="bash">
tail -f 1 &
</code>
</pre>

这样放入后台后,通过进程树可以看到

![tail进程树](https://img.emacs.cn/20180824111151.jpg)
这个tail进程挂接在bash父进程上.

我们exit退出当前shell.再次进去看下:
![tail进程树](https://img.emacs.cn/20180824111206.jpg)
可见它被init进程来接管.

也就是这个进程运行正常,没什么问题.

那么nohup起了什么作用呢 ? 

如果nohup起守护进程的作用,那么刚才这个进程也能正常运行于后台.

我们再用nohup来运行这个进程看看
<pre>
<code class="bash">
nohup tail -f 2 &
</code>
</pre>

按照上述操作,tail -f 2也同样跑在后台中.

nohup主要的作用是可以忽略SIGHUP信号,我们对上面两个进程分别进行发出kill -HUP 信号.

tail -f 2成功忽略了SIGHUP信号,而tail -f 1被成功杀死, 那么SIGHUP信号什么时候发出呢.

做了一些测试,关闭终端是不能发送SIGHUP给进程的.

当终端正常连接过程, 把笔记本合上, 过一段时间后, 发现tail -f 1被杀死了, 也就是说SIGHUP是终端连接断开时才发出的. 正常逻辑是: 当终端断开时,SIGHUP信号首先发给shell所在进程,而shell收到后,会向所有进程组发送SIGHUP信号,假设没有任何捕捉此信号，那么受到SIGHUP信号的进程就会关闭了, 而nohup守护的进程就不会.

当然如果遇到没有nohup进程已经运行,但是想把此进程离开当前进程组比如bash，可以借用distown这个命令.

