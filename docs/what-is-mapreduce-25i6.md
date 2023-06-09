# 什么是 MapReduce？

> 原文：<https://dev.to/kjir/what-is-mapreduce-25i6>

*最初发布于[我的博客](https://www.sbisinger.ch/2018/06/01/what-is-mapreduce/)T3】*

在这个大数据时代，一个叫做*数据科学家*的新物种正在崛起，美国*【mere】*软件开发人员可能听说过一个叫做 MapReduce 的东西，但它到底是什么呢？为什么在过去的十年里它成为了改变游戏规则的工具？这是我试图用简单的术语解释这个概念。

为了说明这些概念，我将使用 Python 和 LISP(特别是 clo jure ), Python 是最流行的语言之一，尤其是在数据科学方面，LISP 是引入了 MapReduce 概念的语言。

那么什么是 MapReduce 呢？它是一种解决一些问题的方法，允许我们以非常简单的方式处理大量数据。它在 2004 年的谷歌白皮书中有所描述，并且已经成为 Apache Hadoop 等工具的基础。

## 古老可靠的方式

但是让我们不要迷失在抽象的描述中，让我们从头开始——用一个简单的循环:

```
def powers(numbers):
  results = []
  for number in numbers:
    results.append(number * number)
  return results 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这不是很习惯，是吗？下面是更好的版本:

```
def powers(numbers):
  return [number * number for number in numbers] 
```

Enter fullscreen mode Exit fullscreen mode

我相信每个能写上面代码的人都明白这里发生了什么:我们有一个某种类型的数组，我们在元素间循环。对于这些元素中的每一个，我们计算 2 的幂，并将结果放入一个新的数组中。

不难看出这在机器本身的较低层次上是如何工作的:数组是一个内存区域，我们从指向这个内存区域的开头开始，将第一个值读入 CPU 寄存器，将其自身相乘，并将该值存储在对应于新数组的另一个内存区域中。然后继续处理下一个元素，直到数组结束。

因为我们确切地告诉计算机应该做什么，我们称这种编写程序的方式为*命令式编程*。

## *其他*老可靠的办法

同样的东西用 LISP 怎么写？没那么复杂:

```
(defn  powers  [numbers]  (map  (fn  [number]  (*  number  number))  numbers)) 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。这些括号是怎么回事？好吧，让我们把它分解成容易消化的部分，好吗？

```
(defn  powers  [numbers]  ...) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义一个名为`powers`的函数，它接受一个参数`numbers`。

```
(fn  [number]  ...) 
```

Enter fullscreen mode Exit fullscreen mode

这创建了一个匿名函数，它接受一个参数`number`。在 python 中，你可以用`lambda number: ...`完成同样的任务。

```
(*  number  number) 
```

Enter fullscreen mode Exit fullscreen mode

这乍一看有点奇怪，但这只是意味着我们将`number`乘以它本身！

现在是有趣的部分:

```
(map  (fn  [number]  (*  number  number))  numbers) 
```

Enter fullscreen mode Exit fullscreen mode

见见我们的新朋友`map`！这个函数做的很简单:返回一个值列表，这些值是作为第一个参数传入的函数的结果，应用于作为第二个参数传入的列表中的每一项。

或者换一种说法，`map`将遍历`numbers`中的每个值，对每个值应用我们的函数，并在一个新的列表中返回这个函数的结果。它相当于我们的 for 循环！

但是，让我们思考一下在较低层次上发生了什么:从我们看到的代码中，我们实际上无法知道这将如何进行！我们会按照 for 循环的顺序遍历内存吗？还是逆序？还是以某种随机的顺序？我们不能确定，因为如何产生结果的细节不在我们的掌握之中，但它由我们的编程语言的实现来负责。

这就是所谓的*函数式编程*，它是*声明式编程的一种形式:*我们声明我们想要什么，而不是如何产生我们的结果。但是为什么这很重要呢？仅仅是风格和个人品味的问题吗？

## 思考大数据

我们的例子足够小，所以我们循环数据的方式无关紧要。但是，如果我们有一个非常大的数字列表，比内存所能容纳的还要大，那会怎么样呢？如果我们不得不执行一个更加 CPU 密集型的函数，而不是乘法，会怎么样？这将如何发展？

在这种情况下，您可能想要引入某种形式的并行性，以使用所有的 CPU 内核。在我们的 Python 例子中会是什么样子呢？ <sup id="fnref1">[1](#fn1)</sup>

```
# To use Threads, we would use ThreadPoolExecutor with concurrent.futures.ProcessPoolExecutor(max_workers=NUM_WORKERS) as executor:
  futures = [executor.submit(expensive_function, number) for number in numbers]
  concurrent.futures.wait(futures)
  results = [future.result() for future in futures] 
```

Enter fullscreen mode Exit fullscreen mode

复杂度绝对上升了！这是利用了`concurrent.futures`模块，它已经简化了很多细节。 <sup id="fnref2">[2](#fn2)</sup>

LISP 呢？

就是这里:

```
(pmap  expensive-function  numbers) 
```

Enter fullscreen mode Exit fullscreen mode

唯一相关的变化是我们使用了`pmap`而不是`map`，我们必须改变函数的唯一原因是因为在单独的线程中运行函数可能并不总是令人满意的。这不仅仅是编程风格或语法糖的问题，这实际上是一个非常大的问题！

现在让我们进一步扩展推理:如果我们有如此多的数据要处理，可能来自几千兆字节甚至兆兆字节的文件，而我们不可能在一台计算机上处理所有这些数据，那该怎么办？我们必须将负载分布到成百上千的服务器上！我们如何做到这一点？

## 分发工作

我们如何将工作分散在不同的服务器上，而不是分散在不同的线程或进程中？这是开始变得令人毛骨悚然的地方。我们必须以某种方式管理我们的服务器集群，确保它们是可达的，分配工作，处理故障和重试，理想情况下还可以自动扩展它们。似乎很复杂？是的，因为我们在谈论一个分布式系统，有很多方式会失败，有无数的陷阱要避免。分布式系统很难正确实现。

那么我们的代码在 Python 中会是什么样子呢？老实说，我甚至不想从它开始。这将是一个巨大的努力来完成这一点，它肯定不适合在这篇博文。一个可能的想法是使用 Celery 对计算片进行排队，并使用它将负载分布到不同的主机上。

在 LISP 中会是什么样子？理论上，它不需要改变:我们可以有一个`dmap`函数，在多个节点上分配工作。实际上，这比那要复杂一些，因为我们需要设置我们的集群并控制它的行为。

不过，我不想给你们留下一个例子，这里有一个用 Python 写的例子，使用了一个名为
[Scoop](http://scoop.readthedocs.io) :
的库

```
from scoop import futures

list(futures.map(expensive_function, numbers)) 
```

Enter fullscreen mode Exit fullscreen mode

您能从代码中看出这实际上可能在多台机器上运行吗？

## 但是 reduce 呢？

我们详细讨论了`map`操作，但是我们还没有提到`reduce`。是关于什么的？

我相信你以前遇到过很多次这种操作，看看这种实现操作的命令式方法:

```
def sum_nums(numbers):
    count = 0
    for number in numbers:
        count += number
    return count 
```

Enter fullscreen mode Exit fullscreen mode

我敢肯定，你已经这样做了上百次了。将一个数组转换成某种散列映射通常是有用的，例如:

```
def group_by_id(customers):
    customers_map = {}
    for customer in customers:
        customers_map[customer.id] = customer
    return customers_map 
```

Enter fullscreen mode Exit fullscreen mode

这在函数世界中会是什么样子呢？就是这里:

```
(defn  sum_nums  [numbers]  (reduce  +  0  numbers)) 
```

Enter fullscreen mode Exit fullscreen mode

同样，细节是隐藏的，我们只提供一个组合操作(在我们的例子中是`+`)、一个初始值(`0`在这个特殊的例子中可以省略)和集合。

将会发生的是，它将从集合中取出一个元素，将其与我们的累加器(即初始的`0`)组合，使用这个组合作为新的累加器，并继续处理集合中的下一个元素。

如果我们将它与`map`操作结合起来，我们可以做一些非常强大的事情:

```
(defn  sum_squares  [numbers]  (reduce  +  (map  *  numbers))) 
```

Enter fullscreen mode Exit fullscreen mode

命令式对等词是这样的:

```
def sum_squares(numbers):
    final_sum = 0
    for number in numbers:
        final_sum += number * number
    return final_sum 
```

Enter fullscreen mode Exit fullscreen mode

## 终于 MapReduce 了

既然我们已经阐明了构建模块及其背后的概念，那么就很容易理解 MapReduce 是什么了。让我们直接看一下 Google 白皮书中的例子，它会统计文本中出现的单词:

```
 map(String key, String value):
      // key: document name
      // value: document contents
      for each word w in value:
        EmitIntermediate(w, "1");

    reduce(String key, Iterator values):
      // key: a word
      // values: a list of counts
      int result = 0;
      for each v in values:
        result += ParseInt(v);
      Emit(AsString(result)); 
```

Enter fullscreen mode Exit fullscreen mode

我们首先定义一个 map 操作，对于它接收的每个文档，它将把它分成单词，并为每个单词发出一个键/值对，其中键是单词本身，值是`1`。

然后，reduce 函数将接收该键和对应于该键的值列表。在我们的例子中，对于每个单词，我们会收到一个 1 的列表，每个 1 代表这个单词的一个重复。

然后我们把所有的这些数据加在一起。这就是我们发出的价值。

所有这些实际上都是以分布式的方式发生的，但是代码并不需要知道。多酷啊。

## 就这样结束了吗？

仅此而已吗？大多数情况下，是的。当然还有更多的东西需要了解，但基本思想是**即**简单。从这个想法出发，其他很酷的东西被创造出来，以至于[谷歌没有人再使用 MapReduce 了](https://www.youtube.com/watch?v=AZht1rkHIxk)！

我希望这能让你更好地理解 MapReduce 背后的概念，并对它允许你做什么有一个大致的感觉。在这和实际使用它之间有更多的步骤要做，但也许现在它看起来不那么可怕了。

这有帮助吗？请在评论中告诉我！

* * *

1.  由于全局解释器锁，我们实际上在这里产生了多个进程。Python 线程对于 CPU 密集型操作毫无用处。 [↩](#fnref1)

2.  对于执行器，您可以使用 executor.map，这是一种解决问题的方法。这凸显了它的强大之处。 [↩](#fnref2)