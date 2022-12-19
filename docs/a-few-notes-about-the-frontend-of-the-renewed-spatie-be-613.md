# ★关于更新后的 spatie.be 前端的几点说明

> 原文：<https://dev.to/freekmurze/a-few-notes-about-the-frontend-of-the-renewed-spatie-be-613>

在 2014 年搬进新办公室之前，我们很快建立了一个临时的单页网站，最初只有荷兰语。它持续了 4 年，从那以后就从它的框架中爆发出来，因为…嗯…没有优先权，没有时间。在很长一段时间里，一个新网站就像一个笑话，直到 Laracon US 2018 为我们提供了一个完美的最终期限。

新网站导航的基础是更好地交流我们这些天真正(喜欢)做什么的冲动。几年前，一家网站设计公司对 Laravel 进行了小修小补，现在很明显，这就是我们的核心和未来所在:Laravel 的定制网站开发，以及尽我们所能的开源。我们想接手更大更复杂的项目。导航应该很清楚这一点。

## 设置

### 山脉

以前使用自然摄影效果很好。它完美地平衡了我们使用的冷技术。我们位于安特卫普，欧洲最大的港口之一，所以提到水和海以前是一个显而易见的选择。这一次，我想更多地关注我们扎实的专业知识和工艺，并转向了山地主题。 尽管这个场景在其他地方已经被广泛使用，我认为它仍然传达了一个伟大的故事。褪色的背景对于在山里呆过一段时间的人来说应该是可以辨认的，它带来了对自由、有巨大回报的艰难道路和无尽可能性的记忆。玩这些图片来支持我们的信息是很有趣的。看看[实习生](http://spatie.be/vacancies/internships)的背景和[空缺](https://spatie.be/vacancies/free-application)的背景。在[网站开发](http://spatie.be/web-development)页面上的自定义图形进一步完善了这一设置。我想做更多的这些，并取代字体真棒图标有一天。

所有的图像都是在[unsplash.com](https://unsplash.com)上找到的。

### 排版

我们已经使用 [Gotham](https://www.typography.com/fonts/gotham/overview/) 作为我们公司的标识字体。这一次，它配有衬线字体——[纪事显示](https://www.typography.com/fonts/chronicle-display/overview/)——用于 outro 部分的标题。 Chronicle 展示为大胆的陈述增加了一些分量，并应突出我们的专业知识。此外，它还增加了较长页面的杂志感，并在 CTA 短语中引起注意。

更多精美字体可以在[typography.com](https://typography.com)上找到。

### 内容

因为我们不断增长的国际观众，这一次我们以英语开始。我们将不得不评估翻译成荷兰语是否值得。 The 文案更加迷人和自信，但不太严肃。它再次强调了我们的工作质量和对更大项目的渴望。

因为我们不想使用 cookies，所以我们暂时远离了接收表单。 All 号召行动是简单的“邮件”链接，带有预填充的主题和正文。如果有人真的期待与我们合作，他们会知道如何找到我们。

## 反应灵敏的图像

正如 Freek 之前指出的，这些图像利用我们的媒体库选项来处理 [scrset 变体](https://docs.spatie.be/laravel-medialibrary/v7/responsive-images/getting-started-with-responsive-images)。从`1px`的`sizes`属性开始，我们使用最小的内嵌 src 作为模糊占位符。当占位符被渲染时，我们用图像的实际宽度交换这个`sizes`值，从而请求更大的版本。

在这种情况下，我们需要在两种情况下完善这项技术。

首先，我们在一些图像上使用`object-fit: cover`来填充它们的响应容器(比如背景)。因此，所需图像的尺寸计算起来有点复杂:您必须考虑图像和容器的长宽比。

其次，这一页有很多明信片，仍然很重。我们为 srcset 图像实现了交叉点观察器，因此它们只在页面上可见时才延迟加载。

感谢上帝，我们有 Sebastian 帮助我完成了实现这些功能的实际代码。

查看 [GitHub](https://github.com/spatie/spatie.be/blob/master/resources/assets/js/front/images/index.js) 上的所有代码。

## CSS

正如在我们最近的所有项目中一样，使用了 [Tailwind CSS](https://tailwindcss.com) 和 PostCSS，但这次我想在混合中引入 CSS 变量和 CSS grid。首先，创建顺风响应网格助手。然后这些可以被组件应用和修改。

```
/* grid.css — grid helper classes */
:root {
    --cols: 1fr;
    --rows: 1fr;
}

@responsive {
    .grid {
        display: grid;
    }

    .grid-template {
        grid-template: var(--rows) / var(--cols);
    }

    …
}

/* wrap.css — the postcard part */

.wrap-gallery {
    @apply grid grid-template gap-0;

    --cols: repeat(auto-fill, minmax(10rem, 1fr));

     @screen sm {
        --cols: repeat(auto-fill, minmax(16rem, 1fr));
     }
} 
```

网格对于这种布局来说非常强大。通过一个简单的`repeat(auto-fill, …)`模式，你可以减少传统媒体的询问。

我们很幸运拥有精通技术的观众。上一年 IE11 及以下版本的使用率为 0.8%(向 2 位 IE7 访客致敬)。对于为数不多的 IE11 用户，我还是想呈现一个干净的体验。所有的网格内容都按照简单的 CSS 规则排列在一列中，我用`@supports`来表示其余的内容。

```
/* wrap.css — grid fallback in 1 column */

.wrap{
    & > :not(:first-child) {
        @apply mt-8;
    }
}

@supports(display:grid) {
    .wrap{
        @apply grid;

        & > :not(:first-child) {
            @apply mt-0;
        }
    }
} 
```

## 总之

为自己建立一些东西总是最难的。我很高兴我们的网站再次处于良好状态，可以在未来几年继续建设。已在列表中:

*   添加真实案例研究
*   添加更多自定义图形和插图

让我们知道你的想法！

* * *

这篇客座博文是我的同事[威廉·范·博克斯特](https://twitter.com/willemvbockstal)写的