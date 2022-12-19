# 快速 CSS 测验#4

> 原文：<https://dev.to/ismail9k/quick-css-quiz-4-3ab8>

在这个系列中，我试图将重点放在 CSS 怪异的部分，抛出 quiz 及其答案，希望能更深入地了解 CSS 是如何工作的。

## 问题:

```
<div class="wrapper">
  <h1   id="header" class="is-red">Red text</h1>
  <span id="span"   class="is-blue">Blue text</span>
  <p    id="text"   class="is-green">Green text</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
#header.is-red {
  color: red;
}
span.is-blue {
  color: blue;
}
.is-green {
  color: green;
} 
```

Enter fullscreen mode Exit fullscreen mode

前面的代码片段中，三个不同的 HTML 元素包装在一个 div `.wrapper`中，每个元素都有不同的颜色，如样式片段中所指定的，如果您在样式表的末尾添加以下 CSS 代码，那么 header、span 和 paragraph 元素的颜色会是什么呢？

```
.wrapper :not(#happy) {
  color: black;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 选项:

*   标题为红色，跨度为蓝色，段落为绿色
*   页眉为红色，跨度为蓝色，段落为黑色
*   页眉为红色，跨度为黑色，段落为黑色
*   所有元素都是黑色的

## 答案:

你可以在下面找到答案，但请给自己几分钟时间思考一下，以确保你从这个测验中受益。

* * *

* * *

* * *

* * *

* * *

## T2】👇👇👇

* * *

* * *

* * *

* * *

正确答案是:**所有元素都是黑色的**
如果你想知道为什么这是正确答案，请阅读下面的讨论..