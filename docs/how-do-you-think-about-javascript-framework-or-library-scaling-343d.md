# 如何看待 javascript 框架(或库)缩放？

> 原文：<https://dev.to/terrierscript/how-do-you-think-about-javascript-framework-or-library-scaling-343d>

我是日本开发商。
在日本，`Vue.js`的知名度越来越高。

我有在生产环境中用`React`创建一些 web 应用程序的经验(有时用`Redux`)。

最近我也用了`Vue.js`。

在我用`Vue.js`创建应用程序的时候，就想到了这一点。

"多人或大规模代码不容易创建(和维护)吗？"

我觉得`React`具有良好的可扩展性和健壮性。
`Redux`有一些乏味之处(冗余，样板文件),但是它使得即使任何人编写它也将是类似的代码。
另外，`React`对类型化系统(比如 flow 或者 typescript)是如此友好。

非常容易设置，它提供了一些实现模式，比如$computed，$el，mixin，emit，`Vue.use`，directive 等等。
这很灵活，但是如果有很多开发人员的话，会使团队变得混乱。
我觉得如果我们和多人一起开发`Vue.js`，我们必须制定很多规则。

不知道`Angular`怎么样，感觉大规模好但是小规模应用很难。

你如何看待 javascript 应用程序在生产中的伸缩性？如果可以，请告诉我您生成的生产应用程序的规模(代码大小、开发人员数量等..).