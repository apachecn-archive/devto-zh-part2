# 平滑代码背后的刚性代码——中间药剂

> 原文：<https://dev.to/blackode/the-rigid-code-behind-smooth-code-elixir-intermediate-2pa3>

你看到的更简化。

[![Photo by [Jamie Street](https://unsplash.com/photos/d299Wu2xipY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)](img/08a0c7c8a3450b9c168a8b6d153964c8.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--x08ejX1z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/6158/1%2AwvfO4tFXIbDev34wNMu4yw.jpeg) *照片由[杰米街](https://unsplash.com/photos/d299Wu2xipY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)* 

本文向您展示了您在 elixir 中编写的流畅代码行背后的僵硬代码行。

正如我们所知，大多数 Elixir 编程语言都是用 Elixir 本身编写的，具有宏的功能，我们也知道为什么我们喜欢用 Elixir 编写代码，因为它很简单。但是，今天我们要走回去，在黑暗中寻找光明。

我只想让你观察一会儿下面的代码行

```
if 1+2==3, do: :this, else: :that 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/152298a978445792b9b76fe8701a19eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rs1yl9RT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2ANPv3-LyItatGwa0DYAkjCw.png)

在这里，我不会问你这会导致什么。但是，我问你这最终会如何改变。

让我们观察一下这些变化。

[![](img/24b7746916b3786c3565e4a5a7f7aba1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nBpkpA3E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2Audm4XJbvIBVtuyZ_goqkJQ.png)

正如我们所知，elixir 允许函数调用而不需要`()`。这就是函数调用简单的原因。但是，我推荐使用`()`，它更容易理解正在发生的事情。

[![](img/580f73c8206ef67d28ff85004dd4ced4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LxptFBDN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2AZcAq70QUQg2wpF5hMDIYwg.png)

如果你观察上面的线，我们有几个`,`。

[![](img/f2d5d90e955812ef6c191bf4d5eaa6b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k7TlcdPG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2AlJdd24JXmmO3J-PcVGtt6A.png)

如果采用三个参数`1+2==3`、`do: :this`和`else: :that`，这种情况更有可能发生，但实际上是采用**两个**。

[![](img/be5b20e79102288fc08e86ba3c74dd33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4A9GmRpt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2A_qQGBo3-Yigd6qUAzvrXHg.png)

最后两个参数`do: :this`和`else: :that`合起来称为关键字列表。

如果您不知道什么是关键字列表，它可能是一个列表，其中它的列表元素应该是两个元素的元组，其中第一个元素应该是一个`atom`。

在一个句子中，关键字列表是元组的列表，其中元组大小应该是 **two (2)** ，并且元组中的第一个元素是原子。实际上，它是**键-值* *对的**列表**，其中* *键**是一个原子，而**值**可以是任何值。

考虑下面的例子

```
iex> list = [{:a, 1}, {:b, 2}]

[a: 1, b: 2]

iex> list == [a: 1, b: 2]

true 
```

Enter fullscreen mode Exit fullscreen mode

这里`[{:a, 1}, {:b, 2}] == [a: 1, b: 2]`。关键词列表已经够多了。

让我们跳回到我们的代码行

```
if 1+2==3, do: :this, else: :that 
```

Enter fullscreen mode Exit fullscreen mode

如果要传递的最后一个参数是一个关键字列表，我们不需要像通常那样将元素放在方括号内。

`hello("medium", user_name: "blackode", lang: "elixir")`

与相同

`hello("medium", [user_name: "blackode", lang: "elixir"])`

在这里，两者都被认为是有效的。

所以，这一行代码是从
转换而来的

```
if 1+2==3, [do: :this, else: :that] 
```

Enter fullscreen mode Exit fullscreen mode

至

```
if( 1+2==3, [do: :this, else: :that] ) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/67777a72736c6a33921b4286f7231830.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RIkwj63B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2AVQ_qpOhvhl8g1-CLf2j9LA.png)

现在，观察第一个参数`1+2 == 3`，也就是`(1+2)==3`。

[![](img/511d822fd7c46da73da42b77fabe168d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7F2CqWsq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2AcHtg90yygSlTWu2K-ViEJg.png)

[![](img/26ed6446d2600ddc8ecd0664d1f493d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QKpkGBxN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2Auv13Xus0Lve8FN3l2O5biA.png)

这里，在解析`==`之前，我们必须添加`(1+2)`。

`(1+2)`更可能是一个有两个参数的函数。它是一个[中缀](https://en.wikipedia.org/wiki/Infix_notation)符号，而+被称为一个[中缀](https://en.wikipedia.org/wiki/Infix_notation)运算符。

当 **function_name** 位于**中间**时，这是执行某些功能的不同方式。**左* *和* *右* *值分别作为* *第一* *和* *第二**参数传递给函数。

所以，传统的表示语法(`1+2)`是`+(1,2)`。

[![](img/3319da357013719ffcf79fa1b9d89806.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2qcXpmp4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2Aam6xvLObA6laoG5Wj377ug.png)

类似地，我们将另一个中缀操作符`==`分解为`==(+(1,2), 3)`，就像我们对`+`所做的一样，这里没什么特别的。

到目前为止，我们已经分解了`+(1,2)==3`到`==(+(1,2), 3)`。

[![](img/cceb40de22e320c48bad5a38fcb74406.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D4n80PhI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2AcgOT6wr3V7HB-nauJOo91Q.png)

这里，我们不能在没有模块名的情况下使用中缀运算符。所有的操作符都在内核模块中定义。这是 elixir 中的默认模块。

因此，我们再次将`==(+(1,2), 3)`分解为`Kernel.==(Kernel.+(1,2), 3)`

[![](img/246379dc3c8a969bd672493fdecc2486.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RQ0yEncL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2AzzwpKayodY1FT-sBSYzisg.png)

现在，该行被转换为如下

```
if(Kernel.==(Kernel.+(1,2), 3), [do: :this, else: :that]) 
```

Enter fullscreen mode Exit fullscreen mode

我们都知道 if 是一个宏，它更可能被表示为 Kernel.if

因此，将所有这些放在一起，并分解中缀函数，这一行再次转化为

```
Kernel.if( Kernel.==(Kernel.+(1,2), 3), [do: :this, else: :that ] ) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/1db90e620a9b94176039080d98bce7f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iX0lemux--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2A1ywFojdPwfzs99ryu7F5gA.png)

[![live execution in iex](img/9c8e8aed5f60fd9ae35a87768eaddb95.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--YbQsgXh0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3824/1%2AxaDH1bDxvhAVjBwZsMwcZw.png) *在 iex* 现场执行

最棒的是，elixir 编译器会处理所有这些转换。

我们不需要像上面那样写所有的东西。

希望你对酷语法背后的性感有所了解。为了更深入的理解，阅读更多关于 **[宏](http://elixir-lang.github.io/getting-started/meta/macros.html)的内容。**

快乐的长编码生活:)