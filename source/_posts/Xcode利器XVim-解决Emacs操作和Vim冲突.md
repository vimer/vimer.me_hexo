title: "Xcode利器XVim, 解决Emacs操作和Vim冲突"
date: 2015-03-31 15:33:21
tags: [Xcode,vim,emacs,iOS]
---
Xcode结合Vim操作推荐使用XVim这个插件:
https://github.com/XVimProject/XVim (插件安装建议直接安装Alcatraz,再通过Alcatraz来安装其他插件)
![Xvim](http://ww1.sinaimg.cn/large/744e593bgw1eqoybjivlyg20kp03o44h.gif)

如果你习惯Emacs的快捷键,如C-e, 那么两者结合才是王道, 在使用了Xvim后, 可能C-e就冲突了. 

解决这个有两种途径, 一种是使用直接改源码(费时), 还有一种是使用类似.vimrc的.xvimrc. 语法是vim script,比如刚才:

```javascript
:imap <c-e> <esc>A
```

就可以完美解决这个问题 :)
<!--more-->
