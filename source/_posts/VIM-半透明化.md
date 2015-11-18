title: vim 半透明化
date: 2012-07-16 22:33:25
tags: [vim]
---
经常有人询问怎么使vim透明化:

先来张图片:

![vim半透明](http://ww2.sinaimg.cn/large/744e593bgw1emnm9ukiwrj20gg0ckjs9.jpg)

半透明化后的vim效果还是不错的, 尤其是直接可以看到背后程序的结果 不用来回切换了.

把下面的Vim script加入 .vimrc即可 ,不过你要先在网上下载vimtweak.dll这个插件(Linux的话就不需要插件了, 直接使用自带的alpha效果就可以了).

```vimscript
;;自动透明
au GUIEnter * call libcallnr("vimtweak.dll", "SetAlpha", 234)
```

```vimscript
;;几个更改alpha的快捷键
map <M-8> <Esc>:call libcallnr("vimtweak.dll", "SetAlpha", 180) <CR>
map <M-9> <Esc>:call libcallnr("vimtweak.dll", "SetAlpha", 230) <CR>
map <M-0> <ESC>:call libcallnr("vimtweak.dll", "SetAlpha", 255) <CR>
```

.source .vimc后就可以用了. 默认是自动透明的 后面的数字可以随变你改.

(linux用户系统直接支持半透明)

