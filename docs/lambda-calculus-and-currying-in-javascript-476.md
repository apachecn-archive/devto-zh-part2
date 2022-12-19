# Javascript 中的 Lambda 演算和 currying

> 原文：<https://dev.to/gmlion/lambda-calculus-and-currying-in-javascript-476>

[![](img/876a6e79edd7108fffbdb87a7b685fbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o4QpStdn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AuBMpTv6_RMVQzco9.) 

<figcaption>照片由[阿列克斯·达尔伯格](https://unsplash.com/@aleksdahlberg?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

Javascript 作为一种语言总是特别容易吸收函数式编程概念，这可能是由于它的动态特性。有一些流行的关于函数式编程概念的 Javascript 库(最著名的是 Ramda)，但是今天我将采用一种更“回到根本”的方法来将 Javascript 与函数式编程相结合。让我们看看它去哪里。

### 一个论点统治所有人

[![](img/37a6bb84d7945f3d39f63782d7ff6a87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OgmOlLWX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AvBeGXtxusetZWFaD.) 

<figcaption>照片由[杰里米·帕金斯](https://unsplash.com/@jeremyperkins?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

函数式语言与命令式语言和面向对象语言的主要区别之一是函数式语言如何严格遵循 Lambda 演算理论。引用维基百科，“Lambda calculus(也写作λ-calculus)是数理逻辑中的一种形式系统，用于使用变量绑定和替换来表达基于函数抽象和应用的计算”。这背后的理论并不复杂，尤其是如果你有任何编程语言的经验。事实上，我们每天都在使用绑定和替换。

简单来说，我们说的是一种理论，你可以用命名实参定义函数，用自己的值替换函数体中的命名实参来调用这样的函数。以
中的
为例，函数

```
function double(x) {
 return x * 2;
} 
```

事实上，我们坚持这个理论。作为一个正式的系统，lambda 演算不像带有多个参数的函数那样定义“捷径”,因为你可以从重复的单次替换操作中得到相同的结果。这和你永远不要定义“三个操作数”的和或乘是一样的，因为定义两个操作数就足够了。

尽管如此，我们还是习惯了多参数函数:

```
function sum(a, b) {
 return a + b;
} 
```

如果我们决定严格遵守形式理论呢？我们可以只用单参数函数来表达同一个函数吗？Lambda 演算证明了这是可能，在 Javascript 中看起来是这样的:

```
function lsum(a) {
 return function(b) {
 return a + b;
 }
} 
```

lsum 定义了一个函数，它接受一个参数并返回另一个函数，所提供的参数已经被“包含”(绑定)。我可以听到你说:“消费这样定义的函数需要多少样板代码？”

你来判断:

```
lsum(2)(3) 
```

正如您所看到的，遵循“每个函数只有一个参数”的规则将我们推向一个不太糟糕的 Javascript 语法。语义上，我们仍然在使用一个双参数函数——我们只需要重新定义语法来给函数提供多个参数——但是在本质上，我们遵守规则。这样一条规则的影响比它初看起来要广泛。例如，使用只有一个参数的函数自动地给我们的函数提供了部分应用程序“特性”,这要感谢我们操作的 curry。

### 什么是阿谀奉承？

[![](img/518c63ff7660198918cc689c32100f6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZbT-GFKn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AsSpc8mlxK316OHR8.) 

<figcaption>照片由[诺贝特·莱瓦伊西斯](https://unsplash.com/@levajsics?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

Currying 是将一个多参数函数转换成多个单参数嵌套函数的操作，就像我们之前做的一样。

自动 currying 是函数式语言的一个特性，你可以通过调用少于参数总数的任何多参数函数来创建函数的“部分应用程序”。在我们的例子中，由于我们“手动”处理了 lsum 函数，如果我们定义了

```
var sum2 = lsum(2) 
```

我们得到一个新函数 sum2，它只接受一个参数(剩下的一个)。这里真正缺少的是一种优雅的方法来定义像 lsum 这样的函数，而没有多个函数嵌套的开销。这就是 Javascript 中一些宏支持派上用场的地方。

### 采用“保守”的方法

[![](img/36310157a058d16d8257627501e834c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L9UMKXqK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A18ZoTvW0OCkeu2G6.) 

<figcaption>照片由[猎杀者竞](https://unsplash.com/@huntersrace?utm_source=medium&utm_medium=referral)上[退](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

部分应用的一个完全不同的方法是采用一个多参数函数，一次“去掉”一个参数，以获得类似的结果。我称之为“保守的”,因为它依赖于 Javascript 语言的传统语义。稍后将详细介绍。我们可以定义这样一个函数:

```
function partialOne(fn, ...args) {
 return function(x) {
 return fn(...args, x)
 }
} 
```

并使用它提供除最后一个以外的所有参数:

```
function sum4(a, b, c, d) {
 return a + b + c + d;
}

var sum6to = partialOne(sum4, 1, 2, 3);

var eight = sum6to(2) 
```

下面是 partial 的另一个有趣版本，它使用任意数量的参数并返回一个仍能接受所有剩余参数的函数。

```
function partial(fn, ...args) {
 return function(x) {
 return fn(...args, ...arguments);
 }
}

var sum3to = partial(sum4, 1, 2)

var ten = sum3to(3,4) 
```

这或多或少就是 Clojure 中 partial 函数的工作方式。值得注意的是，spread 操作符允许一个比 Clojure 源代码中的[更简洁的定义，并且适用于提供给 partial 的任意数量的参数。

这肯定是有用的，但在这一点上，我们打破了一个参数的规则。从现在开始，我将放弃这条推理路线，坚持“单一论点”规则。](https://github.com/clojure/clojure/blob/clojure-1.9.0/src/clj/clojure/core.clj#L2606)

### 采用“λ语义”的方法

[![](img/121e4e27c67268c6d5e9b683cf3cde95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EKljQOqw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ABg-TEb5Q_7WNbHHc.) 

<figcaption>照片由[李凯文](https://unsplash.com/@kevinglobal?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

概括一下:首先，我们定义了单参数函数，并看到了它如何“免费”提供给我们部分应用程序，然后，我们定义了助手函数来操作现有多参数函数上的部分应用程序。可悲的是，这两种方法是正交的，这是因为它们给我们定义函数及其参数的方式赋予了不同的语义。这是我前阵子学习 F#时的一个感悟。F#定义多参数函数的语法是:

```
let lsum a b = a + b 
```

这种语法从概念上解释为开始描述的“嵌套函数”版本，事实上可以用相同的模式编写:

```
let lsum = fun b -\>
 fun a ->
 a + b 
```

第一个语法只是第二个版本的语法糖。因此，这两个定义都只使用一个自变量函数。

我们的 F# lsum 函数支持部分应用，但是我们的 Javascript 版本也支持！这一点很重要:**函数的局部应用不是一种语言的特性，它是专门定义单参数函数，以及使用嵌套函数定义“高阶”多参数函数的必然副产品。**

另一方面，在 F#中我们仍然可以这样定义一个函数:

```
let sumTuple (a,b) = a + b 
```

这看起来既奇怪又熟悉。在这个版本中，我们定义了一个单参数函数，将一个元组(a，b)作为单参数。元组有点像数组或对象，它们是包含多个值的原子结构。很明显，这个版本不允许 a 或 b 的部分应用，同样，我们的“普通”多参数求和 Javascript 函数也不允许！

你可以看到我要去哪里。如果我们认为 Javascript 的常用语法是定义绑定到函数的元组的一种方式，那么我们可以对 Javascript 应用相同的语义，并获得与 F#等函数式语言完全相同的结果。下面是我们得到的:

```
F#:
let lsum a b = a + b 
```

等于

```
F#:
let lsum =
 fun b ->
 fun a ->
 a + b 
```

并翻译成

```
JS:
function lsum(a) {
 return function(b) {
 return a + b
 }
} 
```

或者更好:

```
JS:
let lsum =
 (b) =>
 (a) => a + b 
```

这与第二个 F#版本的语法几乎相同。

更进一步

```
F#:
let sumTuple(a,b) = a + b 
```

翻译成

```
JS:
function sumTuple(a,b) {
 return a + b
} 
```

现在让我们比较一下你是如何使用这些函数的。

```
F#:
let n = lsum 4 5

JS:
let n = lsum(4)(5) 
```

和

```
F#:
let m = sumTuple(4,5)

JS:
let m = sumTuple(4,5) 
```

最后两个不是打印错误:它们完全一样。

还有:因为在 F#中元组是包含信息的基本结构——意味着“值”是“(值)”的语法糖——我们可以在 F#中重写 lsum 应用:

```
F#:
let n = lsum(4)(5) 
```

同样，这与 Javascript 版本完全相同。

### 圆滚滚来

[![](img/076f4a4bb57adbb986b7db20f6afe742.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DJyxKUCk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AWDFblDTlvY2D9kna.)

<figcaption>[阿什利·巴兹](https://unsplash.com/@ashleybatz?utm_source=medium&utm_medium=referral)关于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

的《一个人走过海滩边岩石做成的迷宫》

最终，Javascript 中真正缺少的是:

*   定义嵌套函数的更简单方法
*   元组作为语言的一等公民如果你可以解决这些不足，你可以将这种“替代”语义(姑且称之为“lambda 语义”)应用于 Javascript，并获得 lambda 演算带来的优势，例如部分应用。“现代”Javascript 有助于克服第一个限制。如果这种“lambda 语义”得到认可，从而获得更好的语言支持，那将是一件好事。你认为运输值得考虑吗？你知道现有的解决方案吗？请在评论中告诉我。