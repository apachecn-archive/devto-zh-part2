# 理解伪元素前后的 CSS

> 原文：<https://dev.to/finallynero/understanding-css-before-and-after-pseudo-elements-ml0>

当我开始学习前端开发时，我真的很讨厌 CSS 和设计(我仍然讨厌设计)，但我的 CSS 稍微好一点。我不理解的一个概念是 CSS 伪元素`::after`和`::before`。

在这篇文章中，我将解释什么是伪元素，并展示一些如何使用它们的例子。

### 什么是伪元素？

伪元素允许你创建/定义不在 DOM 上的元素。它们允许您对元素内容的特定部分进行样式化。就文档语言而言，伪元素没有元素类型，因为它们根本不存在于 DOM 中。并且只能使用 CSS 创建。伪元素的奇妙之处在于，它们允许你在不添加额外标记的情况下为网站添加额外的样式。

一些伪元素是:

*   `::first-letter`(原`:first-letter`)
*   `::first-line`(原`:first-line`)
*   `::before`(原`:before`)
*   `::after`(原`:after`)

最初伪元素是使用单冒号惯例(即`:before`)生成的，但是 CSS3 引入了一个新惯例，单冒号用于伪类，如`hover`、`active`等，双冒号用于伪元素。这是为了区分伪类和伪元素而引入的。
点击阅读更多关于 CSS3 规范[的信息。
浏览器仍然支持`:before`和`:after`，但是建议使用双冒号。](https://www.w3.org/TR/selectors-3/)

#### 在伪元素之前

`Before`伪元素在所选元素的第一个子元素之前创建一个伪元素，简单地说，它在所选元素的内容之前创建一个元素。
伪元素有一个 content 属性，可以用来插入生成的内容、图片或者字符串。它们必须具有 CSS 内容属性，否则内容的默认值将设置为 none。

下面是一些`::before`伪元素的简单演示。

[https://codepen.io/finallynero/embed/oQLjVe?height=600&default-tab=result&embed-version=2](https://codepen.io/finallynero/embed/oQLjVe?height=600&default-tab=result&embed-version=2)

在演示的第一个例子中，我们使用了`::before`在`p tag`前添加了一个字符串，并将其样式化为普通的 Html 元素。
中的第二个`::before`是用来给列表添加子弹箭头的，子弹箭头是一个字体牛逼的图标。

#### 在伪元素之后

`after`伪元素与`before`非常相似，唯一的区别是它的位置在所选元素的最后一个子元素之后/所选元素的内容之后。

[https://codepen.io/finallynero/embed/KrNqZR?height=600&default-tab=result&embed-version=2](https://codepen.io/finallynero/embed/KrNqZR?height=600&default-tab=result&embed-version=2)

第一个例子使用`label`选择器上的`::after`用星号表示输入字段是必需的。而第二个例子显示了如何向链接添加外部链接图标。

也可以组合伪元素。注意:根据 CSS3 规范，每个选择器只能出现一个伪元素，但是您可以在同一个选择器上调用伪元素两次。例如

```
 selector::before{
    .....   
 }

 selector::after{
    .....   
 } 
```

Enter fullscreen mode Exit fullscreen mode

下面是演示这一点的简单演示

[https://codepen.io/finallynero/embed/NEbgJM?height=600&default-tab=result&embed-version=2](https://codepen.io/finallynero/embed/NEbgJM?height=600&default-tab=result&embed-version=2)

我们使用伪元素给句子添加引号。

这篇文章只是简单的解释了什么是伪元素以及它们做什么，但是它们比上面演示的要强大的多。

如果你有任何建议或看到文章中的错误，请在下面留下评论。