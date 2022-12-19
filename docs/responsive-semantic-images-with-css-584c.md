# 带有 CSS 的响应性语义图像

> 原文：<https://dev.to/hugo__df/responsive-semantic-images-with-css-584c>

> CSS 提示:`object-fit`你的图片。

为了让一个图像不试图拉伸到它的宽度/高度，经典的 CSS 如下:

```
.thumbnail {
  width: 50px;
  height: 50px;
  background-size: cover;
  background-position: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个关联的 HTML(内嵌样式…👎):

```
<div
  class="thumbnail"
  style="background-image: url('some-url');"
>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以做:

```
.thumbnail {
  width: 50px;
  height: 50px;
  object-fit: cover;
  object-position: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

用下面的 HTML:

```
<img
  class="thumbnail"
  alt="My thumbnail"
  src="some-url"> 
```

Enter fullscreen mode Exit fullscreen mode

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

# 为什么这个很酷？

1.  一个词:可及性。
2.  两个字:语义标记。
3.  很多话，用`div` + `background-image`解:
    1.  如果 URL 被证明是坏的，它将显示空白，用`img` + `object-fit`一，它显示好的旧的“坏的图像，因为 URL”回退(或`alt`属性值)。
    2.  一个`img`标签更容易访问，因为我们可以有`alt`
    3.  输入`src="my-url"`只比`style="background-image: url('my-url')"`少几个字符。

警告:这在老版本的浏览器上可能不起作用，但是不管如何优雅地降级(图像将被拉伸)，它不会弄乱布局。

[内特贝尔](https://unsplash.com/@n8bell34?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Nate Bell")