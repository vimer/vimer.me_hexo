title: gdb调试golang中的内部运行时信息(如:goroutine)
date: 2015-05-06 19:25:03
tags: [Golang,gdb,goroutine]
---

最近使用Go写crawler，使用gdb后发现不能显示gorountine的信息。

查看了下golang的文档 https://golang.org/doc/gdb : 
>A recent extension mechanism to GDB allows it to load extension scripts for a given binary. The tool chain uses this to extend GDB with a handful of commands to inspect internals of the runtime code (such as goroutines) and to pretty print the built-in map, slice and channel types.
> ...
>If you'd like to see how this works, or want to extend it, take a look at src/runtime/runtime-gdb.py in the Go source distribution. It depends on some special magic types (hash<T,U>) and variables (runtime.m and runtime.g) that the linker (src/cmd/ld/dwarf.c) ensures are described in the DWARF code.

source下/usr/local/go/src/runtime/runtime-gdb.py这个文件就可以了打印运行时一些信息了.

注意:
![info_goroutines](http://ww2.sinaimg.cn/large/744e593bgw1eruqpkm1lkj20ju03374r.jpg)
<!--more-->

如果遇到上面这个错误，  直接下载
https://github.com/sokoide/go-gdb

这个里面的runtime-gdb.py进行替换。

另外提供一个tip, 由于每次source其实非常麻烦，所以gdb支持加载配置
```
gdb -x gdb.config
```
gdb.config 可以加入你想要的语句，如:
```
source /usr/local/go/src/runtime/runtime-gdb.py 
```
