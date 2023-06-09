# 如何在 JavaScript 中获取 CSS 值

> 原文：<https://dev.to/zellwk/how-to-get-css-values-in-javascript-32ej>

有时候光靠 CSS 是不够的。您可能需要用 JavaScript 控制 CSS 值。但是如何在 JavaScript 中获取 CSS 值呢？

事实证明，有两种可能的方法，这取决于您是想获得内联样式还是计算样式。

## 获取内嵌样式

内联样式是存在于 HTML 的`style`属性中的样式。

```
<div class="element" style="font-size: 2em; color: red;">Red hot chili pepper!</div> 
```

Enter fullscreen mode Exit fullscreen mode

要获得内联样式，可以使用`style`属性。

```
const element = document.querySelector('.element');

const fontSize = element.style.fontSize;
console.log(fontSize); // 2em

const color = element.style.color;
console.log(color); // red 
```

Enter fullscreen mode Exit fullscreen mode

## 获取计算样式

如果您的样式是在 CSS 文件中编写的，您需要获得计算的样式。为此，您可以使用`getComputedStyle`。

它接受两个值:

```
const style = getComputedStyle(Element, pseudoElement); 
```

Enter fullscreen mode Exit fullscreen mode

这里的`Element`是指您用`querySelector`选择的元素。

`pseudoElement`这里指的是你试图获取的伪元素的字符串(如果有的话)。如果不选择伪元素，可以省略该值。

让我们通过一个例子来帮助理解事情。假设你有下面的 HTML 和 CSS:

```
<div class="element"> This is my element </div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.element {
  background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，你需要用`querySelector`选择元素。然后，使用`getComputedStyle`获取元素的样式。

```
const element = document.querySelector('.element');
const style = getComputedStyle(element); 
```

Enter fullscreen mode Exit fullscreen mode

如果您登录`style`，您应该看到一个包含每个 CSS 属性及其各自值的对象。

[![`getComputedStyle` returns an object that contains every CSS property and their respective values](img/b2add2d1f77b6e2c0e724621629b7ca0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wwzs0xCP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/get-css/all-properties.png) 

<figure>

<figcaption>`getComputedStyle`返回包含每个 CSS 属性及其各自值的对象</figcaption>

</figure>

你也可以在 Chrome 和 Firefox 的开发工具中看到这个对象。

对于 Firefox 开发工具，请查看“检查器”、“计算的”下。

[![Firefox dev tools computed tab](img/13627b7387f1d19f58d4f8dc7e4fbbee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mjgZ4cXm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/get-css/firefox.png)

<figure>

<figcaption>Firefox dev tools computed tab</figcaption>

</figure>

对于 Chrome 开发工具，请在“元素”下查找。如果 dev tools 窗口很大，您可以在右侧面板上看到计算出的样式。如果开发工具窗口很小，你可以在“计算”标签下查看。

[![Chrome dev tools computed tab](img/af0e1ecf4523867ef99c2329f7b3ca72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EUQXsf0T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/get-css/chrome.png)

<figure>

<figcaption>Chrome dev tools computed tab</figcaption>

</figure>

要获取 CSS 属性的值，可以用 camel case 编写属性。

```
const style = getComputedStyle(element);

const backgroundColor = style.backgroundColor;
console.log(backgroundColor); // rgb(0, 0, 0) 
```

Enter fullscreen mode Exit fullscreen mode

注意:`getComputedStyle`为只读。不能用`getComputedStyle`设置 CSS 值。

Note2: `getComputedStyle`获取计算出的 CSS 值。你会从`getComputedStyle`那里得到`px`，而不是像`em`和`rem`这样的相对单位。

## 从伪元素获取样式

要从伪元素中获取样式，需要将伪元素的字符串作为第二个参数传递给`getComputedStyle`。

```
<div class="element"> This is my element </div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.element {
  background-color: red;
}
.element::before {
  content: 'Before pseudo element';
} 
```

Enter fullscreen mode Exit fullscreen mode

```
const element = document.querySelector('.element');
pseudoElementStyle = getComputedStyle(element, '::before');

console.log(pseudoElementStyle.content); // Before pseudo element 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

您可以通过两种方法在 JavaScript 中获取 CSS 值:

1.  `style`属性
2.  `getComputedStyle`。

`style`属性只检索内联的 CSS 值，而`getComputedStyle`样式检索计算的 CSS 值。

如果这一课对你有帮助，你可能会喜欢[学习 JavaScript](https://learnjavascript.today) ，在这里你将学习如何从头开始构建任何你想要的东西。2018 年 7 月开启学习 JavaScript 的报名(下周！).

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。