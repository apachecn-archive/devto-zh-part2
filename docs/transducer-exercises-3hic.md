# 换能器练习

> 原文：<https://dev.to/greencoder/transducer-exercises-3hic>

包含在 Clojure 中实现自定义转换器的练习。

**更新:[所有的解决方案现在都发布在 Github](https://github.com/green-coder/transducer-exercises) 上。本文不再更新，请使用 github 版本。**

## 公平通知

实现传感器很难，你可能会感到困惑。

[![Cry Kid Meme](img/254510b1b10a8fe2d384d2d4fed89240.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d2Pux1EW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gsvbn4gmhf2mgvxci1si.gif)

这是正常的，也是意料之中的，当我阅读自己的代码时，我也会遇到这种情况。不要放弃。

## 无操作传感器

实现一个除了将数据从输入端传递到输出端之外什么也不做的转换器。从功能上来说，它是一个身份转换器。

```
(into  []  nop  (range  3))  ; => [0 1 2] 
```

Enter fullscreen mode Exit fullscreen mode

使用它作为您的传感器模板进行以下操作。

## 准备战斗

*   实施有助于调试的`(debug in out)`传感器。

```
; We use this function instead of `into` for debugging.  ; The reason is that this avoids using transient  ; structures which do not `print` nicely.  (defn  slow-into  [to  xf  from]  (transduce  xf  conj  to  from))  (slow-into  []  (debug  "in"  "out")  (range  3))  ;; Outputs:  ; in 0  ; out [0]  ; in 1  ; out [0 1]  ; in 2  ; out [0 1 2] 
```

Enter fullscreen mode Exit fullscreen mode

*   为方便起见，添加了`(debug)`、`(debug indent)`和`(debug indent in out)`变体。都在叫引擎盖下的`(debug in out)`换能器。

```
(slow-into  []  (comp  (debug)  (debug  2)  (debug  4  ">"  "<"))  (debug  "      >"  "      <"))  ; 6-spaces prefix  (range  3))  ;; Outputs:  ; > 0  ;   > 0  ;     > 0  ;       > 0  ;       < [0]  ;     < [0]  ;   < [0]  ; < [0]  ; > 1  ;   > 1  ;     > 1  ;       > 1  ;       < [0 1]  ;     < [0 1]  ;   < [0 1]  ; < [0 1]  ; > 2  ;   > 2  ;     > 2  ;       > 2  ;       < [0 1 2]  ;     < [0 1 2]  ;   < [0 1 2]  ; < [0 1 2] 
```

Enter fullscreen mode Exit fullscreen mode

奇怪的图案，它让我想起...

PHP Hadouken！！！

[![PHP Hadouken](img/d934be41ad235d0f230e3e6c3d9e5faa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tZxuB4Lu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hcfldtz6c7b3mz1znuy6.jpg)

我们现在已经准备好面对真正的传感器实现，面对一大堆问题。

## 你能再说一遍吗？

你听清楚了，我要你安装下面的传感器。

```
(def  beg-data  (list  :may  :i  :beg  :your  :pardon  :?))  (into  []  (beg  2)  beg-data)  ; => [:may :may :i :i :beg :beg :your :your :pardon :pardon :? :?] 
```

Enter fullscreen mode Exit fullscreen mode

确保你也在处理提前终止。

```
(into  []  (comp  (take  3)  (beg  2))  beg-data)  ; => [:may :may :i :i :beg :beg] 
```

Enter fullscreen mode Exit fullscreen mode

对双方进行测试。

```
(into  []  (comp  (beg  2)  (take  3))  beg-data)  ; => [:may :may :i] 
```

Enter fullscreen mode Exit fullscreen mode

使用调试传感器进行测试(预计会出现问题和掉头发)。

```
(slow-into  []  (comp  (debug  0)  (beg  2)  (debug  2)  (take  3)  (debug  4))  beg-data)  ; Output:  ; > :may  ;   > :may  ;     > :may  ;     < [:may]  ;   < [:may]  ;   > :may  ;     > :may  ;     < [:may :may]  ;   < [:may :may]  ; < [:may :may]  ; > :i  ;   > :i  ;     > :i  ;     < [:may :may :i]  ;   < #reduced[{:status :ready, :val [:may :may :i]} 0x8cdcdd1]  ; < #reduced[{:status :ready, :val [:may :may :i]} 0x8cdcdd1]  ; Result:  ; => [:may :may :i] 
```

Enter fullscreen mode Exit fullscreen mode

`beg`传感器在收到简化的结果后不应继续向下游发送数据。如果需要，修复您的实现。

## 你所有的数据都是属于我的

[![Catducer](img/0fbf51fa773dae3e5ab98eb41c44f4a4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BAa5CVHC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/63bt1i95tncc5nxah0nd.jpg)

实施`my-cat`传感器。对于每一步，您都需要使您的实现适应测试样本所描述的新需求。

*   不成形的猫

其功能与`clojure.core/cat`相似。目前不处理提前终止。

```
(def  cat-data  [[1  2  :fish  3]  [:heat  4]  [5  :sleep  6]  [7]])  (into  []  my-cat  cat-data)  ; => [1 2 :fish 3 :heat 4 5 :sleep 6 7] 
```

Enter fullscreen mode Exit fullscreen mode

*   第二步，饿猫

如你所见，传感器为自己保留了所有的鱼和热量。

```
(into  []  my-cat  cat-data)  ; => [1 2 3 4 5 :sleep 6 7] 
```

Enter fullscreen mode Exit fullscreen mode

*   第三步，瞌睡猫

该版本的传感器在`:sleep`关键字处休眠，不处理任何后续数据。

```
(into  []  my-cat  cat-data)  ; => [1 2 3 4 5] 
```

Enter fullscreen mode Exit fullscreen mode

*   第四步，纠正猫

最后，我们希望我们的传感器以正确的方式尊重正常的提前终止(根据下游结果测试`reduced?`)。

```
(into  []  (comp  (take  2)  my-cat)  cat-data)  ; => [1 2 3 4]  (into  []  (comp  my-cat  (take  2))  cat-data)  ; => [1 2]  (slow-into  []  (comp  (debug  0)  my-cat  ; try replacing it with `cat` and compare  (debug  2)  (take  2)  (debug  4))  cat-data)  ;; Outputs:  ; > [1 2 :fish 3]  ;   > 1  ;     > 1  ;     < [1]  ;   < [1]  ;   > 2  ;     > 2  ;     < [1 2]  ;   < #reduced[{:status :ready, :val [1 2]} 0x517a7e8e]  ; < #reduced[{:status :ready, :val [1 2]} 0x517a7e8e] 
```

Enter fullscreen mode Exit fullscreen mode

*   提供一个相当于`my-cat`的惯用语句。

## 模数转换器

*   实现一个在多个元素间做白日梦的转换器。当处于白日梦状态时，它缓冲它的输入。当它停止白日梦时，它成批处理所有的缓冲区，然后再次做白日梦。

`a-d-d`传感器不会丢失数据。

```
(into  []  (a-d-d  3)  (range  10))  ; => [0 1 2 3 4 5 6 7 8 9]  ; Try:  (slow-into  []  (comp  (debug  0)  (a-d-d  3)  (debug  2))  (range  10)) 
```

Enter fullscreen mode Exit fullscreen mode

*   更改`a-d-d`，使其工作方式与`clojure.core/partition-all`相似。

```
(into  []  (a-d-d  3)  (range  10))  ; => [[0 1 2] [3 4 5] [6 7 8] [9]] 
```

Enter fullscreen mode Exit fullscreen mode

*   验证它在提前终止的情况下运行良好。

```
(slow-into  []  (comp  (debug  0)  (a-d-d  3)  (debug  2)  (take  2))  (range  10))  ; => [[0 1 2] [3 4 5]] 
```

Enter fullscreen mode Exit fullscreen mode

## 变频器内的一个减速器

实现`serieduce`,它提供一个减少输入元素并发射所有中间元素的传感器。

```
(into  []  (serieduce  conj  [1  2])  (range  3  6))  ; => [[1 2 3] [1 2 3 4] [1 2 3 4 5]]  (into  []  (serieduce  +)  (range  5))  ; => [0 1 3 6 10] 
```

Enter fullscreen mode Exit fullscreen mode

## 祝贺你成功了！

[![Success Kid](img/a9b04ac55d9cadddb1770548bdf0c482.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ElZ5WRYx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fcp68zg54jvmqh7s75wx.png)

你是少数人之一。