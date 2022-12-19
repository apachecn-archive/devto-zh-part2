# 使用 JavaScript 承诺非异步控制流？

> 原文：<https://dev.to/dmerand/using-javascript-promises-for-non-async-control-flow--22od>

我喜欢像 Elixir + Elm 这样的函数式语言，它们为控制流提供了管道语法:

```
data
|> first_transformation
|> second_transformation
|> etc 
```

Enter fullscreen mode Exit fullscreen mode

很容易理解这个程序在做什么！我一直在学习 JavaScript 中的承诺是如何工作的，它们似乎是模拟这种数据链的好方法，即使您的函数并没有真正做任何需要异步的事情:

```
first_transformation(data)
.then(second_transformation)
.then(third_transformation)
.catch(handle_errors_from_the_whole_chain) 
```

Enter fullscreen mode Exit fullscreen mode

关于[控制流和错误处理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling)的 MDN 文档表明[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)主要针对异步/延迟操作。仅仅使用承诺作为控制流程和组织的机制正常吗？这是一个可怕的想法吗？