# CSS 自定义属性(FWT 本地)

> 原文：<https://dev.to/_elmahdim/css-custom-properties-native-fwt-2724>

# 简介

样式表由一系列规则组成，许多主题可能包含重复数据；例如，应用程序可以建立一个配色方案并恢复 3+种颜色，或者整个应用程序中各列之间的间距大小。更改这些数据可能会非常痛苦且容易出错。现代 CSS 有一个强大的功能，叫做 CSS 自定义属性(又名 CSS 变量)；这个特点让我们可以更优雅地做事(本土 FWT)。

## 自定义属性`--*`

CSS 变量以`--`为前缀，像`--color-primary`一样，表示包含一个值的自定义属性，该值可以使用`var()`函数符号在整个应用程序中重用。

> 与其他 CSS 属性不同，自定义属性名称区分大小写。

## 用法

声明和使用变量:

```
:root {
  —color-primary: tomato;
}

h1 {
  color: var(—color-primary);
}

button {
  background-color: var(--color-primary);
} 
```

一个常见的用例是在`:root`伪类中定义变量，然后在需要值的任何地方使用它来停止一些重复实例。

## 自定义属性回退值

使用`var()`函数符号，当给定变量尚未定义时，您可以定义多个回退值。该函数的第一个参数是要替换的自定义属性(CSS 变量)的名称。该函数的第二个参数(如果提供)是一个回退值，当引用的自定义属性无效时，该值用作替换值。

示例:Coral 将是 h2 标签的颜色值，因为`--color-secondary`未定义

```
h2 {
  color: var(—color-secondary, coral);
} 
```

示例:黑色是边框颜色值，因为`--color-main`和`--color-accent`都没有定义

```
blockquote {
  border-left-color: var(--color-main, var(--color-accent, black));
} 
```

示例:无效的参数数目

```
footer {
  background-color: var(--color-a, --color-b, red);
} 
```

* * *

## 演示

对于这篇文章，我准备了两个例子:国际化(CSS 变量用法的真实例子)和主题管理；都可以在这里找到[(原文)](https://elmahdim.com/css-custom-properties/#examples)

* * *

## 浏览器兼容性

[![css variables](img/0d9ce70cd06d691c7530507546f8fff9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QsUFipGp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2anpkgfy6ysjjmp7ox3t.png)

更多详情可在[这里找到(can use)](https://caniuse.com/#search=CSS%20Variables)