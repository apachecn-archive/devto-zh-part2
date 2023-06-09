# 降价的故事

> 原文：<https://dev.to/smnh/the-tale-of-markdownify-4lcl>

当我在玩[雨果](https://gohugo.io)和[哲基尔](https://jekyllrb.com/)的时候，我发现他们的 [`markdownify`](https://gohugo.io/functions/markdownify/) 滤镜有些混乱。

为了描述这个问题，首先我想给你展示一下 Jekyll 的`markdownify`滤镜是如何工作的。

假设您已经定义了`my_text`参数，例如在`_config.yml`文件中:

```
my_text: consectetur **adipiscing** [elit](https://www.example.com) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这个变量是一个 [markdown](https://daringfireball.net/projects/markdown/) 格式的字符串。现在，让我们试着在模板文件中使用这个变量。

```
<div>Lorem ipsum dolor sit amet, {{  site.my_text  |  markdownify  }}</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您尝试使用 Jekyll 从这个模板生成一个 HTML，您将得到类似这样的结果:

```
<div>Lorem ipsum dolor sit amet,
  <p>consectetur <strong>adipiscing</strong>
    <a href="https://www.example.com">elit</a>
  </p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

虽然这可能不是我们想要的，因为我们变量的内容是用`<p>`标签包装的。这是完全可预测的行为，因为 markdown 总是用块元素包装其内容。

但是在雨果身上，这种行为就不那么可预测了

```
<div>Lorem ipsum dolor sit amet, {{  .Site.Params.my_text  |  markdownify  }}</div> 
```

Enter fullscreen mode Exit fullscreen mode

结果在:

```
<div>Lorem ipsum dolor sit amet, consectetur <strong>adipiscing</strong>
  <a href="https://www.example.com">elit</a>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是我们想要的，但是等等。如果我们的变量有两个段落呢？

```
params:
  my_text: |-
    first paragraph

    second paragraph 
```

Enter fullscreen mode Exit fullscreen mode

现在我们得到这个:

```
<div>Lorem ipsum dolor sit amet,
  <p>first paragraph</p>
  <p>second paragraph</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

等待...但我以为雨果在剥离这些标签。如果你有不止一段的话就不会了。

但是如果我真的想用`<p>`把一个段落换行呢？根据[这个](https://github.com/gohugoio/hugo/pull/3786)螺纹，应该使用`blocks`滤波器。但是在我看来，最好有某种类型的`inline`参数来剥离`<p>`标签，而不是默认剥离它们并使`markdownify`过滤器行为不正确。即使这意味着我们不会总是得到我们想要的。标准高于我们所有人:)

杰基尔呢？好吧，虽然有一些尝试(见[这个问题](https://github.com/jekyll/jekyll/issues/3571)和[这个拉请求](https://github.com/jekyll/jekyll/pull/5509))提供一个`inline`参数给`markdownify`过滤器，他们没有成功。我没有找到任何好的方法来降低这个变量的价值，而不需要用`<p>`标签来包装它。我发现的唯一解决方案是将变量设置为原始 HTML，并且在没有`markdownify`的情况下使用它。

我认为这里真正的问题是 [markdown 语法](https://daringfireball.net/projects/markdown/syntax)没有定义任何创建内联内容的标准方式，这些内容不会用像`<p>`这样的块级元素包装。但是如果我们有这样的东西:

```
This will be wrapped placed inside paragraph

!!!But this **will not**!!! 
```

Enter fullscreen mode Exit fullscreen mode

好生:

```
<p>This will be wrapped with paragraph</p>

But this <strong>will not</strong> 
```

Enter fullscreen mode Exit fullscreen mode

那会让我们的生活变得更好。

🖖万岁，繁荣昌盛