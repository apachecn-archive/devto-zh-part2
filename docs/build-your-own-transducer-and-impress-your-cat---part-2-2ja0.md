# 建立自己的传感器，给你的猫留下深刻印象-第 2 部分

> 原文：<https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-2-2ja0>

这篇文章是一系列文章中一部分:

1.  [传感器介绍](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-1-mhp)
2.  传感器的解剖(本帖)
3.  [状态传感器](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-3-2f9k)
4.  [传感器提前终止](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-4-2bp8)
5.  [使用传感器的功能](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-5-14di)
6.  [传感器练习和解答](https://github.com/green-coder/transducer-exercises)

* * *

# 解剖换能器

本文简要介绍了传感器的结构以及如何实现。

## 简单的一对一映射

假设我们想要手动创建一个增加数字的传感器。我们通常可以只使用`(map inc)`，但是为了训练，我们将从头开始。

```
(def  inc-transducer  (fn  [rf]  (fn  ([]  (rf))  ; 0-arity aka 'the useless'  ([result]  (rf  result))  ; 1-arity aka 'the flusher'  ([result  input]  (rf  result  (inc  input))))))  ; 2-arity aka 'the doer'  (into  []  inc-transducer  (list  4  5  6))  ; => [5 6 7]  ; idiomatic way:  ; (into [] (map inc) (list 4 5 6)) 
```

Enter fullscreen mode Exit fullscreen mode

`rf`功能处理我们传感器的输出值。确实如此...“something”返回合并(或不合并)版本的`result`值和已处理数据的值`(inc input)`，我们的传感器需要返回新的结果。

`rf`功能可以是我们组成的另一个传感器，也可以是终端缩减功能(因此得名`rf`)。

`0-arity`函数是(IHMO)一个无用的伪约定，因为我们不能指望它被使用传感器的函数调用。只是将呼叫传输到下一个传感器/射频，也许它会在某些特定的上下文中对它做些什么，谁知道呢。

当没有更多的数据要处理时，使用传感器的函数调用`1-arity`函数。如果传感器在本地状态下有一些数据，它们可以在那里刷新它们的数据(稍后将详细介绍这种可能性)。

`2-arity`是输入数据被处理并传递给管道中下一个函数的地方。

## 与参数一一对应

现在让我们假设，不是增加数字，而是给它们加上一个给定值，那么我们需要一个带参数的传感器。

```
(defn  add-transducer  [n]  (fn  [rf]  (fn  ([]  (rf))  ([result]  (rf  result))  ([result  input]  (rf  result  (+  input  n))))))  (into  []  (add-transducer  3)  (list  4  5  6))  ; => [7 8 9]  ; idiomatic way:  ; (into [] (map #(+ 3 %)) (list 4 5 6)) 
```

Enter fullscreen mode Exit fullscreen mode

## 无兔换能器(一对一)

我们想要一个能让兔子消失的传感器，以说明传感器可能无法提供新的输出值的情况。

```
(defn  magician-transducer  [animal]  (fn  [rf]  (fn  ([]  (rf))  ([result]  (rf  result))  ([result  input]  (if  (=  animal  input)  result  ; Just don't "merge" the input into the result.  (rf  result  input))))))  (into  []  (magician-transducer  :rabbit)  (list  :dog  :rabbit  :lynel))  ; => [:dog :lynel]  ; idiomatic ways:  ; (into [] (remove #(= :rabbit %)) (list :dog :rabbit :lynel))  ; (into [] (filter #(not= :rabbit %)) (list :dog :rabbit :lynel)) 
```

Enter fullscreen mode Exit fullscreen mode

没有兔子，没问题。

## 多猫换能器(一对二)

如果我们现在想要更多的猫呢？(数据输出多于输入)

```
(defn  glitch-transducer  [animal]  (fn  [rf]  (fn  ([]  (rf))  ([result]  (rf  result))  ([result  input]  (if  (=  animal  input)  (->  result  (rf  input)  (rf  input))  ; Send the input twice to the output pipeline.  (rf  result  input))))))  (into  []  (glitch-transducer  :cat)  (list  :dog  :cat  :lynel))  ; => [:dog :cat :cat :lynel]  ; idiomatic way:  ; (into []  ;       (mapcat #(if (= :cat %) (list % %) (list %)))  ;       (list :dog :cat :lynel)) 
```

Enter fullscreen mode Exit fullscreen mode

更多的猫。尼奥会很高兴的。

## RLE 减压(一对多)

假设我们想一次将一系列值发送到输出，但我们不能像前面的例子那样做，因为重复的次数不固定或者太多，而且我们又懒又理智。我们可以用`reduce`把值一个一个的输出(现在你可以看到`rf`为什么那么叫了，可以看做是一个减函数)。

```
(def  rle-decoder-transducer  (fn  [rf]  (fn  ([]  (rf))  ([result]  (rf  result))  ([result  [count  data]]  (reduce  rf  result  (repeat  count  data))))))  (into  []  rle-decoder-transducer  (list  [0  :a]  [1  :b]  [2  :c]  [3  :d]))  ; => [:b :c :c :d :d :d]  ; idiomatic way:  ; (into []  ;       (mapcat (fn [[count data]] (repeat count data)))  ;       (list [0 :a] [1 :b] [2 :c] [3 :d])) 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么

上面显示的所有传感器都是无状态的:它们的行为完全由它们的输入和初始不可变参数描述。

在这篇博文的下一部分，我将介绍**有状态**转换器，即具有本地可变状态的转换器。