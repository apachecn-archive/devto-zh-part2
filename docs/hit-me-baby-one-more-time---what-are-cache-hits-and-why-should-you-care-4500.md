# 再打我一次宝贝-什么是缓存命中，为什么你应该关心？

> 原文：<https://dev.to/frosnerd/hit-me-baby-one-more-time---what-are-cache-hits-and-why-should-you-care-4500>

# 动机

当讨论算法性能时，我们通常会考虑复杂性。尤其是在比较不同的算法时，查看[渐近复杂度](https://en.wikipedia.org/wiki/Asymptotic_computational_complexity)(例如 big-O 符号)非常有用。然而，我们必须记住，除了最大的复杂性因素，big-O 符号“吞噬”了一切。big-O 符号可能产生误导的一个突出例子是在集合中查找值。哈希表是这个用例的默认候选，因为访问一个特定的元素需要恒定的时间。但是，如果只有几个元素，使用树或者简单的列表可能会更快。

虽然您应该注意这种情况，但是还有另一个影响性能的重要因素:计算机硬件/架构。您的 CPU 可能很快，但它必须等待 I/O 完成。您的分布式算法可能很强大，但您的网络拓扑还不够。在这篇博文中，我们将着眼于计算机架构中可能影响算法性能数量级的一个特定部分:特别是*内存层次*和 *CPU 缓存*。

该职位的结构如下。首先，我们将看一个非常流行的例子，其中运行时性能受到 CPU 缓存利用率的严重影响。第二部分将给出一些关于计算机体系结构的理论背景，让读者理解在这个例子中发生了什么。之后，我们会用新学到的知识重温第一节的例子。我们通过总结主要观点来结束这篇博文。

# 矩阵乘法示例

## 公式

我们要用的例子是一个非常基础的:矩阵乘法。矩阵乘法被用在很多地方，例如图像处理、人工智能、机器人、数据压缩。矩阵 *A* * *B* = *C* 相乘的公式如下:

[![multiplication formula](img/1e45054c9d783a573fe4f089edbd5151.png)T2】](https://wikimedia.org/api/rest_v1/media/math/render/svg/34cd5ccb936a1e163d99a8221d8f178be40012d7)

对于结果矩阵的每个元素，它按行通过 *A* ，按列通过 *B* ，将每对元素相乘，将结果相加。公式还有一个很好的可视化表示:

[![multiplication scheme](img/96127c305ef37a24000e1c73aed3faa3.png)T2】](https://commons.wikimedia.org/wiki/File:Matrix_multiplication_diagram_2.svg)

现在让我们来看看这个算法的两个不同的变体，并测量不同矩阵大小的执行时间。为了简单起见，我们使用两个方阵。它们由随机生成的双精度值填充。

我们使用 [ScalaMeter](http://scalameter.github.io/) 来测量运行时性能。请随意查看我的[上一篇博文](https://dev.to/frosnerd/microbenchmarking-your-scala-code-4885)，其中包含了关于该工具的更多细节。

## 实验

### 算法 1

第一种算法是上述公式的简单实现。对于两个大小为 *n* 的方阵，它必须执行 *n* 次乘法和加法，因此具有 *O(n )* 的复杂度。

```
def mult1(m1: Array[Array[Double]],
          m2: Array[Array[Double]],
          size: Int): Array[Array[Double]] = {
  var res = Array.fill(size)(new Array[Double](size))
  var i = 0
  while (i < size) {
    var j = 0
    while (j < size) {
      var k = 0
      while (k < size) {
        res(i)(j) += m1(i)(k) * m2(k)(j)
        k += 1
      }
      j += 1
    }
    i += 1
  }
  res
} 
```

Enter fullscreen mode Exit fullscreen mode

### 算法二

第二种算法在应用公式的微小变化之前转置第二个矩阵，该公式使乘法中第二个矩阵的索引反转。对于两个大小为 *n* 的方阵，它首先必须执行 *n* 次复制操作，然后再次执行 *n* 次乘法和加法。这也导致了 *O(n )* 的复杂性，但是有更多的开销。

```
def mult2(m1: Array[Array[Double]],
          m2: Array[Array[Double]],
          size: Int): Array[Array[Double]] = {
  var m2t = Array.fill(size)(new Array[Double](size))
  var x = 0
  while (x < size) {
    var y = 0
    while (y < size) {
      m2t(x)(y) = m2(y)(x)
      y += 1
    }
    x += 1
  }

  var res = Array.fill(size)(new Array[Double](size))
  var i = 0
  while (i < size) {
    var j = 0
    while (j < size) {
      var k = 0
      while (k < size) {
        res(i)(j) += m1(i)(k) * m2t(j)(k)
        k += 1
      }
      j += 1
    }
    i += 1
  }
  res
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结果

观察这两种算法，有根据的猜测可能是第一种比第二种快，因为除了转置操作和反向索引之外，它们几乎是相同的。两者之间的相对差异应该随着大小的增加而减小，因为 *n* 比 *n* 增长得更快，这也反映在具有相同渐近复杂度的两种算法中。然而`mult2`的内存需求有点高，因为它需要首先转置矩阵。

让我们看看不同矩阵大小的两种实现的执行时间 *n* 。我们还添加了一个列，指示选择第二个实现时获得的加速(`mult1` / `mult2` - 1)。

| *n* | 时间`mult1`(毫秒) | 时间`mult2`(毫秒) | 加速 |
| --- | --- | --- | --- |
| Ten | Zero point zero one one | Zero point zero one four | -20.5 % |
| Fifty | Zero point one six six | Zero point one two eight | 29.6 % |
| One hundred | One point three zero seven | One point one zero two | 18.5 % |
| Five hundred | Two hundred and forty-two point one | One hundred and forty-seven point four | 64.2 % |
| One thousand | 9 347 | 1 244 | 651.2 % |
| Three thousand | 398 619 | 33 846 | 1077.7 % |

印象深刻！即使我们做了额外的工作，我们使用`mult2`对 *n = 3000* 快了 10 倍以上！发生了什么事？

在我解释之前，了解一些计算机体系结构的基本知识是很有用的。下一节将讨论这一部分。如果你已经对此很熟悉或者不能等待答案，可以跳过下一部分，稍后再来看。

# 计算机架构

## 冯·诺依曼架构

大多数现代计算机，尤其是在商品领域，都是基于冯·诺依曼架构的。最初在 1945 年被描述，随着时间的推移演变成我们今天所拥有的。它描述了计算机的基本组件，如 CPU、内存和 I/O 设备，以及它们如何协同工作。

以下方案描述了主要组件及其连接方式。中央处理器(CPU)负责进行计算工作，例如算术运算。它通过北桥连接到主随机存取存储器(RAM)。北桥还连接其他高速接口，但我们不打算在这里详细介绍。如果数据在内存中不可用，则必须从永久存储器中加载。用于此的不同接口(例如 IDE、SATA、USB)通过南桥连接。

[![motherboard diagram](img/68143ae0a74d70243008f45673ca8de8.png)T2】](https://en.wikipedia.org/wiki/Northbridge_(computing)#/media/File:Motherboard_diagram.svg)

这意味着处理器和数据之间没有直接的联系。如果数据在主存储器中，它必须通过北桥，如果不在那里，它也必须通过南桥。这种存储层次结构是必要的，因为需要在存储成本、速度和大小之间进行权衡。

## 存储层次结构

存储可以分为持久性存储和易失性存储。为了使数据在计算机重启后仍然存在，需要将其持久地存储在例如硬盘驱动器(HDD)或固态驱动器(SSD)中。这种存储方式的缺点是通常没有针对随机访问进行优化(这对于文件来说是好的，但对于单个数据或代码来说可能不是)。它离 CPU 也很远，这增加了额外的传输延迟。

当引导操作系统时，所有需要的数据都被加载到易失性主存储器中。如果计算机没电，数据就会丢失。主存储器通常也小得多。但是，它速度更快，尤其是在延迟方面，并且针对随机访问进行了优化。

虽然将 RAM 用于更频繁使用的数据是一个好主意，但它仍然不是最佳选择，因为 CPU 需要通过北桥来读取或修改数据。为了解决这个问题，计算机增加了另一层内存:CPU 缓存。

商品主存一般基于动态 RAM ( [DRAM](https://en.wikipedia.org/wiki/Dynamic_random-access_memory) )。每个元件基本上只由一个晶体管和一个电容器组成，DRAM 很便宜，可以很好地封装，允许更多的容量。然而，由于其设计，它比静态 RAM ( [SRAM](https://en.wikipedia.org/wiki/Static_random-access_memory) )慢得多，后者通常在需要更低延迟时使用。SRAM 用于 CPU 缓存。下图总结了我们刚才讨论的存储层次结构。

[![memory hierarchy](img/23345efa0764e7c72113eab90023bd21.png)T2】](https://en.wikipedia.org/wiki/Memory_hierarchy#/media/File:ComputerMemoryHierarchy.svg)

CPU 高速缓存本身通常也有不同的层，从最快但最小的 1 级(l 1)到最大但最慢的 3 级(l 3)高速缓存。此外，L1 缓存分为数据部分(L1d)和指令/代码部分(L1i)。

如果您有一个多核 CPU，一些缓存将在内核之间共享。如果启用了超线程，实际的高速缓存大小很大程度上取决于您正在执行的程序，因为两个超线程必须共享同一个高速缓存。关于这个话题的更多细节，我推荐看看 Ulrich Drepper 写的关于内存的文章[每个程序员都应该知道的东西](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf)。

为了让读者对不同层的实际大小有一个印象，下表包含了我的 2016 笔记本电脑的规格。

| 储存；储备 | 大小 |
| --- | --- |
| L1i 缓存 | 32 KB |
| L1d 缓存 | 32 KB |
| L2 高速缓存 | 256 KB |
| L3 缓存 | 4 MB |
| 主存储器 | 16 GB |
| （同 solid-statedisk）固态（磁）盘 | 500 GB |

## CPU 缓存使用率

高速缓存存储经常使用的主存储器位置的数据副本。虽然在磁盘或内存中存储一些东西是程序员的一个深思熟虑的决定，但存储在缓存中的东西通常是在幕后管理的。

数据以固定大小的块在主存储器和高速缓存之间传输，即所谓的*高速缓存线*。缓存条目包含数据及其缓存的内存位置。每当 CPU 需要访问某个内存位置时，它首先检查缓存中是否有相应的条目。如果该条目存在，则称之为*缓存命中*，否则称之为*缓存未命中*。

缓存命中通常是您想要的，因为它避免了(相对)昂贵的主内存访问。请注意，您无法避免缓存未命中，它们通常不是一件坏事。如果第一次访问某个内存位置，缓存未命中是正常的。

然而，现代 CPU 在这种情况下也具有避免高速缓存未命中的机制。处理器有一个小模块负责预取缓存线。一个例子是，当处理结构类型(例如，一个人)并访问该人的一个属性(例如，地址)时，很可能您稍后将访问另一个属性。因此，CPU 还将预取其他属性，例如年龄和姓名。另一个例子是对阵列的顺序访问。当访问数组的第一个元素时，CPU 将并行预取更多的元素，同时仍然处理第一个元素。

有了这些知识，我们想回到矩阵乘法的原始例子，分析为什么对于更大的矩阵来说`mult2`比`mult1`表现得好得多。

# 矩阵乘法再探

在最初的例子中，我们有两个矩阵乘法的实现:`mult1`和`mult2`。两者基本相同，除了`mult2`在进入执行计算的三个嵌套循环之前转置了第二个矩阵。但是为什么会有区别呢？为什么这种差异只在更大的矩阵中可见？

在我们从更理论的角度分析这种差异之前，让我们看看更多的数字。我们将使用不同的矩阵大小再次执行这两种实现。然而，这次我们不是测量运行时间，而是使用 [`perf`](https://perf.wiki.kernel.org/index.php/Main_Page) 查看 L1d 缓存未命中的 CPU 计数器。

```
for s in 10 50 100 250 500 1000 1500 2000 2500 3000
do
  for m in 1 2
  do
    # Run mult$m with size $s and record CPU counters
    perf stat -e L1-dcache-loads,L1-dcache-load-misses \
      java -jar cpumemory.jar $s $m
  done
done 
```

Enter fullscreen mode Exit fullscreen mode

[![l1 cache miss percentage](img/ed6f925ac3dd3eddafe13eb727b17b60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Evr1fIWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cl9r7g4t90j1c2mrqg0c.png)

你可以看到，从 *n > = 250* 开始，`mult1`的 L1d 缓存未命中百分比增加。这意味着 CPU 必须退回到更大但更慢的 L2 缓存。它会浪费周期，等待所需的数据从更高级别的存储器中可用。但是为什么会这样呢？

如前一节所述，CPU 将数据预取到缓存中。这意味着当访问矩阵的第一个元素时，它将并行预取更多的元素。用三个索引`i`、`j`和`k`重述三个嵌套循环。

`m1(i)(k)`的第一次访问将触发预取更多元素，例如`m1(i)(k + 1)`。然而，`mult1`中的`m2(k)(j)`的第一次访问将触发预取`m2(k)(j + 1)`。虽然只要整个矩阵适合缓存，这不是一个大问题，但随着矩阵越来越大，这就成了一个大问题。

最里面的循环增加`k`，而不是`j`。所以当`j`增加的时候，`m2(k)(j + 1)`可能已经被驱逐了。这就是为什么`mult2`在矩阵越大时表现越好的原因。通过转置矩阵，然后交换对`m2t(j)(k)`的访问，即使缓存已满并且矩阵的部分必须被逐出，我们至少可以正确地为内部循环预取。

# 最后的想法

在这篇博文中，我试图向您展示作为一名软件开发人员，了解一些计算机架构的基础知识是多么重要。虽然大多数大学级别的计算机科学家应该在他们的研究中了解这一点，但对于其他正在编写代码的人来说，阅读更多关于这一主题的内容无疑是一个好主意。

我们研究了朴素矩阵乘法算法的一个实现，其中仅改变一个小细节就导致执行速度提高了 1000%以上。重要的是要注意到`mult2`到目前为止并不是最佳的实现。您可以查看论文[高速缓存不经意矩阵乘法，它使用基于 Peano 曲线](https://www5.in.tum.de/~bader/publikat/matmult.pdf)的元素排序，实现不依赖于实际高速缓存大小的高速缓存优化。还要注意，存在比 *O(n )* 具有更好的渐近复杂度的其他算法，例如 [Strassen 算法](https://en.wikipedia.org/wiki/Strassen_algorithm)。

你有没有遇到过一个很慢的程序，结果却有很多缓存未命中？你以前用过`perf`吗？你认为开发人员应该更加了解缓存逻辑以便编写高效的程序吗？请在评论中告诉我你的想法！