# 一个有用的进度条回退到 IE9。仅限 CSS。

> 原文：<https://dev.to/ayrtonvwf/a-useful-progress-bar-fallback-to-ie9-css-only-14o0>

进度条是很难设计的元素。

是的，完全有可能只用 CSS 就让它们在跨浏览器中看起来很漂亮，正如这篇 CSS-TRICKS 帖子向我们展示的那样。但是让它们看起来漂亮并不是本文的重点。

本文的重点是让它们对那些本身不理解`progress`元素，并且不能显示可见进度条的浏览器有用。

而且很简单。

让我们来看看一个基本的`progress`标记:

```
<progress min="0" max="100" value="15"></progress> 
```

Enter fullscreen mode Exit fullscreen mode

对于不理解`progress`含义的浏览器来说，它只是一个带有属性`min="0" max="100" value="15"`的 [HTMLUnknownElement](https://html.spec.whatwg.org/multipage/dom.html#htmlunknownelement) 。我们能用它做什么？

*   使用`:before`和`:after`将文本 *15/100* 注入其中；

```
/* make it look like a box */
progress {
  border: 1px solid black;
  line-height: 30px;
}  

/* make pseudo-elements 50% side-by-side */
progress:before, progress:after {
  display: inline-block;
  width: 50%;
}

/* put the value and a slash on the left half, aligned to the right */
progress:before {
  content: attr(value) '/';
  text-align: right;
}

/* put the max value on the right half */
progress:after {
  content: attr(max);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   如果我们确定我们的进度元素的最大值是 100，我们可以用`%`来显示它:

```
/* make it look like a box */
progress {
  border: 1px solid black;
  line-height: 30px;
}

/* put the value and the percent sign in the middle of the pseudo-element */
progress:before {
  content: attr(value) '%';
  display: block;
  text-align: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是两种情况的简单演示(用`div`代替`progress` ):
[https://codepen.io/ayrtonvwf/embed/yRPGWm?height=600&default-tab=result&embed-version=2](https://codepen.io/ayrtonvwf/embed/yRPGWm?height=600&default-tab=result&embed-version=2)

这是应用于 CSS-TRICKS post 的[中漂亮的全风格进度条的概念。【T2](https://css-tricks.com/html5-progress-element)[https://codepen.io/ayrtonvwf/embed/JZWENR?height=600&default-tab=result&embed-version=2](https://codepen.io/ayrtonvwf/embed/JZWENR?height=600&default-tab=result&embed-version=2)