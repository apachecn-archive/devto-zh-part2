# 什么是继承？

> 原文：<https://dev.to/flippedcoding/what-is-inheritance-3fk6>

如果你已经进入了编程的世界，你可能听说过术语*继承*。出于某种原因，这个术语对人们来说很难解释，但很容易理解。所以这里有一个对继承的解释，实际上是有意义的。

想想你自己和你的父母。很可能你听说过你有一些和你父母非常相似的特征。也许你有你爸爸的眼睛和你妈妈的鼻子。这些都是遗传的特征。你有，因为你父母有。下面是这种继承的图像:

[![parental inheritance](../Images/75c11a796fba09b3629342714ef13bfa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P8LH0B1V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/531ly2yj11aez4jt8k1y.JPG)

编程也是一样。您可以拥有一个从另一个类获取属性和方法的类。假设你有一个描述汽车的类。它具有车门数量、引擎类型以及品牌和型号的属性。现在，假设您想要创建一个新类，该类只描述奥迪生产的汽车。你仍然需要知道汽车有多少门，以及它们的引擎类型和型号。

为了节省时间，您可以从 Audi 类中的 car 类继承那些属性。这是一张看起来像什么的图片:

[![inheriting properties from a class](../Images/3f95328ceaa6ea0a0eb5a651c005fabf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0oLRs03e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ydextr0gno8sdjx3p40m.JPG)

你可以看到继承非常简单。如果您想在类中使用某些属性或方法，您可以从现有的类中获取它们。当您使用继承创建一个新类时，这个新类称为派生类。那是因为它使用了其他类的一些片段。现有的类称为超类。超类是不从任何其他类继承的类。

派生类的美妙之处在于，您可以始终保持超类的最新状态，因为您对超类所做的任何更改都会自动应用到派生类中。派生类之所以漂亮的另一个原因是，它们可以拥有所有的超类属性，并拥有自己的属性和方法。

就像你和你父母一样。当然，你们有一些相似的特点，但你仍然有自己独特的特点。你可能很高，而你的父母都很矮。你可能比你的父母更外向。不管是什么，你有别人没有的东西。这同样适用于班级。

例如，在 Audi 类中，您可能希望包含汽车的年份和计算每加仑行驶多少英里的方法。你可以在不改变汽车类别的情况下包含这些内容。你所要做的就是把它们包含在奥迪类中，就大功告成了。这并没有带走任何继承的属性，它们是奥迪类独有的。这里有一个例子:

[![derived class properties](../Images/8790f182b95d0903d29428d27e710093.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YGFiPQY6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xwaigvenwr8nr7v8di23.JPG)

请记住，继承意味着您可以在一个类中使用另一个类中的所有属性和方法。您对超类所做的任何更改都将被所有的派生类继承。此外，派生类可以有自己独特的属性和方法。

感谢阅读，我希望这是有意义的继承！

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)