# 建立自己的传感器，给你的猫留下深刻印象-第 4 部分

> 原文：<https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-4-2bp8>

这篇文章是一系列文章中一部分:

1.  [传感器介绍](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-1-mhp)
2.  [传感器的解剖](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-2-2ja0)
3.  [状态传感器](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-3-2f9k)
4.  传感器中的提前终止(此职位)
5.  [使用传感器的功能](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-5-14di)
6.  [传感器练习和解答](https://github.com/green-coder/transducer-exercises)

* * *

# 提前终止和`reduced?`传感器中的值

假设您想要编写一个转换器，它使用输入流中的一些元素，然后忽略其后的任何元素。如果我们不使用流的剩余值，那么迭代它们将是一种浪费。

```
(defn  tired-map-transducer  [func  energy  tired-answer]  (fn  [rf]  (let  [state  (volatile!  energy)]  (fn  ([]  (rf))  ([result]  (rf  result))  ([result  input]  (rf  result  (let  [energy  @state]  (if  (pos?  energy)  (do  (vreset!  state  (dec  energy))  (func  input))  tired-answer))))))))  (into  []  (tired-map-transducer  inc  5  :whatever)  (list  1  2  3  4  5  6  7  8  9  10))  ; => [2 3 4 5 6 :whatever :whatever :whatever :whatever :whatever] 
```

Enter fullscreen mode Exit fullscreen mode

我们当然希望找到一种方法，让调用转换器的函数停止调用它们。

我们可以通过多种方式做到这一点，比如在最后一个数据后发送一个特殊值`:enough`。但是如果那个特殊数据`:enough`出现在某个输入数据流中呢？将其发送到输出端会被误解为传感器发出的提前终止的信息。

Clojure 核心团队偶然发现了一个不太可能出现在输入数据流中的东西(或者我们可以称之为 bug 并责怪用户:-)，并将其作为转换器通知其调用函数某个值是其想要返回的最后一个值的约定。

那个约定是:`(reduced last-value)`

```
(defn  responsible-map-transducer  [func  energy]  (fn  [rf]  (let  [state  (volatile!  energy)]  (fn  ([]  (rf))  ([result]  (rf  result))  ([result  input]  (let  [energy  @state]  (vreset!  state  (dec  energy))  (cond->  (rf  result  (func  input))  (<=  energy  1)  reduced)))))))  (into  []  (responsible-map-transducer  inc  5)  (list  1  2  3  4  5  6  7  8  9  10))  ; => [2 3 4 5 6] 
```

Enter fullscreen mode Exit fullscreen mode

`reduced`只是返回一个包装的值。调用函数可以使用谓词`reduced?`测试一个值是否被包装。

```
(reduced?  7)  ; => false  (reduced?  (reduced  7))  ; => true 
```

Enter fullscreen mode Exit fullscreen mode

## 那么，我们结束了吗？

在称之为完成之前，还有一件事要检查。

[![Look both ways when crossing the railway.](../Images/b316219f5b5f10c41c69c98e8099ca51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FM16s4T0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ypt2oip8ba5w2weps3ra.jpg)

让我们再测试一下我们的传感器。在那些情况下，它仍然有效吗？

```
(into  []  (comp  (responsible-map-transducer  inc  5)  ; Step 1  (responsible-map-transducer  inc  3))  ; Step 2  (list  1  2  3  4  5  6  7  8  9  10))  ; => [3 4 5]  ; It works fine.  (into  []  (comp  (responsible-map-transducer  inc  3)  ; Step 1  (responsible-map-transducer  inc  5))  ; Step 2  (list  1  2  3  4  5  6  7  8  9  10))  ; => [3 4 5]  ; It works fine. 
```

Enter fullscreen mode Exit fullscreen mode

一切正常。那我为什么要问呢？因为你必须知道它为什么还能工作。

`responsible-map-transducer`的实现只负责在它没有能量的时候返回什么。在正常情况下，没有对`(rf result ...)`的结果做任何特殊处理，它只是按原样返回。如果它是一个`reduced?`值，这意味着`rf`函数想要提前终止。我们的传感器必须确保在这种情况下返回值是`reduced`。

因为我们只是简单地返回了`rf`函数返回的内容，所以我们很幸运。情况可能并不总是如此，所以要始终注意并观察两方面:

*   处理传感器想要提前终止的情况，
*   处理`rf`想要提前终止的情况。

## 细节决定成败

你想过测试一下吗？

```
(into  []  (comp  (responsible-map-transducer  inc  3)  ; Step 1  (responsible-map-transducer  inc  3))  ; Step 2  (list  1  2  3  4  5  6  7  8  9  10)) 
```

Enter fullscreen mode Exit fullscreen mode

你没有吗？太糟糕了，因为你应该有:它触发了一个错误，你现在得到一个非常不清楚的错误信息。

```
; => ClassCastException clojure.lang.Reduced cannot be cast to clojure.lang.ITransientCollection  clojure.core/persistent! (core.clj:3240) 
```

Enter fullscreen mode Exit fullscreen mode

[![Confused Baby](../Images/0748c0ba651fab9eb475f194e0b3f2d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hpsw3bZC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2dc49nxku9hnyxqtnjv1.jpg)

当我们返回一个同时被两个传感器包装的值时，发生了一些事情:`(reduced (reduced last-value))`

调用传感器(即`into`)的函数通过期待一个值或`reduced`值来支持提前终止，在这种情况下，它通过`(unreduced reduced-value)`获得展开的值。但是它没有得到值，仍然得到一个减少的值。

```
(reduced?  (unreduced  (reduced  (reduced  7))))  ; => true 
```

Enter fullscreen mode Exit fullscreen mode

怎么修？要么我们要求 Clojure 核心团队改变所有使用转换器支持多重包装值的函数，要么我们避免多次包装值。

## “有这样一个函数”——里奇·希基

我们可以使用标准库中的 [`ensure-reduced`](http://clojuredocs.org/search?q=ensure-reduced) 函数。它的实现非常简单:

```
(defn  ensure-reduced  [x]  (if  (reduced?  x)  x  (reduced  x))) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们校正我们的传感器。

```
(defn  correct-map-transducer  [func  energy]  (fn  [rf]  (let  [state  (volatile!  energy)]  (fn  ([]  (rf))  ([result]  (rf  result))  ([result  input]  (let  [energy  @state]  (vreset!  state  (dec  energy))  (cond->  (rf  result  (func  input))  (<=  energy  1)  ensure-reduced)))))))  ; <- The difference is here  (into  []  (comp  (correct-map-transducer  inc  3)  ; Step 1  (correct-map-transducer  inc  3))  ; Step 2  (list  1  2  3  4  5  6  7  8  9  10))  ; => [3 4 5]  ; Idiomatic way:  ; (into []  ;       (comp  ;         (take 3)   ; Step 1  ;         (map inc)  ; Step 2  ;         (map inc)) ; Step 3  ;       (range 1 11)) 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么

您已经知道了实现自己的传感器所需的一切。我鼓励你自己尝试一些想法。如果你在冒险中遇到了阻碍问题，欢迎留下评论，我会尽力帮助你(当我有时间的时候)。

在下一部分中，我将讨论像`into`这样的函数，它们是使用转换器的**以及如何编写转换器。**