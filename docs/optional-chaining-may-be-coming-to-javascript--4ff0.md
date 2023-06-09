# JavaScript 可能会出现可选链接

> 原文：<https://dev.to/sammyisa/optional-chaining-may-be-coming-to-javascript--4ff0>

**更新:**可选链接终于在[阶段 4](https://tc39.github.io/process-document/) ！！这意味着它是 ES2020 规范的一部分。它也已经制作成了 [TypeScript v3.7](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#optional-chaining) 。

这里是 [repo，包含您需要的所有信息](https://github.com/tc39/proposal-optional-chaining)。

## 什么事？

可选链接允许我们在试图访问对象的属性之前检查对象是否存在。其他一些语言也有类似的东西。例如，C#有一个空条件操作符，其行为与建议的可选链接非常相似。

## 我们为什么需要它？

您是否曾经在访问对象或数组的属性之前检查过它们是否存在？如果你忘记了，它可能看起来有点像这样:

```
if(specimen && specimen.arms && specimen.arms.length > 2)
    console.log("This is probably an alien"); 
```

Enter fullscreen mode Exit fullscreen mode

我们做这些检查的原因是因为在 JavaScript 中允许匿名对象不一定有结构或模式。因此，如果我们不在对象树中检查父对象，我们会得到很多类似这样的错误:

[![TypeError error message](img/d2095d54404a35b6a0c90d8acbb877cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WlXFg0V---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/xsxmyDK.png)

因为，至少在错误的情况下，`specimen`确实存在，但是它没有`arms`属性。因此，我们试图从`undefined`中得到`length`。

## 那么，有什么建议呢？

相反，我们可以像这样*链接*可选检查:

```
if(specimen?.arms?.length > 2)
    console.log("This is probably an alien"); 
```

Enter fullscreen mode Exit fullscreen mode

然而，请记住可选的链接操作符是`?.`而不是`?`——这意味着当使用它来访问数组中的一个项时，它将看起来像这样:

```
var firstArm = specimen?.arms?.[0]; //CORRECT
var secondArm = specimen?.arms?[1]; //WRONG 
```

Enter fullscreen mode Exit fullscreen mode

同样，当使用它来检查函数的存在时:

```
var kickPromise = specimen?.kick?.();  //CORRECT
var punchPromise = specimen?.punch?(); //WRONG 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们在作为函数调用它之前检查`kick`是否存在！

## 它是如何工作的？

操作员检查`?.`左侧**的**是`null`还是`undefined`。如果是，那么表达式短路并返回`undefined`。否则表达式继续求值，好像什么都没错。

## 什么时候可以用？

嗯，它仍然是一个提案，所以它还不是普通的 JavaScript。但是，可以和巴别塔一起使用[！](https://github.com/babel/babel/pull/5813)

要了解提案的最新状态，以及更深入的理解和查看一些示例，您应该查看[他们的 GitHub repo](https://github.com/tc39/proposal-optional-chaining) ！你也可以在这里找到[的规格](https://tc39.github.io/proposal-optional-chaining/)，但我不会深入讨论，因为那份文件的大部分内容我都不知道😅

## 谢谢！

欢迎你！❤️