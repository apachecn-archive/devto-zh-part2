# 关于快速管道的快速解释(->和|。理性地

> 原文：<https://dev.to/mrmurphy/a-quick-explanation-about---and--in-reasonml-5329>

# 简介

ReasonML 中有一个简洁的操作符，叫做“快速管道”，看起来像这样:`->`或者像这样`|.`。当调用以类型作为第一个参数的函数时，它允许一个很好的可视化流程(如果我们使用面向对象的语言，这些函数可能被定义为对象上的方法)。

# 为什么是两种语法？

ReasonML `3.3.x`的新版本不赞成使用`|.`作为快速管道操作符，而倾向于使用瘦箭头语法:`->`。我们可能会在文档和示例中看到它们。

# 它到底是做什么的？

快速管道获取操作符左侧的所有内容，并将该表达式的结果作为操作符右侧函数的第一个参数。

这有点类似于在面向对象语言中对对象调用方法的感觉。

例如，如果我们在 Python 中有一个带方法`greet`的类`Person`，它将消息作为字符串，我们可以这样调用它:

`personA.greet("hello")`。

合理地说，实践是创建一个`person`类型，但是保留任何在`person`类型上工作的函数作为独立的函数，将`person`类型作为第一个参数。大概是这样:

```
type person;
let greet = (person, string) => string; 
```

Enter fullscreen mode Exit fullscreen mode

请看上面的内容，`greet`接受一个人和一个字符串，然后返回另一个字符串，大概是问候那个人。

为了合理地调用这个*而不使用*快速管道，我们可以这样做:

```
let greeting = greet(personA, "hello"); 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们想感觉到`greet`更像是一个方法而不是一个独立的函数，我们可以使用快速管道操作符:

```
let greeting = personA->greet("hello");
/* or */
let greeting = personA|.greet("hello"); 
```

Enter fullscreen mode Exit fullscreen mode

看到左边的东西(人物角色)是如何作为右边函数的第一个参数被应用的了吗？

这似乎没什么用。但是一旦我们开始链接函数调用，我们就真正开始看到它的美妙之处:

```
maybeSomething->Belt.Option.map(a => a)->Belt.Option.getExn 
```

Enter fullscreen mode Exit fullscreen mode

我们最终得到一个调用结构，看起来有点像“ol JQuery 方法链模式”!当处理大量包装 Javascript 库的代码时，这非常方便，因为许多 JS 库大量使用对象方法。

# 总结

“快速管道”是一种方便地调用以对象(或类型)作为第一个参数的函数的方法。首选语法是`->`，但也以`|.`的形式存在。快速管道对于“Belt”标准模块中的 API 非常有用，并且在包装面向对象的 Javascript APIs 时非常有用。