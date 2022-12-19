# HTML 和 CSS 初学者的可访问性

> 原文：<https://dev.to/mxl/accessibility-for-beginners-with-html-and-css-16j7>

这是我的#100DaysOfCode Day 2 的帖子，基于我了解到的 freeCodeCamp 材料。

网站应该是开放的，所有人都可以访问，不管用户的能力如何，但经常发生相反的情况。这里有一个关于使用 HTML 和 CSS 提高网站可访问性的快速备忘单。

**1。当图片的内容在阅读文本**
时不明显时，图片应该有替代文本

```
<img src="logo.jpeg" alt="Company logo"> 
```

Enter fullscreen mode Exit fullscreen mode

**2。h1 - h6 文本对屏幕阅读器很重要，保持它们的顺序**

```
<!-- Don't do this -->
<h1>A header</h1>
<h4>A smaller header</h4>
<!-- Do this -->
<h1>A header</h1>
<h2>A smaller header</h2> 
```

Enter fullscreen mode Exit fullscreen mode

**3。通过使用主页、页眉、页脚、导航、文章、章节和音频**
来赋予你的页面结构

```
<header>
  <h1>The header!</h1>
</header>
<main>The document body</main>
<footer></footer> 
```

Enter fullscreen mode Exit fullscreen mode

**4。将文章元素用于博客条目、论坛帖子或新闻文章**

```
<div> - groups content
<section> - groups related content
<article> - groups independent, self-contained content 
```

Enter fullscreen mode Exit fullscreen mode

**5。使用视觉和听觉内容**
，以便有视觉和/或听觉障碍的用户可以访问。

**6。将图元素用于图表**

```
<figure>
  <img src="your_chart.jpeg" alt="Short description of the chart">
  <br>
  <figcaption>
    Description of the chart.
  </figcaption>
</figure> 
```

Enter fullscreen mode Exit fullscreen mode

**7。使用带输入的标签元素**

```
<label for="name">Name:</label>
<input type="text" id="name" name="name"> 
```

Enter fullscreen mode Exit fullscreen mode

**8。分组字段集中的单选按钮**

```
<fieldset>
  <legend>Choose one of these three items:</legend>
  <input id="one" type="radio" name="items" value="one">
  <label for="one">Choice One</label><br>
  <input id="two" type="radio" name="items" value="two">
  <label for="two">Choice Two</label><br>
  <input id="three" type="radio" name="items" value="three">
  <label for="three">Choice Three</label>
</fieldset> 
```

Enter fullscreen mode Exit fullscreen mode

**9。用选择器使用日期字段**

```
<label for="input1">Enter a date:</label>
<input type="date" id="input1" name="input1"> 
```

Enter fullscreen mode Exit fullscreen mode

10。用时间元素
将时间标准化该元素的 datetime 属性是辅助设备访问的值。它通过陈述标准版本的时间来帮助避免混淆，即使它在文本中是以非正式或口语的方式书写的。

```
<time datetime="2013-02-13">last Wednesday</time> 
```

Enter fullscreen mode Exit fullscreen mode

**11。让一些内容仅对屏幕阅读器可见**
像这样:

```
.sr-only {
  position: absolute;
  left: -10000px;
  width: 1px;
  height: 1px;
  top: auto;
  overflow: hidden;
} 
```

Enter fullscreen mode Exit fullscreen mode

不要使用:

*   显示:无；或者能见度:隐藏；它们为每个人隐藏内容，包括屏幕阅读器用户

*   像素大小的零值，如宽度:0px 高度:0px 它会将该元素从文档流中移除，屏幕阅读器会忽略它

**12。使用高对比度文本**
网页内容可访问性指南(WCAG)建议普通文本的对比度至少为 4.5 比 1。该比率通过比较两种颜色的相对亮度值来计算。范围从相同颜色或无对比度的 1:1 到白色对黑色的 21:1。前景色和背景色需要足够的对比度，这样色盲用户才能区分它们。

13。使用描述性链接文本

```
This:
<a href="">information about computers</a>
is better than this:
<a href="">Click here</a> 
```

Enter fullscreen mode Exit fullscreen mode

**14。使用重要链接的访问键**

```
<button accesskey="b">Important Button</button> 
```

Enter fullscreen mode Exit fullscreen mode

15。使用 tabindex 添加键盘焦点
链接和表单控件，当用户浏览页面时自动获得键盘焦点。通过对任何其他元素使用 tabindex="n ",可以将此功能赋予它们，其中 n 不是负的。

```
<a href="" tabindex="1">First accessed link</a>
<a href="" tabindex="2">Second accessed link</a> 
```

Enter fullscreen mode Exit fullscreen mode

在 [Unsplash](https://unsplash.com/) 上由 [Rodion Kutsaev](https://unsplash.com/@frostroomhead) 拍照