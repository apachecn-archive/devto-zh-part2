# 最常见的(善意的)错误

> 原文：<https://dev.to/laken/most-common-well-meaning-a11y-mistakes-2bap>

每当我查看网站的网页可访问性(a11y)问题时，我都会看到一些重复多次的 a11y 错误。

## 不是每张图片都需要可选文字

不正确的替代文本是我随处可见的一个错误，甚至在大公司的网站上也是如此。只有当图像传达了文本没有传达的附加信息时，才需要替换文本。

如果一个图像不需要替换文本，你仍然不能省略`alt`属性！相反，将`alt`属性留空，如下所示:

```
<img src="foo.png" alt="" /> 
```

Enter fullscreen mode Exit fullscreen mode

空的`alt`属性告诉屏幕阅读器理解内容不需要图像。No `alt`属性导致一些屏幕阅读器试图解释图像，比如读取图像的 URL。可以想象，这是一种可怕的用户体验；当图像对理解内容毫无帮助时，情况就更糟了。

## 每个表单域都需要一个标签

省略表单字段标签来换取仅仅使用占位符被认为是当前 Web 设计领域的一种时髦做法。这种趋势的流行令人不安，因为它对 11 岁的人来说是可怕的。

在一些屏幕阅读器上，占位符被完全忽略。因此，您应该始终使用与每个表单域相关联的标签。

如果你坚持使用占位符，至少要包含一个标签，但是要有一个类，让屏幕阅读器可以看到它。例如:

HTML:

```
<label for="email_address" class="sr-only">Email</label>
<input id="email_address" type="text" placeholder="Email" /> 
```

Enter fullscreen mode Exit fullscreen mode

CSS:

```
.sr-only {
 position: absolute;
 width: 1px;
 height: 1px;
 margin: -1px;
 padding: 0;
 border: 0;
 clip: rect(0 0 0 0);
 overflow: hidden;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果采用这种方法，请确保标签文本与占位符文本相同。标签文本是基于听写的辅助技术用于导航网站上的表单字段的内容。

## 咏叹调太多

在 HTML5 中，你可以使用`aria`属性，让辅助技术更好地理解你的网站的各个部分。

然而，如果 aria 被滥用，这比你的站点上没有任何属性更糟糕。

我看到的最常被误用的`aria`属性是`role`属性。我相信这个错误被广泛滥用的原因是由于编程方法*“堆栈溢出复制/粘贴”*。例如，如果您将一个`a`元素设计成类似于 CTA(行动号召)的按钮，那么您看到的例子可能会有`role="button"`。`role`属性并不告诉辅助技术元素看起来像什么，而是它的行为方式。链接元素没有`button`元素那样的键盘控件。例如，按钮可以用空格键切换，但这不能在链接上做任何事情，只能让你的页面在许多浏览器上向下滚动。

帖子[最常见的(善意的)A11y 错误](https://laken.net/blog/most-common-a11y-mistakes/)首先出现在 [Laken Hafner](https://laken.net) 上。