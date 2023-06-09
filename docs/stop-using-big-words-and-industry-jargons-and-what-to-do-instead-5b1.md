# 停止使用大词和行业术语(以及该怎么做)

> 原文：<https://dev.to/zellwk/stop-using-big-words-and-industry-jargons-and-what-to-do-instead-5b1>

假设你想教一个人一些东西。为什么这个人不明白你在说什么？

其中一个主要原因是因为我们喜欢使用大词和行业术语。这些术语可能对我们有意义，但对我们要教的人来说毫无意义。

下次你试图教编程时，要注意你使用的词语。

## 三类大词

术语可以分为三类:

1.  那些可以用几句话解释的
2.  那些无法用简单的语言解释的
3.  这些词在不同的语境中有不同的含义。

当你教书的时候，你应该经常注意这三种类型的单词。

## 三言两语可以解释清楚的行话。

如果行话可以用几句话来解释，你想用这些话来代替行话。

互操作性就是这样一个词的例子。

这听起来很吓人，也很复杂，但可以用简单的几句话来解释。

如果您搜索互操作性的含义，您会看到如下定义:

来自维基百科:“*互操作性是一个产品或系统的特征，其接口被完全理解，以在当前或未来与其他产品或系统一起工作，在实现或访问方面没有任何限制。*

来自 dictionary . com:“*互操作性是在不同的计算机系统之间共享数据的能力，尤其是在不同的机器上。*

简单来说，“互操作性”就是“共享数据的能力”。

看到它是如何让语言障碍降低很多的了吧？

如果可以用简单的词代替这样的行话，为什么还要坚持难的词呢？

## 在不同上下文中表示不同意思的术语

一些术语在不同的上下文中有不同的意思。

这种行话的一个例子是封装。

封装某物的意思是用另一种东西把它包起来。如果你用布包裹土豆，你可以说布包裹了土豆。

开发人员喜欢封装这个词。他们一直在使用它。

第一种方法是将变量和其他代码包装在函数中。在这种情况下，函数将代码封装在。

```
function someFunction () {
  const variableName = 'I am a variable!'
} 
```

Enter fullscreen mode Exit fullscreen mode

第二种方式是包含一个对象的个性。例如，如果你有一个人类对象，你从这个人类对象创建了两个人类，这两个人类不应该是同一个。

在这种情况下，每个对象封装自己的数据。

```
function Human (name) {
  this.name = name
}

const zell = new Human('Zell')
const vincy = new Human('Vincy')

zell.name === vincy.name // false 
```

Enter fullscreen mode Exit fullscreen mode

第三种方法是信息隐藏。在 JavaScript 中，我们可以创建私有变量。这些私有变量由对象括起来。

在这种情况下，对象封装私有变量。您不能访问私有变量。在这种情况下，封装用来表示与第二种情况略有不同的东西。

```
function Human () {
  const privateVariable = 'private'
  this.publicVariable = 'public'
} 
```

Enter fullscreen mode Exit fullscreen mode

那么你所理解的封装是什么？

你不能确定。

你们交流的时候不应该有歧义。如果有歧义，交流就中断了，学生就学不到东西。

如果行话在不同的上下文中有不同的意思，最好抛弃行话。

## 无法用简单文字解释的行话

有些术语不能用简单的词语来解释。这些术语经常被用来谈论抽象的概念，这就是为什么简单的词语可能不够。

这个词的一个例子是“突变”。

突变来源于 mutate 这个词。突变意味着形式或性质的改变。在 JavaScript 中，突变发生在你不注意的情况下。

在这种情况下，变化不足以解释突变。它缺乏深度和细节。另外，改变还是太抽象了。

你觉得一个概念是抽象的，因为你无法想象它。你看不到，听不到，感觉不到，摸不到，也尝不到。为了使抽象具体化，我们需要诉诸人类的五种感官。

要解释一个抽象的概念，可以用类比。当你使用类比时，你可以用一种人们能看到、听到或感觉到你的意思的方式来描述一个物体或一个场景。

比如[我在解释突变](https://alistapart.com/article/why-mutation-can-be-scary)的时候用 x 战警做类比。

我让学生们想象一个朋友在他们眼前长出皮毛并变成蓝色。任何人都可以想象长出皮毛变成蓝色意味着什么，即使不知道野兽是谁。

如果你想扩大类比以迎合更多的人，你可以诉诸更多的感官。例如，让盲人想象突变，你也可以告诉他们想象他们的朋友像野兽一样咆哮。

这里的关键是一个未被发现的变化。没有人知道一个人是否是变种人，直到他们展示他们的力量。同样，在 JavaScript 对象发生变化之前，没有人知道它发生了变化。

我强调这一点是为了追溯 JavaScript 中的突变。

一旦联系建立起来，变异就变成了具体的东西。当我说突变时，读过这篇文章的学生可以想象他们的朋友变成蓝色，长出皮毛，像野兽一样咆哮。

一旦你把一个抽象的行话变成了一个具体的概念，你就可以正常使用这些行话了。学生会明白你的意思。

我写了一篇关于[创造好的类比](https://zellwk.com/blog/creating-good-analogies)的文章，如果你有兴趣学习这项技能的话。

## 包装完毕

教编程的时候要注意用词。如果你使用对学生来说没有任何意义的难词，他们就不能理解你的意思。

如果可以的话，用更简单更容易理解的词代替难懂的词。

避免使用在不同上下文中可能有不同含义的术语。这些术语使事情变得模糊不清。

最后，用类比把抽象的概念变成具体的概念。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。