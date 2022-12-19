# accesskey

> 原文：<https://dev.to/kahlil/accesskey-37ik>

在致力于改进应用程序的可访问性特性时，我了解到一个我以前不知道的 HTML 特性:[`accesskey`属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/accesskey)。

属性允许您向元素添加键盘快捷键。

为了分配一个键盘快捷键，你要给一个元素添加`accesskey`属性，并给它分配一个字母。比如字母“a”，像这样:

```
<div accesskey="a"> ... </div> 
```

Enter fullscreen mode Exit fullscreen mode

这将自动将一个快捷方式绑定到该元素，该元素要么获得焦点，要么触发一个单击事件。

实际的快捷方式因浏览器和平台而异，但一般来说，在现代浏览器中，Windows 和 Linux 上的快捷方式是`alt` + `a`，Mac 上的快捷方式是`alt` + `ctrl` + `a`，Firefox 上的快捷方式是`alt` + `shift` + `a`。