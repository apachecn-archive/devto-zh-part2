# 什么是三进制？

> 原文：<https://dev.to/mcarpenter/what-in-the-if-is-a-ternary-4l1l>

作为 Javascript 语法的“新手”,有时会有些不知所措。首先，你可以用 10 种不同的方法解决任何一个问题。这意味着，您可以用几种不同的方式编写语法来获得相同的结果。这就是为什么在我的最新发现(条件三元运算符)上，它提示:(我写这篇文章)。

*让我们潜入更深的地方*

## 究竟什么是三进制？

条件(三元)操作符是唯一接受三个操作数的 JavaScript 操作符。该运算符常用作 if 语句的快捷方式。

*三元语法*

```
condition ? expr1 : expr2 
```

Enter fullscreen mode Exit fullscreen mode

如果`condition`为`true`，则运算符返回`expr1`的值；否则返回`expr2`的值

让我们来看一些例子 *IF/ELSE*

```
let age = 16;

if(age > 18){
console.log('You meet the requirement');
}else {
console.log('Not yet!');
} 
```

Enter fullscreen mode Exit fullscreen mode

同样的例子；修订后的

```
let age = 16;

let ageRequirement = age > 18 ? 'You meet the requirement' : 'Not yet!';
console.log(ageRequirement); // 'Not yet!' 
```

Enter fullscreen mode Exit fullscreen mode

三元组也可以有多个条件和表达式。

# 结论

这显然是一个非常基本的解释如何三元的工作。我的目标是让像我一样的 Javascript 新手有所了解。[这里的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)是 MDN 上的一个很好的资源，它非常详细地解释了如何在 Javascript 代码中有效地使用三元组。