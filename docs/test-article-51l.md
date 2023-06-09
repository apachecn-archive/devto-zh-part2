# 为什么 LinkedIn 不会显示一些图片？

> 原文：<https://dev.to/kymiddleton/test-article-51l>

作为一名新兵训练营的学生，我成为 MERN stack JavaScript 开发人员的旅程已经进行了四个月。我从来都不是一个逃避挑战的人，对于一个非技术背景的人来说，有些概念比其他概念更难理解，但没有什么是我不能克服的。这也是一件好事，我喜欢研究和学习新的概念，因为我不会停下来，直到我明白拼图的碎片是如何组合在一起的。

上周，我遇到了一个与 JavaScript 完全无关的挑战。鉴于我们正处于社交媒体无处不在的时代，从脸书、Twitter、Instagram 到 Pinterest 或 Etsy 和 LinkedIn，有大量的应用程序需要清晰的图像作为关键组成部分。我们都知道，在浏览各种提要时，正确的图片在吸引某人的注意力方面是多么迷人。在`Dev.to`上写了几篇博客文章后，我想用图片来增强我的文章，这些图片会在将文章分享到不同网站时显示，尤其是 LinkedIn。

当在`Dev.to`中写一篇新文章时，编辑器会打开一个专门的标题部分，提示在每篇博客文章中包含特定的信息。

```
---
title: 
published: false
description: 
tags: 
--- 
```

在头部分添加带有图像地址链接的`cover_image:`标记简单而直接。

```
---
cover_image: <link to image address>
title: 
published: false
description: 
tags: 
--- 
```

帖子发布后，帖子底部会出现一个`share bar`。

[![share bar](img/c0dbcfe6ceea761b0a5496bbf655de0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h_4bj4KE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6s05gks420t8fgkmplpe.jpeg) 
点击用红色圈出的三个黑色方块，即可将帖子分享到另一个站点。
[![click share bar](img/1d3c195c23f0e9ad0317b69a783704b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_JdtfTK0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fnqo4kpgubbyyypuu1t7.jpeg) 
点击后会出现一个弹出框。
[![popup box](img/0fd0f4a5dfd7366857c3592fe88aae45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ce4HCDB---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yc54w4nuw7yg27scbdeb.jpeg) 
我选择了`Share to LinkedIn`，当文章打包显示在 LinkedIn 上时，缩略图是空白的，只包含一个标题卡。
[![title card](img/af88d99677bcdf45fbe8e04cfd35c322.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bAfj36s0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x3krv0m5d7rdfdrngu3w.jpeg) 
对于生活中的我来说，我想不出为什么一个简单的图像不会渲染。我试了一下`Share to Facebook`，图像果然呈现出来了。因为它呈现给一个应用程序，而不是另一个，我想知道这个问题是否是 LinkedIn 特有的。

在对 LinkedIn 的形象要求进行了大量研究后，我了解到以下几点:

*   LinkedIn 的图像需求是特定于他们的共享模块的。文件的最大大小是 5 MB。最小图像尺寸:1200 像素宽，627 像素高。
*   如果一张图片符合指定的要求，但无法按照预期呈现，那么该图片的托管站点可能会阻止 LinkedIn 的访问。
*   图像可能位于受保护的网站或目录中。

LinkedIn 也有一个工具叫做[帖子检查员](https://www.linkedin.com/post-inspector/)。在检查栏内，复制要在 LinkedIn 上分享的博客文章或文章的链接，然后点击`Inspect`。出现一个显示卡，如果图像没有渲染，只需向下滚动页面到`image`信息，并单击突出显示的链接了解更多详细信息。

对于显示在 LinkedIn 上的图像，它归结为`meta data tags`和一个叫做`OG`的东西，后者指的是`open graph tag`。当涉及到分享图片时，开放图形标签对社交媒体至关重要。作为一名网站开发人员，了解所建网站的类型是否旨在处理社交商务或跟踪客户数据以获得转化率或点击率也很重要。开放图标签对社交媒体上链接的表现有直接影响。

对于我的 Dev.to 帖子，下面是我如何修改标题部分以包含`meta data tags`和`open graph tag`来获取 LinkedIn 上呈现的图像。

```
---
cover_image: <link to image address>
title: 
published: false
description: 
tags: 
<meta prefix="og: http://ogp.me/ns#" property="og:image" content="{link to image address}" />
--- 
```

一旦我修改了标题部分，我就可以很容易地将特定的图片呈现到 LinkedIn 上。记住，不是每张图片都需要这样做，但是如果你爱上了一张不能按预期渲染的图片，试试吧！