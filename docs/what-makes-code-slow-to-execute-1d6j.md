# 是什么导致代码执行缓慢

> 原文：<https://dev.to/taowen/what-makes-code-slow-to-execute-1d6j>

“光锥内是命运”——刘慈欣

性能主要由代码的算法决定。这是毫无疑问的。有些用 python 写的算法比用 c 写的要慢很多，对我来说，这也是算法问题，如果你把代码和它的语言作为一个整体来考虑的话。在 python 中做同样的事情比 c 语言需要更多的机器指令。机器指令序列是一种算法。

x86 指令可以被视为 CPU 内部使用的微代码的高级语言。当 CPU 将 x86 指令解码为带有推测性执行的微代码时，会导致显著的性能差异。这也可以归类为一个(增加并发的)算法问题，也不是我在这里要讲的。

除了算法，还有什么能使代码在机器上执行缓慢？答案是“**数据局部性**”，读写数据进行计算需要时间。有很多这样的例子

*   CPU 缓存
*   GPU 架构
*   分布式映射/还原
*   堆管理

走过这些例子，我们可以看到为什么**异构计算**是不可避免的，主流编程语言中的编程模型是过度简化的。

# CPU 缓存

考虑这个例子[https://stack overflow . com/questions/9936132/why-the-order-of-the-loops-affect-when-iterating-a-2d-arra](https://stackoverflow.com/questions/9936132/why-does-the-order-of-the-loops-affect-performance-when-iterating-over-a-2d-arra)

逐列迭代矩阵

```
#include <stdio.h>
#include <stdlib.h> 
main () {
  int i,j;
  static int x[4000][4000];
  for (i = 0; i < 4000; i++) {
    for (j = 0; j < 4000; j++) {
      x[j][i] = i + j; }
  }
} 
```

逐行迭代矩阵

```
#include <stdio.h>
#include <stdlib.h> 
main () {
  int i,j;
  static int x[4000][4000];
  for (j = 0; j < 4000; j++) {
     for (i = 0; i < 4000; i++) {
       x[j][i] = i + j; }
   }
} 
```

行方式比列方式快 3 到 10 倍。当连续写入存储器地址时，可以将写入合并到单个请求中，以节省往返时间。

[![image](../Images/37cafd3a5f0e6748bbbac372eea8582c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ap-MsYM_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/40541/46247871-3fae2e00-c444-11e8-9d39-550623ad4bc0.png)

据[https://en.wikipedia.org/wiki/CPU_cache](https://en.wikipedia.org/wiki/CPU_cache)报道，随着 x86 微处理器在 386 中达到 20 MHz 及以上的时钟频率，少量的快速高速缓存开始在系统中使用以提高性能。处理器设计最大的问题是内存跟不上处理器的速度。

[![image](../Images/4d970573f239b9a0750fa2f3b8e4f4af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GWbZoEl1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/40541/46323686-0a5f3700-c622-11e8-8759-12710e920251.png)

我们可以看到上面的图表，现代 CPU 每秒钟可以处理大量的数字。保持 cpu 忙碌的理论数据传输速率远远高于当前主存所能提供的带宽。

CPU 和主内存之间的延迟最终取决于光速。我们必须让内存更靠近 CPU，以降低延迟。但是片上内存(L1/L2/L3 缓存)不能太大，否则电路会很长，延迟会很高。

# GPU 架构

在 CPU 的设计中，缓存是不可见的，内存是一致的。您不需要关心所有套接字的缓存和内存之间的同步。尽管您确实需要使用内存屏障来声明一个内存地址上的动作序列，就好像它们直接在内存本身上竞争一样。为了实现这一点，CPU 需要高速环/网格来同步套接字之间的缓存。

[![image](../Images/0a122b85ec52ebb0909d80d5af073ca0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---B302QD7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/40541/46253034-bbd85e00-c4a4-11e8-82ae-575f770937f2.png)

这种编程模式会阻止内核增长。因为数据传输的延迟是不可避免的，所以维持内核之间直接共享内存的假象成本太高。需要向所有高速缓存广播存储器改变。

GPU 架构不一样。它通过明确“缓存”来解决内存延迟问题。而不是称之为“缓存”，它只是你可以使用的另一层内存。

[![image](../Images/faeb58d3c33eca5a4d3af4325b5b8e1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9mKd5WuO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/40541/46254299-c3f1c700-c4bf-11e8-8f24-266ee57d7f2b.png)

在这种编程模型中，不只有一个内存“堆”，相反，变量需要明确其作用域。例如

```
__global__ void parallel_shared_reduce_kernel(float *d_out, float* d_in){
    int myID = threadIdx.x + blockIdx.x * blockDim.x;
    int tid = threadIdx.x;
    extern __shared__ float sdata[];
    sdata[tid] = d_in[myID];
    __syncthreads();

    //divide threads into two parts according to threadID, and add the right part to the left one, 
    //lead to reducing half elements, called an iteration; iterate until left only one element
    for(unsigned int s = blockDim.x / 2 ; s>0; s>>=1){
        if(tid<s){
            sdata[tid] += sdata[tid + s];
        }
        __syncthreads(); //ensure all adds at one iteration are done
    }
    if (tid == 0){
        d_out[blockIdx.x] = sdata[myId];
    }
} 
```

变量要么没有前缀，要么有`__shared__`或`__global__`。在 CPU 编程中，我们需要猜测缓存是如何工作的。相反，我们可以在 GPU 编程中直接询问。

# 分布式映射/还原

Map/Reduce 可以处理分布式大数据，因为它避免了在数据原来所在的节点上进行计算的数据传输

[![image](../Images/0294a67ba9b6999a7eeb718a3215e64a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eY1iE0gE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/40541/46254553-b8080400-c4c3-11e8-9ad2-46b2ff7c5a13.png)

从“映射”节点发送到“缩减”节点的数据比原始输入小得多，因为已经完成了部分计算。

# 堆管理

大堆很难管理。在现代 c++或 rust 中，我们使用所有权来管理内存。本质上，不是使用垃圾收集器将整个堆作为一个连接的对象图来管理，而是将堆分成更小的范围。例如

```
class widget {  
private:  
  gadget g;   // lifetime automatically tied to enclosing object 
public:  
  void draw();  
};  

void functionUsingWidget () {  
  widget w;   // lifetime automatically tied to enclosing scope 
              // constructs w, including the w.g gadget member 
  …  
  w.draw();  
  …  
} // automatic destruction and deallocation for w and w.g 
  // automatic exception safety, 
  // as if "finally { w.dispose(); w.g.dispose(); }" 
```

在很多情况下，一个内存地址的生命周期与函数的进入/退出和对象的构造/销毁是一致的。有些情况下，需要对生存期进行显式标注

```
fn substr<'a>(s: &'a str, until: u32) -> &'a str; 
```

像`'a`这样的终身声明和 CUDA 中的`__shared__`类似。像 c++和 rust 这样的显式生命周期管理是一个极端。多线程安全的垃圾回收语言像 Java 和 Go 是另一个极端。像 Nim 这样的语言通过线程来划分内存，处于中间位置。为了确保不同分区中的内存不会意外共享，需要在跨越边界时复制内存。同样，最小化内存复制成本是一个数据局部性问题。

JVM 有垃圾收集功能。它不是静态地注释源代码，而是试图通过将大堆划分为粗略的几代来找出运行时的情况:

[![image](../Images/f0bf9adaebedaac170f839b6af184528.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gl4b81P2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/40541/46323083-14cc0180-c61f-11e8-8870-4f51dec4f681.png)

如果我们一开始就不共享大堆，那么处理堆管理问题会容易得多。在某些情况下，多处理器分片内存是有益的([https://en.wikipedia.org/wiki/Symmetric_multiprocessing](https://en.wikipedia.org/wiki/Symmetric_multiprocessing))。但是在很多情况下，将数据和计算放在一起(actor 编程模型)会更容易。

# 协同定位数据和计算

每时钟指令增长不是很快。

[![image](../Images/11a0b1110ef778772fca3da64925d772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---dh7W85z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/40541/46322713-13013e80-c61d-11e8-9937-a34c1909b393.png)

主存储器访问延迟几乎是平坦的

[![image](../Images/34093a2a43932464ad2438fa1b4512ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_8TpIlk5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/40541/46322909-1d700800-c61e-11e8-95f5-c07549e7e67c.png)

必须处理一个以上的处理器，每个处理器都有自己的内存(异构计算)可能是普遍的。一个明显的趋势是，我们需要确保数据和计算在这种范式中彼此共处一地。问题是如何正确地划分数据和计算，以最大限度地减少数据复制或消息传递。

* * *