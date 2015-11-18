title: 说说Objective-C的id及动态绑定
date: 2015-04-05 00:14:35
tags: [Objective-C, iOS]
---

OC动态绑定很重要的一点在于 OC中有一个非常重要的类型叫 id, id是一个指针, 指向类未知的对象, 指向未知类型或未指定的类型. 实际上OC中的所有指针都是id, 当你将消息发给一个对象时, 具体执行什么代码直到运行时才会决定. 这被叫做动态绑定(消息机制).

这个特性和其他语言是不一样的, 使用消息结构的语言,其运行时所应执行的代码由运行环境所决定, 而使用函数调用的语言(C++)则由编译器决定, 当然你会想到, 如果只在运行时检测, 那么是否会有安全问题? 
其实不用担心, 因为大部分写程序的时候都是用静态类型. 少部分时要进行些保护性措施, 这个后面会说.

比方使用静态类型化:
NSString *s = @“x”; 编译器编译的时候就会检查,如果指向非NSString会给出警告(这也提醒我们要好好对待编译器的警告)
id obj = s; id obj是指向任何对象的指针,所以不会产生警告

这种情况在日常写代码中也会随处遇到, 比如NSArray的方法firstObject的类型就是id: 
```OBJECTIVE-C
@property(nonatomic, readonly) id firstObject
```
所以这种时候, 应该加些保护措施. 否则很容易crash !

<!--more-->
另外Stanford slide上的一个Demo:

```OBJECTIVE-C
@interface Vehicle : NSObject
-(void)move;
@end
@interface Ship : Vehicle
-(void)shoot;
@end
```

```
Ship* s = [[Ship alloc] init];
Vehicle* v = s;
[v shoot];
id obj = v;
[obj shoot]; 
```
其中:
[v shoot] //编译错误, 这个在Slide中只是个警告, 估计是编译器版本问题
[obj shoot]   //编译、运行都正常

这个Demo其实很好的诠释了id这个类型的特殊性 .
* Ship *s = [Ship alloc] init];创建了s,Vehicle* v = s; s仍然是Ship类型，同时v指向s，v虽然是Vehicle指针，但实际在内存中仍然是Ship类型
* id obj = v; obj指向v，v指向s，所以obj实际是指向s的，所以obj是能响应shoot函数的
* v 的指针指向Ship那块内存，[v shoot]由于受到类型的保护 所以报错， obj由于是id类型所以一切正常.

可以输出刚才三者指向的地址, 都是指向s所alloc的内存区域的.

![指针指向](http://ww1.sinaimg.cn/large/744e593bgw1equ157ft7vj20lj0bsaap.jpg)

继续看下去:
```OBJECTIVE-C
NSString* hello = @"hello";
Ship* helloShip = (Ship *)hello;
[helloShip shoot];
```
更通俗点解释是: 告诉编译器NSString就是Ship(只是把编译器糊弄了) 所以编译正常, 第三行在运行时,由于那个地址没有还是为@"hello", 当真正去shoot时, 它会分派那个shoot, 结果发送到的地方是个字符串. 导致crash.

同理, 下面这段代码可以再体会下:
```OBJECTIVE-C
NSString* hello = @"hello";
[hello shoot];
[(id)hello shoot]; 
```

那么，说到这都是在说动态绑定的一些"坑", 那么动态绑定的优势是什么呢?

此文未完.

