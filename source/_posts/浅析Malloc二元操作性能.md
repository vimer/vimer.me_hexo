title: 浅析Malloc二元操作性能
date: 2012-07-11 15:11:59
tags: [C,性能]
---

在浅析之前先看下 malloc的原型:
```C
void* malloc(size_t size);
```
这里唯一需要注意到是:

```C
typedef unsigned int size_t;
```
来段测试程序
```C
#include <stdio.h>
int main(int argc, char *argv[])
{
	int i = 0;
	for (; i<100; i++) {
		malloc(32);	/**< 每次malloc 32 字节 */
	}
	return 0;
}
```
编译后,看下内存泄漏:
![内存泄露](http://ww2.sinaimg.cn/large/744e593bgw1emn9ltrbdgj20e103dq3a.jpg)

和预计的没错,正好泄漏32*100 bytes

我们放大循环次数和修改,看下运行时间:
```C
int i = 0;
for (; i<1000000; i++) {
    malloc(1000);	/**< 每次malloc 1000 字节 */
}
```
real  7.366s

我们这次加大循环次数并且malloc后顺便free掉看看结果:
```C
int i = 0;
for (; i<1000000; i++) {
	int* mm = malloc(1000);	/**< 每次malloc 1000 字节 */
	free(mm);
}
```
结果显示如下:
![内存泄露](http://ww4.sinaimg.cn/large/744e593bgw1emn9noz3fej20i802gaa6.jpg)

也和预计的一样,二元操作,内存显示正常,

再来看看和第一次相同的循环(100W)浪费多少时间:

real 0.047s

可以看出,是否可以认为内存泄漏直接了影响性能?,且慢,继续看下面

还是和第一个例子相同,如果我们最后一起free掉所有内存,也就没有内存泄漏了,那看看性能如何?
<!--more-->

```C
int i = 0;
void* szMM[1000000];
for (; i<1000000; i++) {
	szMM[i] = malloc(1000);	/**< 每次malloc 1000 字节 */
}
for (i=0; i<1000000; i++) {
	free(szMM[i]);
}
```
无任何内存泄漏,但是运行时间!!

real 9.690s

时间和第一次差不多长,三次测试结果说明什么?

说明性能和内存泄漏没直接关系,第一和第三都有共同的特点,malloc的内存 每次1000bytes 循环100W 不停的累计.而第二个例子是每次malloc都会free掉.,没有释放的内存的最大数量影响了整个程序的性能!

