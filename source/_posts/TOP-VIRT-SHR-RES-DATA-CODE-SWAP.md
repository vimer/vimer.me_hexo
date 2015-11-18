title: "TOP: VIRT, SHR, RES, DATA, CODE, SWAP"
date: 2012-07-8 00:30:06
tags: [linux,性能,内存]
---
写服务端程序,对这几个值深入认识很重要,网上也有很多帖子讲这个概念的,但是大多都是出自几个作者的名下的 转载. 说的也比较笼统, 比如RES=DATA+CODE 但是很多实际情况并不是这样.

从下面的程序来分析这个值的真正含义.
```C
int main(void) {
	while (1) {
		;
	}
	return 0;
}
```
运行这个程序后来看看这几个值情况:
![Top](http://ww4.sinaimg.cn/large/744e593bgw1emmk73ifzqj20jb01eglr.jpg)

VIRT  是这个进程最大能够占用的内存空间,是个额定值,虚拟的!
RES  为进程占用的实际内存的大小, 也就是在内存条中占用的大小(物理内存),当然不包含被置换的大小
%MEM 是进程占用的物理内存的百分比
SHR  是共享内存大小, 也是额定的 表示这个程序可能被其它程序共享到的大小
CODE 进程的代码段在实际内存中占用的空间大小
DATA 是程序运行时需要使用的数据空间(数据+栈),
SWAP 是交换分区的大小,被置换的!

从上面的图可以得出

VIRT(4000) = RES(280) + SWAP(3720)

但是
RES(280) != CODE(4) + DATA(188)
这是为什么呢？ 试试下面这段代码：

```C
#include <stdio.h>
int main(int argc, const char *argv[])
{
	char* a = (char*)malloc(104857600);
	/*memset(a, 0, 104857600);*/
	while (1) ;
	return 0;
}
```
实验发现:
data也是额定的 也就是说 当你malloc时就会显示的大小 ,而RES值不会进行变化， 但是当向malloc的这段内存中进行 memset 或者写数据时 RES 才会趋向于 data+code

最后附上一个详细的Top使用说明:
http://www.thegeekstuff.com/2010/01/15-practical-unix-linux-top-command-examples/

(PS:2012年自己写得一篇文章, 保留在新博客)

