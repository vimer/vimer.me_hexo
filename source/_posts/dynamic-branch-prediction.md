title: 动态分支预测
date: 2015-05-10 12:24:29
tags: [cpu, 性能]
---

说起这个话题也挺有意思，上周五我在公司技术分享的时候，在最末提到一个问题: 

为什么排序过的数据在遍历的时候速度更快 ?

这里我拿出很久之前在Stack Overflow上火车在行驶的过程中的例子

<!--more-->

![](http://ww4.sinaimg.cn/large/744e593bgw1erz071og68j21520jp7b8.jpg)

![](http://ww2.sinaimg.cn/large/744e593bgw1erz08s1yjaj21540ih7bj.jpg)

![](http://ww2.sinaimg.cn/large/744e593bgw1erz08s1yjaj21540ih7bj.jpg)

![](http://ww3.sinaimg.cn/large/744e593bgw1erz0a5681wj215a0mh7dc.jpg)

![](http://ww2.sinaimg.cn/large/744e593bgw1erz0bo194hj214y0j7tel.jpg)

![](http://ww2.sinaimg.cn/large/744e593bgw1erz0dxmakaj214r0lun4t.jpg)

这里就有伙伴提出问题了，既然靠猜，火车如果发现目的地不是预期的，那么就说明猜错了，但是CPU怎么知道猜错了呢？ 这里就涉及到cpu的流水线.

比如一个分支代码: 
<pre>
<code class="C">
if (data[j] >= 128) {
	sum += data[j];
}
</code>
</pre>

从高级语言层来看，这段代码是只能顺序执行的，必须先检测条件，才能执行条件对应的语句. 但是从CPU角度来看，却不是这样，CPU会把这几条语句分成多条指令，取个简单例子:

![](http://ww1.sinaimg.cn/large/744e593bgw1erz14fsgxmj210b0cltc0.jpg)

为了提高CPU中存储器、译码等的效率，CPU可采取流水线方式，上面两条指令假设一个是判断指令，一个是预测指令。那么当判断指令取指时，预测指令什么都还没做，当判断指令译码时，预测指令才开始取指，当判断指令执行时预测指令开始译码，这个时候，判断指令要么true，要么false，如果true 说明预测的指令是对的，这样效率会很明显提高，反之如果是false，那么就要重新进行，并把 目标地址 缓存到一个存储器中。一直循环到最后，这样就很容易解释为什么排序过的程序遍历会更快了.


补充下测试代码:
<pre>
<code class="C++">
int main()
{
        const unsigned arraySize = 32768;
        int data[arraySize];
        for (unsigned i = 0; i < arraySize; i++) {
                data[i] = std::rand() % 256;
        }
        //std::sort(data, data + arraySize);
        clock_t start = clock();
        long long sum = 0;
        for (unsigned i = 0; i < 100000; i++) {
                for (unsigned j = 0; j < arraySize; j++) {
                        if (data[j] >= 128) {
                                sum += data[j];
                        }
                }
        }
        double elapsedTime = static_cast<double>(clock() - start) / CLOCKS_PER_SEC;
        std::cout << "time = "<< elapsedTime << std::endl;
        std::cout << "sum = " << sum << std::endl;
}
</code>
</pre>

在不sort的情况下和sort情况下可分别测试下。
