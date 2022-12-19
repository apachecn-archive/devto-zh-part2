# 获得编译器帮助，即使在 ReasonML 中有“stringly”类型的参数

> 原文：<https://dev.to/splodingsocks/get-compiler-help-even-with-stringly-typed-parameters-in-reasonml-438k>

有时类型不足以帮助编译器帮助你。有时您需要将函数参数从一个字符串更改为另一个字符串(例如，您突然想要一个用户的 ID，而不是他们的名字)。语义完全不同，但是类型系统无法帮助您传递正确的内容，您只能自己寻找代码库，搜索函数的每次调用，并希望您不会得到失败的请求，因为您将“Sally Mae”而不是“aw 3432 ref q 23 f 23 qfq 23 f”传递给了用户服务(什么，您的用户 id 看起来不像这样吗？)

例如，假设旧函数是这样的:

```
let getUser = (userName: string): Js.Promise.t(Js.Json.t) => {
    UserService.getUser(userName)   
}; 
```

但是现在你需要它看起来像这样:

```
let getUser = (userId: string): Js.Promise.t(Js.Json.t) => {
    UserService.getUser(userId) 
}; 
```

啊哦！唯一改变的是将“userName”参数的名称改为“userId”。你保存文件，编译器报告 0 错误，你出货，应用崩溃。

在这种情况下，我们可以使用 ReasonML 的标签参数来告诉编译器我们需要改变这个函数的所有调用。

```
let getUser = (~userId: string): Js.Promise.t(Js.Json.t) => {
    UserService.getUser(userId) 
}; 
```

看到你扔在那里的那个小东西了吗？在函数名的开头添加一个波浪符号(多么奇怪的词)要求任何函数调用方在调用时指定该参数的正确名称，这是由编译器强制执行的。现在在调用点，看起来像这样:

```
getUser(user.name); 
```

您会得到一个编译器错误！每个调用点都会给你一个编译错误。当终端引导您通过 263 个错误时，您的脸上露出了苦笑，每个错误只需将代码改为:
即可修复

```
getUser(~userId=user.id); 
```

除了您可能在某个调用点的范围内没有用户，并且您必须在接下来的三个月中重构您的应用程序代码来获得 userId，而不是用户名。没关系，这是常有的事。

但是，编译器最终报告了 0 个错误。你发布你的应用。你没有抱怨，你和你最好的朋友去海滩游泳。🏝

玩得开心！