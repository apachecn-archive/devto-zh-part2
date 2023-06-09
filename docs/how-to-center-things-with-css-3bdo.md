# 如何用 CSS 将事物居中

> 原文：<https://dev.to/flaviocopes/how-to-center-things-with-css-3bdo>

在 CSS 中居中是一项非常不同的任务，如果你需要水平或垂直居中。

在这篇文章中，我解释了最常见的情况以及如何解决它们。如果 Flexbox 提供了一个新的解决方案，我会忽略旧的技术，因为我们需要前进，而且 Flexbox 已经被浏览器支持很多年了，包括 IE10。

## 水平居中

### 文本

使用设置为`center` :
的`text-align`属性，文本水平居中非常简单

```
p {
  text-align: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 区块

将非文本内容居中的现代方法是使用 Flexbox:

```
#mysection {
  display: flex;
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

`#mysection`内的任何元素将水平居中。

[![Center horizontally](img/0cc0a5c96eaada877e54c695056e9151.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NwuVjQFq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flaviocopes.com/css-centering/flexbox-horizontal-center.png)

* * *

如果你不想使用 Flexbox，这里有一个替代方法。

任何不是文本的内容都可以通过在左右两边应用自动边距来居中，并设置元素的宽度:

```
section {
  margin-left: 0 auto;
  width: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的`margin-left: 0 auto;`是
的简写

```
section {
  margin-top: 0;
  margin-bottom: 0;
  margin-left: auto;
  margin-right: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果是内联元素，记得将该项设置为`display: block`。

## 垂直居中

传统上，这一直是一项艰巨的任务。Flexbox 现在以最简单的方式为我们提供了一个很好的方法:

```
#mysection {
  display: flex;
  align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

`#mysection`内的任何元素将垂直居中。

[![Center vertically](img/47e9d9cd63cacb83d2f4c73e375af26f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BKUTUs3L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flaviocopes.com/css-centering/flexbox-vertical-center.png)

## 纵横居中

可以将垂直居中和水平居中的技术结合起来，使页面中的元素完全居中。

```
#mysection {
  display: flex;
  align-items: center;
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Center both vertically and horizontally](img/1564245a285abd1dd68a310c87eadb53.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RUX1YL84--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://flaviocopes.com/css-centering/flexbox-center.png)