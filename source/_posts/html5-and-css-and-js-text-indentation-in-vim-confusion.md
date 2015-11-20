title: 基于Vim编写Html5的插件推荐及解决混排缩进
date: 2015-06-02 19:10:01
tags: [vim]
---
<pre>
<code class="js">
* html5.vim:
 	git@github.com:othree/html5.vim.git

* vim-javascript:
 	git@github.com:pangloss/vim-javascript.git

* vim-matchit:
 	git@github.com:edsono/vim-matchit.git

* vim-css-color:
 	git@github.com:ap/vim-css-color.git
</code>
</pre>

上面是我推荐的四款插件，但是这四个插件整合在vim插件中会有个混排缩进问题。

<!--more-->

![整齐代码](http://ww1.sinaimg.cn/large/744e593bgw1espz3wu3nxj209i0bv3ze.jpg)

> * html和js混排的时候js排版总是会出问题
* html和css混拍的时候css排版会出问题

gg=G后整个是一团糟

![混排问题](http://ww3.sinaimg.cn/large/744e593bgw1espxjkvjzjj20a10bx754.jpg)

下面是两个我修改过的版本，目前测试正常.

<pre>
<code class="js">
git@github.com:vimer/html5.vim.git

git@github.com:vimer/vim-javascript.git
</code>
</pre>



