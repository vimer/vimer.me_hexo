title: 由 C 中字符串比较引入的问题
date: 2012-07-19 22:27:45
tags: [C,字符串]
categories: 技术
---
一个同事在Project中写下如下错误代码(为了说明问题, 代码已经简写)

<pre>
<code class="C">
void function(const char* _str) {
	if (_str == "OK") {

	}
}
</code>
</pre>

这段代码 错就错在把char*(从堆里分配的)字符串直接 常量字符串进行比较.
而这位同事不解的拿出下面这个例子对我发出这样的疑问:为什么会这样?

<!--more-->

<pre>
<code class="C">
#include < stdio.h>
int main(int argc, const char** argv)
{
	char str_1[6] = "Crazy1";
	char* str_2 = "Crazy2";
	if (str_1 == "Crazy1") {
		printf("%s\n", "字符数组OK");
	}
	if (str_2 == "Crazy2") {
		printf("%s\n", "字符串常量OK");
	}

	return 0;
}
</code>
</pre>

输出结果: 字符串常量OK

大部份人认为只有C++的String类才能执行等于比较符号操作, 而C中需要使用strcmp来进行”字符串”比较, 为什么这段程序告诉我们字符串常量是可以的, 而字符数组是不可以的. 这里其实主要是字符数组和字符串常量的区别,至于它们本质区别就不多说了, 比如一个存储在静态存储区, 一个在栈上分配空间…

这里主要要知道 str_2是指针, 指向”Crazy2″这个字符串常量的内存首地址,  而str_1是在栈里分配的字符数组”Crazy1″的首地址.

我们更深一步来解释这个问题:

咱们反汇编这段代码:

<pre>
<code class="C">
.section .rodata
.LC0:
.string "Crazy2"
.LC1:
.string "OK"
.text
.globl main
.type main, @function
main:
pushl %ebp
movl %esp, %ebp
andl $-16, %esp
subl $32, %esp
movl $2053206595, 22(%esp)
movw $12665, 26(%esp)
movl $.LC0, 28(%esp)
cmpl $.LC0, 28(%esp)
jne .L2
movl $.LC1, (%esp)
call puts
.L2:
movl $0, %eax
leave
ret
.size main, .-main
.ident "GCC: (GNU) 4.5.1 20100924 (Red Hat 4.5.1-4)"
.section .note.GNU-stack,"",@progbits
</code>
</pre>

咱们根据这段汇编代码可以看出 字符串常量是在编译时就确定的, 而字符数组是在运行时确定的.

<pre>
<code class="C">
movl $.LC0, 28(%esp)
cmpl $.LC0, 28(%esp)
</code>
</pre>

这2句可以直接看出 字符串常量的比较是怎么运算的.

而字符数组为什么不行呢? 很显然 因为他们不属于同一个内存空间.

> 建议: C字符串比较都使用strcmp


