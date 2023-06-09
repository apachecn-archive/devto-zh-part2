# 删除列表中的内容

> 原文：<https://dev.to/robinheghan/de-throning-the-list-2fjk>

听着，我有个计划。你会问，什么样的计划？一个狡猾的家伙。像狐狸一样狡猾？是的，我会这么说。像猫一样狡猾，它训练一个人在铃响时给它送食物？是...不。我是说，这很聪明。让我们说，我有一个想法，可能是一个非常好的想法:

我想删除列表。

## 听我说完

每种编程语言都有一个通用的集合类型。大多数非函数式语言，如 Javascript 和 Java，都使用数组。然而，在函数式语言中，列表并不罕见。这是为什么呢？

在很大程度上，我怀疑这是历史原因。高效的不可变数组是最近的发明，最早出现在 2007 年的 Clojure 中。流行的函数式语言，如 Haskell、OCaml 和 Common Lisp 都来自 80 年代和 90 年代，所以当时根本没有。

此外，这个列表很容易实现和掌握。它确实有一些强大的套件。例如，如果您想要访问第一个元素，删除它或者甚至添加一个新的第一个元素，您可以在恒定时间内这样做。如您所料，您还可以在线性时间内从左到右遍历集合。

但是它也有一些令人难以接受的缺点。如果您想要检索或修改除第一个元素之外的任何元素，您将在线性时间内完成(讨厌)。如果你想从右到左迭代条目，当你执行`map`或`filter`时，你必须先反转列表(是的，真的！).

另外要提到的是`List`并不真正适合浏览器环境。我们来看看`[1, 2]`是怎么编译下来 JS:

```
{ ctor: '::', a: 1, b: { ctor: '::', a: 2, b: { ctor: '[]' } } } 
```

Enter fullscreen mode Exit fullscreen mode

但是这有一个问题。一旦列表变得足够大，浏览器(或者至少是 Chrome)就会因为堆栈溢出异常而崩溃。所以在 Elm 0.19 中，相同的列表编译成:

```
<core_namespace>$ToList([1, 2]) 
```

Enter fullscreen mode Exit fullscreen mode

这要短得多，而且会缩小得很漂亮，但也意味着应用程序中的每个列表文字在运行时都有额外的开销。内联的`Array`不会有这些问题。

由于我刚才提到的问题，我认为`List`不适合作为 Elm 中的默认集合类型。我认为`Array`更适合这个角色，特别是考虑到它的工作方式更像人们习惯的 Javascript。

## 所以我们就把`List`换成`Array`，问题解决了！

嗯，没那么快。

首先，`Array`的实现细节隐藏在一个不透明的类型中。这样做是有充分理由的，因为`Array`是一个很难处理的数据结构。但这也意味着有些事情你无法用`Array`有效地完成，这些事情用`List`来做是微不足道的，比如过滤元素，直到你找到一定的数量。

`Array`似乎也不擅长`List`擅长的所有事情。例如，如果你想在一个`List`的开头插入或删除一个元素，这是一个常量时间操作。用一个`Array`做同样的事情需要重新构建整个数据结构，相比之下这非常慢。

最后，有些操作是为`List`而不是`Array`实现的，比如`sort`。

## 那我们就只能用`List`了？

就是这么回事。我不认为我们是。我相信我提到的所有问题都可以解决，这就是我的狡猾计划的全部内容。

在接下来的几周里，我会详细解释我的计划。将会有基准，代码片段，甚至可能是另一个参考黑爵士。这个计划虽然险恶，但确实需要大量的工作，所以我不指望它能很快实施。

下次请收听[，看看我们如何让`Array`更加灵活。](https://dev.to/skinney/de-throning-the-list-part-deux-4idm)