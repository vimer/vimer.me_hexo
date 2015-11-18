title: vim开发HTML5插件及解决混排缩进问题
date: 2015-06-02 19:10:01
tags: [vim]
---
首先推荐的插件是:

* html5.vim:
 	git@github.com:othree/html5.vim.git

* vim-javascript:
 	git@github.com:pangloss/vim-javascript.git

* vim-matchit:
 	git@github.com:edsono/vim-matchit.git

* vim-css-color:
 	git@github.com:ap/vim-css-color.git

<!--more-->

使用上面插件后，html、js、css混排排缩一直是个问题，比如下面三种语言混排的代码段，非常整齐。
![整齐代码](http://ww1.sinaimg.cn/large/744e593bgw1espz3wu3nxj209i0bv3ze.jpg)

第一个是html和js混排的时候js排版总是会出问题，还有个是html和css混拍的时候css排版会出问题，使用gg=G后整个是一团糟

![混排问题](http://ww3.sinaimg.cn/large/744e593bgw1espxjkvjzjj20a10bx754.jpg)

下面是两个修改过的版本，目前测试很正常.

git@github.com:InfoGeeker/html5.vim.git

git@github.com:lepture/vim-javascript.git



