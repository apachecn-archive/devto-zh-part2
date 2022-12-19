# TIL: Element.prototype.matches 可以用来检查一个元素是否包含某个类

> 原文：<https://dev.to/stefanjudis/til-elementprototypematches-can-be-used-to-check-if-an-element-includes-a-certain-class-49cg>

在构建接口时，判断一个元素是否包含一个特定的类是非常常见的操作。今天我看到了[大卫·吉尔伯森](https://twitter.com/D__Gilbertson)写的一篇文章，这篇文章描述了[“你可能从未听说过的 15 种 HTML 元素方法”](https://hackernoon.com/15-html-element-methods-youve-potentially-never-heard-of-fc6863e41b2a)，这篇文章向我介绍了`Element.prototype.matches`。这个元素方法可以用来检查一个元素是否包含某个类，并且比`element.classList.contains`短多少。🎉

```
const elem = querySelector('.foo');

elem.classList.contains('bar'); // true
elem.matches('.bar');           // true 
```

Enter fullscreen mode Exit fullscreen mode