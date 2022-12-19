# 具有自定义属性的智能动画

> 原文：<https://dev.to/equinusocio/smart-animations-with-custom-properties-18em>

几天前我和一个朋友谈论最流行的 CSS 库来将简单的动画集成到一个项目中。经过一番研究，这些名字出现了，包括最流行的 **Animate.css** 和 **AnimeJS** :

*   **animate . CSS**-【https://daneden.github.io/animate.css/】T3
*   **anime js**-T3】http://animejs.com/
*   **魔法动画**-T3】https://www.minimamente.com/example/magic_animations/
*   **悬停**-T3】http://ianlunn.github.io/Hover/
*   **阿尼吉斯**-T3】http://anijs.github.io/

最让我惊讶的是，其中一些库，尤其是那些 CSS，是几年前才推出的。虽然 CSS 现在允许我们找到更好的结果，编写更简单、更高效的代码，但这些库似乎是永恒的(你还记得 jQuery 吗？)并且只有很少人真正重视这些一旦被包含到我们的项目中所带来的复杂性；尤其是如果我们必须考虑重量和性能的话。例如，我们都应该问自己的一个问题是:

> 如果我只需要 3 个动画，那么包括整个库有意义吗？

答案显然是*不*，为了克服这一点，有些人“复制”(👮🏻)单个关键帧，并独立于库本身使用它们。如果你使用 [PostCSS](https://postcss.org/) (你应该这样做)，有一个插件可以让你在需要的时候有条件地导入你想要的关键帧。

## 问题

几乎所有这些 CSS 库都提供了一组特定动画被分配到的类。因此，为了应用一个从底部渐变的动画，我们将添加相关的类到 HTML 元素中，然后通过 CSS 定制它:

```
<progress class="fadeFromBottom"></progress>

<style>
  .fadeFromBottom {
    animation-duration: 2s; /* This may require !important 👀 */
    animation-delay: 0.5s;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

很容易理解，这意味着对于每个导入的动画(例如，方向由不同的类管理)都有一个相应的 CSS 类，为了定制它，我们必须重写代码，有时使用*！重要的* (❗️)关键字，导致不太可伸缩的代码。

因此，我们如何利用今天的工具来发展这种方法呢？

## 继续阅读:

> [带有自定义属性的智能动画- Equinsuocha.io](https://equinsuocha.io/blog/smart-animations-with-custom-properties)