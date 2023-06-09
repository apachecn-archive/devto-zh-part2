# 建立自己的传感器，给你的猫留下深刻印象-第 3 部分

> 原文：<https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-3-2f9k>

这篇文章是一系列文章中一部分:

1.  [传感器介绍](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-1-mhp)
2.  [传感器的解剖](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-2-2ja0)
3.  有状态传感器(这篇文章)
4.  [传感器提前终止](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-4-2bp8)
5.  [使用传感器的功能](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-5-14di)
6.  [传感器练习和解答](https://github.com/green-coder/transducer-exercises)

* * *

# 有状态传感器

本文简要描述了如何实现有状态转换器。

## 我们什么时候需要它们

每当一些输出元素依赖于多个输入元素时，我们就需要它们。

需要存储一些与先前输入值相关的信息，直到传感器有足够的信息开始输出值。

存储的信息可以只是输入值，也可以是派生信息(通常占用较少的内存空间)。

## 纤纤传感器

假设我们有一个字符流来表示以零结尾的字符串，我们想要一个字符串流。

```
(defn  string-builder-transducer  [separator]  ; The local state *should not* be defined here.  (fn  [rf]  ; The local state of the transducer comes here.  (let  [state  (volatile!  [])]  (fn  ([]  (rf))  ([result]  (->  result  (rf  (apply  str  @state))  (rf)))  ([result  input]  (let  [chars  @state]  (if  (=  separator  input)  (do  (vreset!  state  [])  (rf  result  (apply  str  chars)))  (do  (vreset!  state  (conj  chars  input))  result))))))))  (into  []  (string-builder-transducer  0)  (list  \H  \e  \l  \l  \o  0  \C  \l  \o  \j  \u  \r  \e  0  \w  \o  \r  \l  \d  \!))  ; => ["Hello" "Clojure" "world!"] 
```

Enter fullscreen mode Exit fullscreen mode

三个音符:

*   地方政府应该被引入的位置很重要。如果您在错误的位置引入它，当换能器在多个管道中使用时，一些局部状态将会徘徊不去并导致错误。
*   为了效率，使用了`volatile!`和`vreset!`来代替原子。
*   在这个例子中，转换器只是存储遇到的字符，直到每个块的末尾，然后一次性使用它们来创建一个字符串。不必总是这样(例如下一段)。

## 组块总和传感器

假设我们有一个由特殊关键字`:|`分隔的数字流，我们想要计算每个数字块的总和。

```
(defn  chunk-sum-transducer  [separator]  (fn  [rf]  (let  [state  (volatile!  0)]  (fn  ([]  (rf))  ([result]  (->  result  (rf  @state)  (rf)))  ([result  input]  (let  [acc  @state]  (if  (=  separator  input)  (do  (vreset!  state  0)  (rf  result  acc))  (do  (vreset!  state  (+  acc  input))  result))))))))  (into  []  (chunk-sum-transducer  :|)  (list  1  2  3  4  :|  42  :|  :|  3  5))  ; => [10 42 0 8] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们的转换器在内存方面是高效的，因为它只在本地状态中保存一组数字，而不是当前块的所有元素。

## 分组换能器

另一个稍微复杂一点的例子是，这种传感器将消息分组为数据包，这样数据包的大小不会超过其最大限制(如果可能，否则它会使用更大的数据包)。

这里我们使用一个包含多个值的映射作为本地状态。

```
(defn  packet-transducer  [max-size]  (fn  [rf]  (let  [state  (volatile!  {:packet  []  :size  0})]  (fn  ([]  (rf))  ([result]  (let  [{:keys  [packet  size]}  @state]  (cond->  result  (pos?  size)  (rf  packet)  ; Flush the local state to output.  :always  (rf))))  ; Transmit the flush signal.  ([result  input]  (let  [{:keys  [packet  size]}  @state  input-size  (count  input)  new-size  (+  size  input-size)]  (if  (<=  new-size  max-size)  (do  (vreset!  state  {:packet  (conj  packet  input)  :size  new-size})  result)  (do  (vreset!  state  {:packet  [input]  :size  input-size})  (cond->  result  (pos?  size)  (rf  packet))))))))))  (into  []  (packet-transducer  5)  (list  [1  1]  [2  2]  [3  3  3]  [4  4]  [5]  [6  6  6  6  6]))  ; => [[[1 1] [2 2]] [[3 3 3] [4 4]] [[5]] [[6 6 6 6 6]]] 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么

[在本博客系列](https://dev.to/greencoder/build-your-own-transducer-and-impress-your-cat---part-4-2bp8)的下一部分，我将讨论如何通过使用`reduced`和`reduced?`来支持数据管道中的提前终止。