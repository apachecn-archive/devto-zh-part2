# 建立自己的传感器，给你的猫留下深刻印象-第 5 部分

> 原文：<https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-5-14di>

这篇文章是一系列文章中一部分:

1.  [传感器介绍](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-1-mhp)
2.  [传感器的解剖](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-2-2ja0)
3.  [状态传感器](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-3-2f9k)
4.  [传感器提前终止](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-4-2bp8)
5.  使用传感器的功能(本帖)
6.  [传感器练习和解答](https://github.com/green-coder/transducer-exercises)

* * *

这篇文章描述了一些使用传感器的函数，以及如何编写自己的函数。

# 传感器，使用它们获取`into`

如果你阅读了这个博客系列的前 4 部分，你可能已经注意到我只提到了一个使用传感器的函数:函数`into`。

```
(into  []  (map  inc)  (list  3  4  5))  ; => [4 5 6] 
```

Enter fullscreen mode Exit fullscreen mode

还有更多这样的。他们的角色是提供流转换的上下文。这包括**向传感器提供数据流**，以及**处理从传感器获得的转换后的数据流**。

它们是:

*   `from where`和`to where`，
*   `how do I take that input`和`what should I do with that output`。

由于它们是调用传感器的一方，它们也负责决定`when do I do this to that`。

### 来自标准库的传感器用户

注:在以下段落中，参数`xform`指的是传感器。

*   [(into to xform from)](http://clojuredocs.org/clojure.core/into) :当您希望输出流的元素都存储在一个集合中时，这个集合可以是一个向量、一个列表、一个映射、一个排序的映射或者任何其他类型的集合。我喜欢这个函数，因为它以一种非常有效的方式向集合[添加了内容](https://github.com/clojure/clojure/blob/clojure-1.9.0/src/clj/clojure/core.clj#L6820)。

*   [(sequence xform coll)](http://clojuredocs.org/clojure.core/sequence) :当您希望流“仅在需要时”被处理时。如果您认为序列消费者可能不需要所有的输出元素，或者如果不希望所有的输出元素都被立即处理，那么这是非常有用的。这对于限制 CPU 工作负载的转换是有用的，或者如果您需要通过较慢的通道(如网络)发送输出流，并且您不想预先缓冲整个输出流。

*   [(排出 xform* coll)](http://clojuredocs.org/clojure.core/eduction) :...这很复杂，查看文档以获得精确的解释。一般程序员很少需要。它返回一个非惰性序列，每次被 [`reduce`](http://clojuredocs.org/clojure.core/reduce) 函数使用时，使用传感器对该序列进行评估。

*   [(transducer xform f init coll)](http://clojuredocs.org/clojure.core/transduce):当您想使用 [`reduce`](http://clojuredocs.org/clojure.core/reduce) 操作，但又想对要缩减的数据执行流转换时。

### 传感器用户来自`clojure.core.async`库

*   [(chan buf-or-n xform)](http://clojuredocs.org/clojure.core.async/chan) :创建一个通道，在该通道中，读取器端的数据流将是由传感器转换的写入器端的数据流。

*   [(管道 n 到 xf from)](http://clojuredocs.org/clojure.core.async/pipeline) :“从 from 通道获取元素，并将其提供给 to 通道，以换能器 xf 为准，并行度 n [...]".仅用于无状态传感器。 [`pipeline-blocking`](http://clojuredocs.org/clojure.core.async/pipeline-blocking) 都有变体。

*   [(transducer xform f init ch)](http://clojuredocs.org/clojure.core.async/transduce):[`clojure.core/transduce`](http://clojuredocs.org/clojure.core/transduce)的变体，其中数据流来自通道而不是集合。

### 传感器的其他用途

可能存在使用换能器的其他功能，适用于不同的环境。如果你知道一些，请留下评论让我知道，如果它们与观众相关，我会添加它们。

# 还是实施一些吧！

好消息，传感器现在是你的朋友，你可以随时打电话给他们。但是如果你想成为一个好朋友，有一些规则要遵守。

### 输出功能

换能器在技术上是将一个缩减函数转换成另一个缩减函数的函数。

如果您不知道这是什么意思，您也可以将它们视为将一种输出函数转换为另一种输出函数函数，只不过这个输出函数将一种累加器值作为其第一个参数。

让我们尝试一下这个想法，让我们实现一个函数，从一个集合向一个传感器提供一个数据流，并将转换后的数据流逐个元素地输出到标准输出。

```
; Notes:  ; - Don't do this at home, this is still a toy function.  ; - The transducer is often called 'xf' and the reducer function 'rf'.  (defn  print-duce  [transducer  coll]  (let  [reduction-fn  #(print  (str  %2  \space))  process  (transducer  reduction-fn)]  (loop  [c  coll]  (when  (seq  c)  (do  (process  nil  (first  c))  (recur  (rest  c)))))))  (print-duce  (map  inc)  (list  3  4  5))  ; Output:  ; 4 5 6  ; But this does not work:  (print-duce  (partition-all  2)  (list  3  4  5))  ; Output:  ; [3 4]  ;  ; the [5] is missing! 
```

Enter fullscreen mode Exit fullscreen mode

### {0 1 2}-Arity

[![You said Arrietty?](img/985aae60e32a69b794598263e27c7fc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PV75P1uk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8vz1hmarvozuwt526k0y.jpg)

如果您阅读了这个博客系列的第 1 到第 4 部分，您将会知道换能器被期望以不同的算法来调用。

0-Arity: **忽略它**，不要调用它，除非你知道你在做什么(并在评论中告诉我为什么或者在 StackOverflow 问题中告诉我)。

2-Arity:每当您希望**向传感器**多输入一个数据元素时调用它。

1-Arity:一旦通知不再有可用的输入，就调用它。转换器(即使是无状态的)**可能会将更多的数据**刷新到输出函数中。

现在让我们改进一下我们的`print-ducer`。

```
; Note: Still a toy function.  (defn  print-duce  [xf  coll]  (let  [rf  (fn  ([])  ([result]  (print  (str  \newline  "--EOS")))  ([result  input]  (print  (str  input  \space))))  process  (xf  rf)]  (loop  [c  coll]  (if  (seq  c)  (do  (process  nil  (first  c))  ; 2-arity 'process'  (recur  (rest  c)))  (process  nil)))))  ; 1-arity 'flush'  (print-duce  (map  inc)  (list  3  4  5))  ; Output:  ; 4 5 6  ; --EOS  ; Now this works:  (print-duce  (partition-all  2)  (list  3  4  5))  ; Output:  ; [3 4] [5]  ; --EOS 
```

Enter fullscreen mode Exit fullscreen mode

### 适可而止(提前终止)

你的传感器有权决定何时停止水流。当它认为不应该有任何其他元素时，它将返回一个`reduced`值。你需要注意，不要问太多。

```
(defn  print-duce  [xf  coll]  (let  [rf  (fn  ([])  ([result]  (print  (str  \newline  "--EOS")))  ([result  input]  (print  (str  input  \space))))  process  (xf  rf)]  (loop  [c  coll]  (if  (seq  c)  (let  [result  (process  nil  (first  c))]  (if  (reduced?  result)  (process  (deref  result))  ; unwraps it and stop processing the stream  (recur  (rest  c))))  ; continue processing the stream  (process  nil))))) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，当我在第 4 部分中使用`unreduced`时，我使用了`deref`来展开减少的值。这两个函数是不同的，`deref`实际上是展开减少的值， [`unreduced`](http://clojuredocs.org/clojure.core/unreduced) 是展开一个值**，只有当**减少时。

源代码:

```
(defn  unreduced  [x]  (if  (reduced?  x)  (deref  x)  x)) 
```

Enter fullscreen mode Exit fullscreen mode

### 减少作为参数的功能

如果我们想让我们的函数更通用，并接受一个“1-2-arity”减函数作为参数，该怎么办？

```
(defn  multi-duce  [xf  rf  init  coll]  (let  [process  (xf  rf)]  (loop  [acc  init  c  coll]  (if  (seq  c)  (let  [result  (process  acc  (first  c))]  (if  (reduced?  result)  (process  (deref  result))  (recur  result  (rest  c))))  (process  acc)))))  (multi-duce  (map  inc)  conj  []  (list  3  4  5))  ; => [4 5 6]  (multi-duce  (partition-all  2)  conj  []  (list  3  4  5))  ; => [[3 4] [5]] 
```

Enter fullscreen mode Exit fullscreen mode

### (由我的猫)变得简单容易

[![This is not my cat](img/ec01f2fd83d69e2e44b36cf040d3f781.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F8JfEISW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vw6dkrd03jzz8lwmmszn.jpg)

我的猫刚刚告诉我，我是一个白痴，因为有一个更短更有效的方法来实现上述功能。

我把键盘给了他，下面是他给我看的:

```
(defn  cat-duce  [xf  rf  init  coll]  (let  [process  (xf  rf)  result  (reduce  process  init  coll)]  (process  result)))  (cat-duce  (map  inc)  conj  []  (list  3  4  5))  ; => [4 5 6]  (cat-duce  (partition-all  2)  conj  []  (list  3  4  5))  ; => [[3 4] [5]] 
```

Enter fullscreen mode Exit fullscreen mode

事实上，这似乎是有效的。谢谢猫！

### `transduce`功能

cat-duce 函数非常接近传说中的 [`transduce`](http://clojuredocs.org/clojure.core/transduce) 函数的实现。它有相同的签名和[几乎相同的实现](https://github.com/clojure/clojure/blob/clojure-1.9.0/src/clj/clojure/core.clj#L6800)，不同之处在于增加了对集合的支持，这些集合希望在被缩减时被注意到(就像 [`eduction`](http://clojuredocs.org/clojure.core/eduction) 函数的结果)。

# 接下来是什么

恭喜你！！！

到现在为止，你应该已经知道了以你自己的方式使用传感器所需要的任何东西。你可能还需要一点点锻炼你的技能- **熟能生巧**。

[https://www.youtube.com/embed/04854XqcfCY](https://www.youtube.com/embed/04854XqcfCY)

我将在 2018 年 5 月 30 日发布一些与换能器相关的练习，作为对台北换能器研讨会的支持。链接将在不久后添加到这里。

敬请期待！

**更新**:我创作了一些[换能器练习及其解答](https://github.com/green-coder/transducer-exercises)。