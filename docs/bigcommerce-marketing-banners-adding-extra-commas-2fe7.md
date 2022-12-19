# 添加额外逗号的 BigCommerce 营销横幅

> 原文：<https://dev.to/jackharner/bigcommerce-marketing-banners-adding-extra-commas-2fe7>

我在使用 BigCommerce 的模板框架时发现了一个有点烦人的小 bug。当你在主题中的同一位置设置了多个营销横幅时，显示它们的标准方式只是`{{{banners.bottom}}}`(最新版本的 Cornerstone 使用`{{{limit banners.bottom 1}}}`来显示其中一个横幅)。愚蠢的错误是，这将所有不同的横幅 HTML 输出为逗号分隔的列表。

## 这个问题

如果您将三个横幅设置为显示在特定页面(在我的例子中是主页)的底部位置，`{{{banners.bottom}}}`输出为:

```
<<BANNER CONTENT>>
,
<<BANNER CONTENT>>
,
<<BANNER CONTENT>> 
```

Enter fullscreen mode Exit fullscreen mode

## 修罗

我们不只是输出带有`{{{banners.bottom}}}`的横幅，而是要遍历它们并自己输出。来看看:

```
{{#each banners.bottom}}
    <div class="banner--bottom">
        {{{this}}}
    </div>
{{/each}} 
```

Enter fullscreen mode Exit fullscreen mode

它将输出如下:

```
<div class="banner--bottom">
    <<BANNER CONTENT>>
</div>
<div class="banner--bottom">
    <<BANNER CONTENT>>
</div>
<div class="banner--bottom">
    <<BANNER CONTENT>>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

哒哒。干净、无逗号的横幅。如果这对你有帮助，请告诉我。我觉得 Stencil 框架中缺少很多定制的东西，还有很多 BigCommerce 应该包含但没有包含的东西。你遇到过什么奇怪的虫子吗？请在评论中告诉我。