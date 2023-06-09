# 建立自己的传感器，给你的猫留下深刻印象-第 1 部分

> 原文：<https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-1-mhp>

这篇文章是一系列文章中一部分:

1.  传感器介绍(本帖)
2.  [传感器的解剖](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-2-2ja0)
3.  [状态传感器](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-3-2f9k)
4.  [传感器提前终止](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-4-2bp8)
5.  [使用传感器的功能](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-5-14di)
6.  [传感器练习和解答](https://github.com/green-coder/transducer-exercises)

* * *

本文简要介绍了什么是传感器以及如何使用传感器。

# 你好换能世界

简单来说:

> 转换器相当于对数据元素流的操作。

那个人是一种转换器，它从其输入端获取数据元素，并可能在其输出端发送一些:
[![A kind of transducer](img/99c71b2860bcb9d353513b9fff96bf76.png "from the game 'Human Resource Machine'")](https://res.cloudinary.com/practicaldev/image/fetch/s--FjZRWx4f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bnn66cxstjgiptb3mgh7.png)

您可以将它视为一个函数，对于输入流的每个元素，它只被调用一次，并且可以将任意数量的元素发送到输出流。

有一个特别强调的事实是:

*   传感器本身不会从输入流中获取任何东西，这不是它的工作或职责。它被每个数据元素调用。
*   传感器只知道它必须用它的每个输出元素调用一个指定的(和模糊的)函数，并且必须返回它的结果。它不知道数据去了哪里。

🤓注:*如果你想在一个有趣的虚构世界中用直观简单的编程语法来扮演自己的转换器，[试试这个很酷的游戏](https://en.wikipedia.org/wiki/Human_Resource_Machine)。*

# 文卿，请集中注意力

哦，好的，好的。

这里是最简单的传感器:

```
(map  identity) 
```

Enter fullscreen mode Exit fullscreen mode

它接受一些数据元素作为输入，并将完全相同的数据元素输出。除了目前它没有朋友，没有人给他数据，也没有提供输出。一个传感器就像一个没有食物的胃(嗯..那种情况下的输出我们就不多说了，注意它还是需要一个去处的)。

让我们正确地使用传感器。这里,`into`函数从一个列表中给它一些数据元素，并将它的输出元素收集到一个向量中。

```
(into  [\b  \a]  (map  identity)  (list  \n  \a  \n  \a))  ; => [\b \a \n \a \n \a] 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子是一个转换器，它实际上对它的输入做了一些事情，它把每个字符转换成字母表中它前面的字符。

```
(into  [\b  \a]  (map  #(char  (dec  (int  %))))  (list  \u  \n  \b  \o))  ; => [\b \a \t \m \a \n] 
```

Enter fullscreen mode Exit fullscreen mode

注意，您可以使用任何函数`f`，该函数以 1 个值作为参数，并通过使用形式`(map f)`返回 1 个值。简单易行。

现在让我们看看一些内置传感器，它们的输入和输出元件数量不同。

这个为每个输入元素输出 1 个或无输出元素:

```
(into  []  (filter  #(<=  (int  \a)  (int  %)  (int  \f))  (list  \c  \r  \a  \f  \e  \b  \h  \a  \b  \l  \e)))  ; => [\c \a \f \e \b \a \b \e] 
```

Enter fullscreen mode Exit fullscreen mode

这个为每个输入元素提供一个或多个输出元素。

```
(into  []  (mapcat  #(if  (<=  0  %  9)  (list  %  %)  (list  %)))  (list  10  5  16  7  13)))  ; => [10 5 5 16 7 7 13] 
```

Enter fullscreen mode Exit fullscreen mode

*注意:提供给`mapcat`的函数返回一个任意大小的集合，而`mapcat`返回一个转换器，它为每个输入元素输出任意数量的元素。*

# 会融合吗？

多个传感器可以通过管道连接在一起，形成更复杂的数据处理管道。这是通过`comp`功能完成的，合成的结果是一个传感器。数据元素在管道中一个接一个地流动，在每个步骤之间不使用缓冲区来存储中间结果——这是一个流过程。

```
(into  []  (comp  (map  inc)  ; first step  (filter  odd?)  ; second step  (mapcat  #(if  (<=  0  %  9)  ; third step  (list  %  %)  (list  %))))  (list  8  9  10  11  12)))  ; => [9 9 11 13] 
```

Enter fullscreen mode Exit fullscreen mode

# 内置换能器

Clojure 有一些非常流行的传感器，因为它们很方便。

[TODO:插入一个链接，指向我未来的自己将会写的一篇文章]

# 接下来是什么

[在下一篇文章](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-2-2ja0)中，我将讨论传感器的构造以及如何编写自己的程序。