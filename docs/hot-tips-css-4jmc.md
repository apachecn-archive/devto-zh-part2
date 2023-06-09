# 热门提示 CSS

> 原文：<https://dev.to/equinusocio/hot-tips-css-4jmc>

这里收集了一些 css 片段，你可以在特定的情况下使用，或者只是为了好玩。我们开始吧。

## 图像渲染

```
img.QRcode {
  image-rendering: pixelated;
} 
```

Enter fullscreen mode Exit fullscreen mode

此属性对于渲染 QR 码和图像缩略图以提高其质量非常有用。

* * *

## 在 Safari 11+上运行风格

```
@supports (padding: env(safe-area-inset)) {
  /* Your code for Safari 11+ */
} 
```

Enter fullscreen mode Exit fullscreen mode

`env()`功能和`environment variables`由 Safary 11+实现，支持苹果 iPhone X 屏幕。你可以在这里阅读更多信息[。](https://webkit.org/blog/7929/designing-websites-for-iphone-x/)

* * *

## 检查空节点

```
my-component:empty {
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

当里面没有内容时隐藏你的元素。`Returns`和`white spaces`被认为是内容。

* * *

## 塑造你的文字

```
p {
  shape-outside: polygon(0 0, 0 200px, 300px 600px);
} 
```

Enter fullscreen mode Exit fullscreen mode

更改内容环绕浮动元素的方式。

* * *

## 以普通 SVG 为背景

```
my-component {
  background-image: url('data:image/svg+xml;utf8,...');
} 
```

Enter fullscreen mode Exit fullscreen mode

使用``作为 css 背景，而不将其转换为 base64。

* * *

* * *

> # 本文继续..
> 
> [equinsuocha.io/blog/hot-tips-css](https://equinsuocha.io/blog/hot-tips-css/)

* * *

* * *