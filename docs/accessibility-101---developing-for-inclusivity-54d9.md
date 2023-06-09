# 发展包容性

> 原文：<https://dev.to/jamesrweb/accessibility-101---developing-for-inclusivity-54d9>

## 简介

在本文中，我们将讨论语义标记、ARIA、性能、为用户解决常见问题等主题，最后我们将实现上一篇设计文章中讨论的一些主题。

## 语义标记

> “语义学是对语言中单词和短语的意义的研究。语义元素=有意义的元素。”- [W3Schools HTML5 语义元素](https://www.w3schools.com/htmL/html5_semantic_elements.asp)

语义给了我们一种为我们生产的内容提供意义的方法，一个简单的例子就是博客文章的布局。

也许你会写类似下面这样的东西

```
...
  <div class="container">
        <h1>My awesome post</h1>
        <p>Quis possimus soluta officia accusantium unde perferendis.</p>
        <p>Lorem, ipsum dolor sit amet consectetur adipisicing elit.</p>
  </div>
... 
```

这里的问题是，从视觉上来说，你会有你正在寻找的外观，但有视觉问题的用户怎么办？他们的屏幕阅读器会告诉他们`h1`和`p`标签在一个“组”中，但这并不意味着什么，组可以是任何东西。

更好的方法是使用语义分组元素。在这种情况下，最适合这项工作的元素是`article`标记。

```
...
  <article class="container">
        <h1>My awesome post</h1>
        <p>Quis possimus soluta officia accusantium unde perferendis.</p>
        <p>Lorem, ipsum dolor sit amet consectetur adipisicing elit.</p>
  </article>
... 
```

视觉上什么都不会改变，因为一篇文章在风格上仅仅是一个块级元素。然而，从语义上讲，这将告诉通过辅助技术访问信息的用户，他们处于一篇自包含信息的文章中，这有助于提供上下文和理解。

以下被视为语义分组元素:

| 标签 | 描述 |
| --- | --- |
| 文章 | 定义自我描述内容的文章(项目) |
| 在旁边 | 定义页面内容之外的内容 |
| 细节 | 定义用户可以查看或隐藏的其他详细信息 |
| 图片标题 | 定义一个`figure`元素的标题 |
| 数字 | 指定独立的内容，如插图、图表、照片、代码清单等。 |
| 页脚 | 定义文档或节的页脚 |
| 页眉 | 指定文档或节的标题 |
| 主要的 | 指定文件的主要内容 |
| 标记 | 定义标记/突出显示的文本 |
| 航行 | 定义导航链接 |
| 部分 | 定义文档中的节 |
| 摘要 | 定义一个`details`元素的可见标题 |
| 时间 | 定义日期/时间 |

语义是非常重要的，如果你在前端工作，知道语义和它们的价值，但不把它们作为一个话题。更糟糕的是，如果你自愿选择不执行它们。你真的应该退一步重新考虑一些事情。这样做，你对你的用户、潜在用户和你自己都不公平，因为这个东西很容易实现。如果你只是花时间去思考、论证、构建和实现，同时在必要时接受约束，你将能够为所有用户提供最好的体验。对我来说，这是可及性的基础。

这也是你如何实现*“提供可比较的体验”*无障碍设计原则的一大块。

## DOM 结构

标记的结构和语义一样重要。用户视觉上看到的通常应该是标记的排序方式，因为这是使用辅助技术的用户解释页面的方式。

在坚持语义和结构的同时，使用尽可能少的标签的高质量标记将始终为用户和开发人员提供更好的体验。

## 表现

一般来说，开发中的一个大主题是性能。

这个词对很多人来说可能意味着很多事情，但就可访问性而言，我们可以把它理解为仅仅意味着“这有多快，怎么才能更快？”。

这似乎不是可访问性的逻辑组成部分，但慢速连接、低端设备和低数据预算的用户存在进入障碍，因此性能成为这方面的访问主题。

有一些关于可访问性和性能的非常好的文章，例如:

*   [Max bck 的连接感知性能](https://mxb.at/blog/connection-aware-components/)
*   [Marcy Sutton 的可访问性和性能](https://marcysutton.com/accessibility-and-performance/)
*   [Caroline Lind strm](http://www.diva-portal.org/smash/get/diva2:1077923/FULLTEXT01.pdf)可视化网页可访问性性能，以提高产品利益相关方的意识和理解

这些仅仅是我最喜欢的几个，但还有更多。

简而言之，考虑性能，尤其是前端内容交付和优化。请记住，一刀切的解决方案不适合任何人，每个问题十有八九是独一无二的，应该如此对待。更高性能的前端提供更高的保留率、更低的跳出率和更高的用户满意度，因此，这是一个非常重要的主题，也是一个很好的商业案例。

## 常见问题

### 动画

动画是一种很好的方式来实现一些游戏性，或者提供进一步的上下文，用户操作是成功的或者至少是注册的，等等。然而，这也是一个对于具有某些健康问题(例如癫痫)的用户来说是有问题的话题。

对于许多用户来说，动画如果做得好，会极大地增强网站的体验和情感共鸣，但是对于某些用户来说，在极端的情况下，动画会引起严重的问题。这就是为什么我们应该允许用户控制如何向他们提供动画，并尊重用户的选择，就像我们会做的那样，或者更恰当地说，在任何其他功能中应该做的那样。

输入`prefers-reduced-motion`。这是一个操作系统级别的设置，我们可以通过前面提到的 css 属性插入，它允许用户说他们喜欢减少动画量，然后向我们公开该设置及其选择的值，以便在我们的样式表中进行检测。

这里有一个视频展示了它的作用:

[https://www.youtube.com/embed/uANmA7korfs](https://www.youtube.com/embed/uANmA7korfs)

正如你所看到的，当设置被打开时，这是立即改变的，这是超级强大的，我们可以很容易地实现它。例如:

```
@keyframes moveRight {
  from {
    transform: translateX(0);
  }

  to {
    transform: translateX(100%);
  }
}

.some-element-to-animate {
  animation: moveRight 1s;
}

@media (prefers-reduced-motion: reduce) {
  .some-element-to-animate{
    /* Stop the animation */
    animation: none;

    /* Provide alternative styles if you like */
    ...
  }
} 
```

对于`@supports`特性查询，我们有 3 个选项，分别是:

```
/* Applies styles when Reduced Motion is enabled */
@media screen and (prefers-reduced-motion: reduce) { }

/* Also applies styles when Reduced Motion is enabled */
@media screen and (prefers-reduced-motion) { }

/* Applies styles when the user has made no preference known */
@media screen and (prefers-reduced-motion: no-preference) { } 
```

如果你真的想变得更具体，上面的例子可以改成下面的:

```
@keyframes moveRight {
  from {
    transform: translateX(0);
  }

  to {
    transform: translateX(100%);
  }
}

@media screen and (prefers-reduced-motion: no-preference) {
    .some-element-to-animate {
      animation: moveRight 1s;
    }
}

@media (prefers-reduced-motion: reduce) {
  .some-element-to-animate{
    /* Stop the animation */
    animation: none;

    /* Provide alternative styles if you like */
    ...
  }
} 
```

显式地，只有在没有指定首选项的情况下才运行动画，但是在有类似于`prefers-reduced-motion: allow-animation`或类似效果的关键字之前，我将只使用第一种实现方式，而不是双重媒体查询，原因很简单，因为`prefers-reduced-motion: no-preference`与“他们可能想要和/或接受动画”不同，如果我们假设无论如何都是这样的话，级联是比第二个媒体查询更简单的选择，以有效地做同样的事情。

> **请注意:**遗憾的是([https://cani use . com/# search = prefers-reduced-motion)【prefers-reduced-motion](https://caniuse.com/#search=prefers-reduced-motion)%5Bprefers-reduced-motion)目前支持相对较少】但是在所有 macOS 和 iOS 设备中完全支持，并且正在为下一版本的 Firefox 进行开发。

这是一个实现为渐进增强的完美特性，只有通过使用这些特性，其他浏览器才会注意到我们推动规范通过的兴趣。这是一个伟大的特性，它将增强许多人的体验，所以前进吧，增强所有的东西！

## 从设计篇往前走

如果您还没有阅读设计文章，我强烈建议您阅读，因为这将是接下来的内容，我的目标是在每一节中复习我们讨论过的内容，但理解这一节中每个领域的基础方面很重要，因此如果您还没有阅读上一篇文章，我强烈建议您阅读。

### 字体大小

#### 复习

> 对于许多主流正文字体，14 磅和 18 磅大约相当于 18.75 像素和 24 像素或 1.2 像素和 1.5 像素，或者相当于正文默认大小的 120%或 150%(假设正文字体为 100%)，但作者需要对使用的特定字体进行检查- [史蒂夫·福克纳](https://twitter.com/stevefaulkner)

如果我们以小文本为例，上面的引用中没有包括它，但它通常在用户代理样式表中，其值介于包含元素字体大小的 80%和 85%之间。如果正常正文文本的字体大小为 18.75 像素，我们可以推断出一个 16 像素的小文本基值。

| 大小 | 像素值 |
| --- | --- |
| 小型文本 | Sixteen |
| 正文 | Eighteen point seven five |
| 大文本 | Twenty-four |

#### 从像素值生成 rem

首先，让我们创建一个类似于基础框架的`rem-calc`函数。这将允许我们提供一个像素值并接收一个 rem 值作为回报。

该函数将如下所示:

```
/**
 * @function rem-calc
 *
 * @example - SCSS usage
 *
 *  p {
 *      font-size: rem-calc(18.75);
 *  }
 *
 * @example - CSS output
 *
 *  p {
 *      font-size: 1.171875rem;
 *  }
 *
 * @param {int} $size - desired font size in px
 * @param {int} $base [16] - base font size in px
 * @return string - rem value representation of the desired px input
 */
@function rem-calc($size, $base: 16) {
  $remSize: $size / $base;
  @return #{$remSize}rem;
} 
```

好，让我们来分解一下。首先，我们有一个默认的基本字体大小 16，这是由于假设正文字体大小设置为 100%。为了确保这一点，我们将在我们的 scss 中添加下面一行:

```
body {
    font-size: 100%;
} 
```

然后就像我们喜欢的任何地方调用函数一样简单，比如:

```
// body text
main {
    font-size: rem-calc(18.75);
}

// small text
small {
    font-size: rem-calc(16);
}

// large text
.text-large {
    font-size: rem-calc(24);
} 
```

现在我们已经做好了一切准备来满足上面列出的最低要求，很不错吧？

我们甚至可以通过逆向计算得到 px，就像这样:

```
/**
 * @function px-calc
 *
 * @example - SCSS usage
 *
 *  p {
 *      font-size: px-calc(1.171875);
 *  }
 *
 * @example - CSS output
 *
 *  p {
 *      font-size: 18.75px;
 *  }
 *
 * @param {int} $size - desired font size from rem
 * @param {int} $base [16] - base font size in px
 * @return string - px value representation of the desired rem input
 */
@function px-calc($size, $base: 16) {
  $pxSize: $size * $base;
  @return #{$pxSize}px;
} 
```

我个人不知道这方面的任何用例，但重点是你可以做到这一点。

### 颜色对比

#### 复习

> “文本和文本图像的视觉呈现的对比度至少为 4.5:1，以下情况除外:
> 
> 大文本
> 
> 大尺寸文本和大尺寸文本的图像具有至少 3:1 的对比度；
> 
> 附带事件
> 
> 作为非活动用户界面组件一部分的文本或文本图像、纯装饰、任何人都看不见的文本或文本图像，或者作为包含其他重要视觉内容的图片的一部分的文本或文本图像，没有对比度要求。
> 
> 作为标志或品牌名称一部分的文字没有对比度要求。”- [WCAG 成功准则 1.4.3 -色彩对比](https://www.w3.org/TR/WCAG/#contrast-minimum)

使用我们在本文的[字体大小部分中详述的普通和大字体大小的定义，我们现在可以应用下面的对比度规则来获得可达到的对比度。](#font-sizing)

| WCAG 2.0 级别 | 大小 | 对比率 |
| --- | --- | --- |
| 美国汽车协会 | 常态 | 7:1 |
| 美国汽车协会 | 大，正常粗体 | 4.5:1 |
| 嗜酒者互诫协会 | 常态 | 4.5:1 |
| 嗜酒者互诫协会 | 大，正常粗体 | 3:1 |

#### 生成可能的最佳对比度

对于开发人员来说，色彩理论、对比度等等有时会很难理解，尤其是当你的注意力不在前端的时候。

有时，设计师也会受到公司指导方针的阻碍，这些指导方针不允许他们在设计过程中获得最佳的可能比率，然而，作为开发人员，我们仍然可以尝试给出最佳的比率，这就是我所发现的最有用的组合之一发挥作用的地方。

所以，我们有一个按钮，它有一个背景颜色值`#f44242`，我们需要提供一个可访问的，或者至少是“尽可能可访问”的颜色与之对比。如何才能实现这一点？

嗯，这是拯救任何想法的混合

```
/**
 * @mixin text-contrast
 *
 * @example - SCSS usage
 *
 *  body {
 *      background-color: black; 
 *      @include text-contrast(black);
 *  }
 *
 * @example - CSS output
 *
 *  body {
 *      background-color: black; 
 *      color: white;
 *  }
 *
 * @param {colour} $colourToContrast - the colour value to contrast against, this can be any valid css colour value whether a named value, an rgba() value, a hex value, etc
 */
@mixin text-contrast($colourToContrast) {
  $color-brightness: 
    round((red($colourToContrast) * 299) + (green($colourToContrast) * 587) + (blue($colourToContrast) * 114) / 1000);
  $light-color: 
    round((red(white) * 299) + (green(white) * 587) + (blue(white) * 114) / 1000);

  @if abs($color-brightness) < ($light-color / 2){
    color: white;
  } @else {
    color: black;
  }
} 
```

现在，这可能看起来有点像魔术师的工作，但实际上它是 W3C 提出的一种算法，正在付诸实施。

> 颜色亮度由以下公式决定:
> 
> ((红色值 X 299) +(绿色值 X 587) +(蓝色值 X 114)) / 1000
> 
> 注意:该算法取自将 RGB 值转换为 YIQ 值的公式。该亮度值给出了颜色的感知亮度。"- [W3C 可访问性评估技术和修复工具](https://www.w3.org/TR/AERT/#color-contrast)

假设为了便于讨论，我们想要在背景`forestgreen`上对比文本。在这种情况下，整个 mixin 可以分解为:

```
/*
*
* @example
*
* .green-section {
*   background-color: forestgreen;
*   @include text-contrast(forestgreen);
* }
*
* becomes:
*/
@mixin text-contrast(forestgreen) {
    /*
    * forestgreen RGB values:
    *
    * Red: 1
    * Green: 68
    * Blue: 33
    */
    $color-brightness: round(
        (red(forestgreen) * 299) + 
        (green(forestgreen) * 587) + 
        (blue(forestgreen) * 114) / 1000
    ); // 40219

    /*
    * white RGB values:
    *
    * Red: 255
    * Green: 255
    * Blue: 255
    */
    $light-color: round(
        (red(white) * 299) + 
        (green(white) * 587) + 
        (blue(white) * 114) / 1000
    ); // 225959

    /*
    * The middle ground between white and black:
    * $light-color / 2 = 225959 / 2 = 112979.5
    *
    * The absolute value of $color-brightness:
    * 40219
    *
    * The condition ends up as:
    * 40219 < 112979.5
    *
    * The outcome is true and thus the resolution is:
    * color: white
    */
    @if abs($color-brightness) < ($light-color / 2) {
        color: white;
    } @else {
        color: black;
    }
} 
```

要使用 mixin，我们可以像下面这样做:

```
$black: #333;
$cornflower: cornflowerblue;

body {
    background-color: $black; 
    @include text-contrast($black);
}

header {
    background-color: $cornflower; 
    @include text-contrast($cornflower);
} 
```

超级简单，在我的经验中，99.999%的情况下都能满足 WCAG AA 通过的最低色彩对比要求。

向大卫·哈尔福德和他在 mixin 上的对比文本博文大呼一声，完全归功于他，它这些年来对我很好。

### 为数据表提供表头

**注意:**本节没有复习内容，因为我们将基于 Haydon Pickering 撰写的关于创建可访问数据表的优秀文章[构建一个可访问数据表。](https://inclusive-components.design/data-tables/)

让我们写一个表格，显示销售渠道中所有潜在客户的数据。

这可能是我们表格的开始标记:

```
<table>
    <thead>
        <tr>
            <th>name</th>
            <th>employees</th>
            <th>founded</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ACME co</td>
            <td>1296</td>
            <td>1993</td>
        </tr>
        <tr>
            <td>ACME co</td>
            <td>1296</td>
            <td>1993</td>
        </tr>
    </tbody>
</table> 
```

现在，这是一个完全有效的表，但如果我们想让它尽可能地易于访问，我们可以做一些小的更改，这将真正提高使用辅助技术的用户对数据的理解，并依赖于样式，非辅助技术用户也是如此。

让我们做一些改变:

```
<table>
    <caption>
        Companies in the current sales pipeline
    </caption>
    <thead>
        <tr>
            <th>name</th>
            <th>employees</th>
            <th>founded</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th>Google</th>
            <td>1296</td>
            <td>1993</td>
        </tr>
        <tr>
            <th>Facebook</th>
            <td>1296</td>
            <td>1993</td>
        </tr>
    </tbody>
</table> 
```

好吧，你可能有一些问题，但是看看新的标记，试着理解这里发生了什么。

好吗？好吧，让我们来分析一下。

首先，我们添加了一个`<caption></caption>`标签。这个标记是特定于表的，它提供了一种方式，向用户提供更多关于表中包含哪些数据的上下文。它还可以作为表格本身的标题，甚至可以将标题标记作为直接子元素！

其次，我们在表体行中添加了`<th></th>`标签。我敢肯定，在此之前，你们中没有多少人会看到这样做，但这样做是完全正确的。但是有一点需要注意:

`<th></th>`标签通常与列相关联，因此我们需要告诉浏览器这个特定的`<th></th>`只与它所在的行相关。

这就是`scope="row"`和`scope="col"`的用武之地。这告诉浏览器这一行中的数据有两个标题，列标题和行标题。

如果我们将这个添加到:
中，这个表现在看起来会是这样

```
<table>
    <caption>
        Companies in the current sales pipeline
    </caption>
    <thead>
        <tr>
            <th scope="col">name</th>
            <th scope="col">employees</th>
            <th scope="col">founded</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">Google</th>
            <td>1296</td>
            <td>1993</td>
        </tr>
        <tr>
            <th scope="row">Facebook</th>
            <td>1296</td>
            <td>1993</td>
        </tr>
    </tbody>
</table> 
```

例如，对于 screenreader 用户，如果我们在`<tbody></tbody>`的第二行第二个单元格，我们可能会在 screenreader 上听到以下声明:

> 雇员，脸书，1296

这有助于我们提供上下文并促进理解，如果我们将此与样式相结合，以直观地显示行/列标题和表格单元格之间的差异，我们可以在所有用户中促进可比较的体验和理解。

### 不要单靠色彩来传达意思

#### 复习

> “使用颜色可以增强理解，但不要只用颜色来传达信息。要特别注意红色/绿色的搭配。”- [快速参考网页无障碍原则](https://webaim.org/resources/quickref/)

#### 构建无障碍条形图

继续设计文章中的例子，让我们建立一个可访问的条形图，它不仅仅依靠颜色来显示每个项目之间的差异。

在这一点上，让我们也充分利用上面的文本对比和 rem-calc 函数，在真实场景中展示它们。

首先，让我们构建我们的标记:

```
<dl class="bar-chart">
  <dt class="bar-chart__legend">
    Users favourite films
  </dt>
  <dd class="bar-chart__bar bar-chart__bar--40">
    Batman begins - 40%
  </dd>
  <dd class="bar-chart__bar bar-chart__bar--30">
    Pokemon: The movie - 30%
  </dd>
  <dd class="bar-chart__bar bar-chart__bar--60">
    The Avengers - 60%
  </dd>
  <dd class="bar-chart__bar bar-chart__bar--90">
    Titanic - 90%
  </dd>
</dl> 
```

```
// @requires the above rem-calc and text-contrast helpers
.bar-chart {
  // @see: http://geoffgraham.me/randomize-sass/
  $s-min: 20;
  $s-max: 70;
  $l-min: 30;
  $l-max: 90;

  @at-root #{&}__legend {
    font-weight: bold;
    margin-bottom: rem-calc(12);
    font-size: rem-calc(24);
  }

  @at-root #{&}__bar {
    padding: rem-calc(12);
    margin: 0;
    font-size: rem-calc(18.75);

    @for $i from 1 through 100 {
      &--#{$i} {
        max-width: #{$i}#{"%"};
      }

      &:nth-child(#{$i}) {
        $bg: hsl(
          random(360),
          $s-min + random($s-max + -$s-min),
          $l-min + random($l-max + -$l-min)
        );
        @include text-contrast($bg);
      }
    }
  }
} 
```

这样，我们就有了一个功能性的条形图，它使用了我们以前的助手，并且“工作正常”。

然而，我们仍然仅仅依靠颜色来表示事物，因此，让我们快速的举例说明如何给我们的条形背景图案。在`nth-child()`块中，添加以下`@if`块:

```
@if $i == 2 {
    background: linear-gradient(
        63deg,
        lighten($bg, 10%) 23%,
        transparent 23%
        )
        7px 0,
    linear-gradient(63deg, transparent 74%, lighten($bg, 10%) 78%),
    linear-gradient(
        63deg,
        transparent 34%,
        lighten($bg, 10%) 38%,
        lighten($bg, 10%) 58%,
        transparent 62%
    ),
    $bg;
    background-size: rem-calc(16) rem-calc(48);
} @else if $i == 3 {
    background: radial-gradient(
        circle at 0% 50%,
        rgba(96, 16, 48, 0) rem-calc(9),
        darken($bg, 20%) rem-calc(10),
        rgba(96, 16, 48, 0) rem-calc(11)
        )
        0px 10px,
    radial-gradient(
        at 100% 100%,
        rgba(96, 16, 48, 0) rem-calc(9),
        darken($bg, 20%) rem-calc(10),
        rgba(96, 16, 48, 0) rem-calc(11)
    ),
    $bg;
    background-size: rem-calc(20) rem-calc(20);
} @else if $i == 4 {
    background: linear-gradient(
        135deg,
        darken($bg, 20%) 25%,
        transparent 25%
        ) -50px 0,
    linear-gradient(225deg, darken($bg, 20%) 25%, transparent 25%) -50px
        0,
    linear-gradient(315deg, darken($bg, 20%) 25%, transparent 25%),
    linear-gradient(45deg, darken($bg, 20%) 25%, transparent 25%);
    background-size: rem-calc(100) rem-calc(100);
    background-color: $bg;
} @else if $i == 5 {
    background: linear-gradient(
        135deg,
        darken($bg, 20%) rem-calc(22),
        $bg rem-calc(22),
        $bg rem-calc(24),
        transparent rem-calc(24),
        transparent rem-calc(67),
        $bg rem-calc(67),
        $bg rem-calc(69),
        transparent rem-calc(69)
    ),
    linear-gradient(
        225deg,
        darken($bg, 20%) rem-calc(22),
        $bg rem-calc(22),
        $bg rem-calc(24),
        transparent rem-calc(24),
        transparent rem-calc(67),
        $bg rem-calc(67),
        $bg rem-calc(69),
        transparent rem-calc(69)
        )
        0 64px;
    background-color: darken($bg, 20%);
    background-size: rem-calc(64) rem-calc(128);
} @else {
    background: $bg;
} 
```

现在我们有了每根棒线的模式，我们已经完成了这一部分的目标！

### 按钮和链接状态

现在让我们转到本文的最后一节，构建一个按钮和链接组件，为每个状态提供适当的反馈。

#### 按钮组件

##### 默认状态

```
 button {

    } 
```

##### 悬停状态

```
 button {
        &:hover {

        }
    } 
```

##### 活动状态

```
 button {
        &:active {

        }
    } 
```

##### 焦点状态

```
 button {
        &:focus {

        }
    } 
```

##### 禁用状态

```
 button {
        &:disabled {

        }
    } 
```

#### 链接组件

##### 默认状态

```
 a {

    } 
```

##### 链接状态

```
 a:link {

    } 
```

##### 到访过的国家

```
 a:visited {

    } 
```

##### 悬停状态

```
 a:hover {

    } 
```

##### 活动状态

```
 a:active {

    } 
```

##### 焦点状态

```
 a:focus {

    } 
```

##### 禁用状态

```
 a:disabled {

    } 
```

## 结论

这需要经历很多，但我们现在已经回顾了我们在设计文章中所做的几乎所有事情，但都是从开发的角度。

我希望您学到了一些关于构建可访问组件的技巧和趣闻，或者仅仅是关于如何轻松实现元素状态这样的主题。

这是也将是这个系列中最长的一篇文章，所以如果你看完了，干得好！

接下来，我们将看一个使用 aXe 库的简单可访问性测试，以及如何设置一个基本的`gitlab-ci.yml`。

下一集再见！

## 资源

*   [包容性设计原则](https://inclusivedesignprinciples.org/)
*   [包容性设计原则——提供可比较的体验](https://inclusivedesignprinciples.org/)
*   [Max bck 的连接感知性能](https://mxb.at/blog/connection-aware-components/)
*   [Marcy Sutton 的可访问性和性能](https://marcysutton.com/accessibility-and-performance/)
*   [Caroline Lind strm](http://www.diva-portal.org/smash/get/diva2:1077923/FULLTEXT01.pdf)可视化网页可访问性性能，以提高产品利益相关方的意识和理解
*   [WCAG 的成功准则](https://www.w3.org/TR/WCAG/)
*   [W3C 可访问性评估技术和修复工具](https://www.w3.org/TR/AERT/)
*   [大卫·哈尔福德和他关于对比文本的博客文章](http://www.davidhalford.com/thoughts/2013/auto-contrasting-text/)
*   [关于创建可访问数据表的文章](https://inclusive-components.design/data-tables/)
*   [快速参考网页无障碍原则](https://webaim.org/resources/quickref/)
*   [随机选择 SASS 中的项目](http://geoffgraham.me/randomize-sass/)