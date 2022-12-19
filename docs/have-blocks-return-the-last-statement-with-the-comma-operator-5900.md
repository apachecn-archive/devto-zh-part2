# 让块使用逗号运算符返回最后一条语句

> 原文：<https://dev.to/nektro/have-blocks-return-the-last-statement-with-the-comma-operator-5900>

一些语言允许一种语法，该语法允许块自动返回最后一条语句。

```
{ 
  System.print("one")
  System.print("two")
  System.print("three")
  2 + 4
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在 JavaScript 中使用[逗号操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comma_Operator)来实现这一点。

```
const doSomethings = () => (
    console.log(location.href),
    global.variable += 4,
    12 / 2
); 
```

Enter fullscreen mode Exit fullscreen mode

就这样，上面的两个代码块将返回相同的东西！