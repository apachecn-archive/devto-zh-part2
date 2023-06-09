# 用 Flexbox 创建简单表单

> 原文：<https://dev.to/zellwk/creating-a-simple-form-with-flexbox--3gln>

web 上最简单的表单包含一个电子邮件字段和一个提交按钮。有时，电子邮件字段和提交按钮位于同一行，如下所示:

[![Email and submit button on the same row](img/305d8069569c0707169ddb3be74b2b04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wm1bMFZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/simple-form-flexbox/form.png) 

<figure>

<figcaption>邮件和提交按钮在同一行</figcaption>

</figure>

这个 UI 看起来很简单！但是如果你使用像`inline-block`这样的老方法，那么构建起来会很困难。困难的部分是让电子邮件字段和按钮在视觉上对齐。

好消息是:CSS Grid 或 Flexbox 可以帮助您轻松构建这个表单。

## 用 Flexbox 制作表单

从上图中，我们知道表单包含两个元素:

1.  电子邮件字段
2.  提交按钮

下面是 HTML:

```
<form>
  <input type="email" name="email"> 
  <button type="submit">Send</button>
</form> 
```

这是你写 HTML 时所拥有的东西(在为外观设计样式之后)。

[![Input and Button elements are both inline-block elements](img/3d4cee454d455118e0529486d5cd8fe0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xuv69CFs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/simple-form-flexbox/before.png) 

<figure>

<figcaption>输入和按钮元素都是内嵌块元素</figcaption>

</figure>

`input`和`button`都是`inline-block`元素。当您将内联块元素放在一起时，它们之间会有大约 3-4 px 的空白。(在每个`inline-block`元素的底部还会有 3-4px 的空白)。

要解决这个空白问题，您可以更改`input`和`button`的显示属性。一种方法是使用 Flexbox。

如果您使用 Flexbox，您希望将父元素的 display 属性设置为`flex`。

```
form {
  display: flex;
} 
```

将显示器设置为 flex 后，您会得到以下结果:

[![Whitespace between elements disappeared we used Flexbox](img/0251a940c092f57167940a70338573ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hc0q9ply--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/simple-form-flexbox/flexboxed.png) 

<figure>

<figcaption>元素间的空白消失了我们改成了 Flexbox</figcaption>

</figure>

下一个。

你想给用户尽可能多的空间来填写他们的电子邮件地址。我们的例子中没有足够的空间。

为了增加空白，我们可以让`input`元素增长，这样它就占据了所有额外的可用空间。您可以通过将`flex-grow`设置为`1`来实现这一点。

```
input {
  flex-grow: 1; 
} 
```

[![Email field grew to take up any available spaces](img/305d8069569c0707169ddb3be74b2b04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wm1bMFZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/simple-form-flexbox/form.png) 

<figure>

<figcaption>电子邮件字段变得占用任何可用空间</figcaption>

</figure>

这里有一个代码笔来试试这个:

参见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的带 Flexbox 的笔[简表。](https://codepen.io/zellwk/pen/aaQerX/)

## 当元素高度不相等时...

如果您的`input`和`button`元素高度不相等，这种技术会很有帮助。我们将通过用 SVG 替换`<button>`文本来模拟这一点。我们还将使这个 SVG 比`input`文本大。

```
<form action="#">
  <input type="email" placeholder="Enter your email">
  <button type="button"> <!-- a smiley icon --> </button>
</form> 
```

[![Adding a smiley icon as the to the submit button](img/932d0d9e4d9057fe2801ba948d9e47e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DwerTIlw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/simple-form-flexbox/smiley.png) 

<figure>

<figcaption>向提交按钮添加笑脸图标</figcaption>

</figure>

注意`input`的高度增加以适应大的 SVG 图标。我们不必编写任何额外的代码！这是因为默认情况下，所有元素都被垂直拉伸以填满所有可用空间。

如果您想改变这种行为，您可以将`align-items`属性改为`flex-start`、`flex-end`或`center`。

[![Items can be aligned differently if you set `align-itemns` to a different value](img/3c83d94939259316f9732b9f14f145bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_hdvYN6N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/simple-form-flexbox/align-items.png) 

<figure>

<figcaption>如果将`align-itemns`设置为不同的值</figcaption>

</figure>

，项目可以不同的方式对齐

注意 SVG 图标下面有一些额外的空间。这里有一个额外的空格，因为默认情况下 SVG 是`inline-block`元素。并且`inline-block`元素下面有一个 3-4px 的空格(如上所述)。

为了修复 SVG 图标下面的这个空间，我们可以将 SVG 的`display`属性更改为`block`。

这里有一个代码笔来试试这个:

见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的[带 Flexbox(带 SVG 按钮)](https://codepen.io/zellwk/pen/JawPwX/)的笔简单形式。

## 包装完毕

Flexbox 可以轻松对齐高度不等的元素。CSS Grid 也是如此！在下一篇文章中，我将向您展示如何使用 CSS Grid 创建相同的表单。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。