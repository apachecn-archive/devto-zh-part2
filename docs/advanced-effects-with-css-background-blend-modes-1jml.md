# CSS 背景混合模式的高级效果

> 原文：<https://dev.to/bnevilleoneill/advanced-effects-with-css-background-blend-modes-1jml>

[![](img/241c0ae71bf681ffa10298ec92faab75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HqEzke2a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgkMmIfiEZNLdV9Q10FrjBw.jpeg)

如果一张图片胜过千言万语，那么将两张图片融合在一起的价值肯定是它的数倍。同样，CSS 中混合模式的可用性所带来的设计可能性可能比您意识到的要大。

当你听到 **CSS 混合模式**被谈论时，骚动实际上是关于三个新的 CSS 属性，它们在现代浏览器中获得了相当好的支持。

这些属性包括:

*   背景混合模式，用于混合元素的背景图像、渐变和背景颜色
*   mix-blend-mode，用于将元素混合到其他元素上，最后
*   隔离，mix-blend-mode 使用的一个较少使用的属性，用于防止元素混合在一起。

然而，本文将重点介绍背景混合模式，这是一种得到最广泛支持的属性，以及您现在如何使用它来为您的网站创建引人注目的背景和照片效果，这些曾经只有在 Photoshop 中才能实现。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 结合 CSS 渐变与背景混合模式

背景属性是我们可以使用 CSS 渐变的地方。像 linear-gradient()、radial-gradient()以及这两种类型的 repeating-linear-gradient()和 repeating-radial-gradient()这样的函数得到了广泛的支持，更重要的是，它们拥有跨浏览器的标准化语法。

但是 background 属性也可以接受一个以上的渐变渲染，每个函数用逗号分隔。Lea Verou 展示了令人印象深刻的图案，这些图案可以用这种技术创造出来，从棋盘、砖块到星星。

[![](img/46060ce753fb0f7ab6ffa102f63acc1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LhXa5pOY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AG-mavF51iOebWwp8)

但是现在我们有了背景混合模式属性，我们可以创建更多新的渐变和图案。

### 光谱背景

让我们叠加三个渐变来制作一个可以在显示器上显示的几乎全光谱的背景。

```
.spectrum-background {
    background:
        linear-gradient(red, transparent),
        linear-gradient(to top left, lime, transparent),
        linear-gradient(to top right, blue, transparent);
    background-blend-mode: screen;
} 
```

[![](img/f893bed0e6490fb09ce8b624b59bbd31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--73e8yIqs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AI-sI3u34g0ydRqyA)

上面的效果过去只可能出现在几万千字节的图像上。但是我们刚刚用不到 200 字节的 CSS 复制了这个效果，更不用说保存一个 HTTP 请求了。

### 格子背景

我们也可以用渐变和背景混合模式创建一些有趣的格子图案。

```
.plaid-background {
    background:
        repeating-linear-gradient(
            -45deg,
            transparent 0,
            transparent 25%,
            dodgerblue 0,
            dodgerblue 50%
       ),
       repeating-linear-gradient(
            45deg,
            transparent 0,
            transparent 25%,
            tomato 0,
            tomato 50%
        ),
        repeating-linear-gradient(
            transparent 0,
            transparent 25%,
            gold 0,
            gold 50%
        ), white;
    background-blend-mode: multiply;
    background-size: 100px 100px;
} 
```

[![](img/6f613d9b0df44f51c3ef1a852404efd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1Ondc-Oq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AytlEFkVMKjOcHi5t)

### 圆圈背景

再来一个怎么样，这次用径向渐变:

```
.circles-background {
    background:
        radial-gradient(
            khaki 40px,
            transparent 0,
            transparent 100%
        ),
        radial-gradient(
            skyblue 40px,
            transparent 0,
            transparent 100%
        ),
        radial-gradient(
            pink 40px,
            transparent 0,
            transparent 100%
        ), snow;
    background-blend-mode: multiply;
    background-size: 100px 100px;
    background-position: 0 0, 33px 33px, -33px -33px;
} 
```

[![](img/a9ac99ec1aeb5d1e0e20b9bdc85c1475.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6hY8RHc_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ADLJBVt3ZN5l2E-1G)

### 更多背景

在 Yoksel 和 Una 克拉韦茨的帮助下，我准备了 24 种混合模式渐变的[集合](https://bennettfeely.com/gradients/)，让你的网站看起来更快更有吸引力。

[![](img/a0d706566f82de30639d38619f2c0696.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7ztVtIqz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ABti236XTjirmwhhK)

### 带有背景-混合-模式的照片效果

虽然 background-image 允许我们在一个元素上设置多个渐变，但有一个技巧是，我们也可以用 url()语法以大致相同的方式设置多个图像背景。当我们将它与背景混合模式和过滤器等属性结合起来时，事情就变得非常有趣了。

### 铅笔素描效果

[![](img/69ac8ed98ea05ab06b4333310cc26f60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bsucvuOp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AkmcnSP2DUv2LjnKs)

我们可以使用 CSS 使左边的照片看起来像是用铅笔和纸画的。没有 Photoshop，没有 HTML5 画布，没有 WebGL，没有 Javascript 库。我们只需要五个 CSS 属性。

尽管我们可以使用其他块级元素，如

, , , here’s the HTML we’ll use to get started.

```
<div class=”pencil-effect”></div> 
```

开始造型吧。将 chapel.jpg 替换为您正在使用的图像的 url。我们将设置背景图像两次，其背景大小覆盖。

```
.pencil-effect {
    background:
        url(chapel.jpg),
        url(chapel.jpg);
    background-size: cover;
} 
```

下面第一个方块是我们第一步的结果:

[![](img/83dad7340c61d47a543daf4875799498.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W0VuLmX8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AZctU9Uc8vVC66dzn)

现在让我们添加一个混合模式:

```
background-blend-mode: difference; 
```

哦不，东西都去哪了？我们只剩下一个全黑的正方形。差异混合模式采用两个背景，并从一个背景的较亮颜色中逐个像素地减去另一个背景的较暗颜色。

[![](img/3d0838e4ab22148d5bb039f5bf24402c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LvZLy7wX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AcgKI5l8d51jPi69y)

如果对这里发生的事情感到困惑，让我们看看当我们用 background-position 和 calc()稍微偏移两个背景时会发生什么。

```
background-position:
    calc(50% — 1px) calc(50% — 1px),
    calc(50% + 1px) calc(50% + 1px); 
```

[![](img/eef8ecdc965a01dd66427517a409594c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ojUUu3Cm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ArEPPqTFGKm4MPWT6)

使用逗号，我们设置两个背景位置，每个位置对应于背景图像的一个副本。第一张图片，我们从 x 轴的中心向左移动一个像素，从 y 轴的中心向上移动一个像素。对于背景图像的第二个副本，我们做相反的事情，向下向右移动。

我们剩下的是两个稍微偏移的背景图像，但是整个图片仍然在我们的元素的中心。现在，差异混合模式找到了两个图像之间的差异，它揭示了照片中的边缘。很酷吗？

最后，我们将使用过滤器属性来反转照片，并使其灰度化。

```
filter: brightness(3) invert(1) grayscale(1); 
```

对于这张特定的照片，我们还将提高亮度，这具有增加线条对比度的次要效果。

这是我们最后的 CSS 片段:

```
.pencil-effect {
    background:
        url(photo.jpg),
        url(photo.jpg);
    background-size: cover;
    background-blend-mode: difference;
    background-position:
        calc(50% — 1px) calc(50% — 1px),
        calc(50% + 1px) calc(50% + 1px);
    filter: brightness(3) invert(1) grayscale(1);
} 
```

[![](img/b55c050f41ebb5058530a7fe4f2c84a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yhOqrqiG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AkXT2ciDnr0RNJ3O5)

### 黑板效果

[![](img/ff8d01fbc3cd9bc884edb13b25f12ea4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nu0fwvLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AL21nPsxAgdm6M14u)

我们可以按照铅笔素描效果的步骤重新创建一个黑板效果，同时省略最后一步的反转(1)功能。

### 夜视效果

[![](img/62b13f57ce9da84067129ee25b3bf948.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NobZFCzr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A77ao_vh0ALXwOL1T)

让我们试着用 CSS 混合模式再创建一个效果，让照片看起来像是我们通过夜视镜的镜头在看它。

我们的背景有三个部分，我们将用叠加混合模式混合在一起。这种混合模式可以使背景变亮和变暗，也可以作为其他两种混合模式的组合，即“倍增”和“加网”。

首先，我们将设置我们的背景图像，只是这一次。

```
background: url(moose.jpg);
background-size: cover;
background-position: center; 
```

[![](img/a87ecce9021fb23adc688a08361a8eb6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cKIKtLEc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AI_0R-A0Qpr4oPuTE)

现在让我们添加一个渐变和背景混合模式。我们在这里使用了一个稍微透明的石灰到黑色的径向渐变。

```
background:
    url(moose.jpg),
    radial-gradient(
        rgba(0,255,0,.8),
        black
    );
background-blend-mode: overlay; 
```

[![](img/e89f5d38eb252a8bd3e1bc0c74f83be4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pP604nMB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AN7plCqwnf9tQiwRK)

不算太坏，也许我们想就此打住。我还有一个想法可能会使效果看起来更真实，那就是用重复的背景渐变给背景添加一些人造扫描线。

```
background:
    url(moose.jpg),
    radial-gradient(
        rgba(0,255,0,.8),
        black
    ),
    repeating-linear-gradient(
        transparent 0,
        rgba(0,0,0,.2) 3px,
        transparent 6px
    ); 
```

[![](img/97f596862d04981738c99e129e8166a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BsLE4ay0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ApBYvLAFw7tfu0wcN)

总结一下，用于这个效果的完整 CSS:

```
.night-vision-effect {
    background:
        url(moose.jpg),
        radial-gradient(
            rgba(0,255,0,.8),
            black
        ),
        repeating-linear-gradient(
            transparent 0,
            rgba(0,0,0,.2) 3px,
            transparent 6px
        );
    background-blend-mode: overlay;
    background-size: cover;
 } 
```

### 更多照片效果

我做了一个图库，里面有 20 种 CSS 图像效果，你可以在你的网站上使用。

[![](img/ff4000ff1b3ede426d66e86168a707a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZGt2_UF1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ApbOpSQo7_KpwEIeO)

### 浏览器支持优雅退化

好消息是，Firefox、Chrome 和 Opera 完全支持背景混合模式属性。

稍微好一点的消息是，它在 Safari 中有足够的支持来支持我们在这里概述的所有效果，而 Safari 目前缺乏对饱和度、色调、颜色和亮度混合模式的支持。

坏消息是，Internet Explorer 和 IE Edge 不支持任何 CSS 混合模式属性。

这意味着我们需要为尚不支持背景混合模式的浏览器提前做好准备。漂亮的 CSS @supports 规则使这变得非常容易。这里有两个例子。

对于第一个例子，如果不支持背景混合模式属性，我们将使用光谱背景渐变并提供一个后备。我们使用背景:灰色；作为后备，但是任何 CSS 都可以用在它的位置上。

```
.spectrum-background {
    background: gray;

    @supports (background-blend-mode: screen) {
        background:
            linear-gradient(red, transparent),
            linear-gradient(to top left, lime, transparent),
            linear-gradient(to top right, blue, transparent);
        background-blend-mode: screen;
    }
} 
```

这是有退路的铅笔素描效果。我们正在测试两个我们需要支持的关键属性:滤镜和背景混合模式。如果用户的浏览器不支持这些(或者如果浏览器不支持 CSS @supports)，我们将退回到普通的原始照片。

```
.pencil-effect {
    background-image: url(photo.jpg);
    background-size: cover;

    @supports (background-blend-mode: difference) and (filter: invert(1)) {
        background-image:
            url(photo.jpg),
            url(photo.jpg);
        background-blend-mode: difference;
        background-position:
            calc(50% — 1px) calc(50% — 1px),
            calc(50% + 1px) calc(50% + 1px);
        filter: brightness(3) invert(1) grayscale(1);
    }
} 
```

### 资源

[贾斯汀·麦克道尔揭开混合模式的神秘面纱](http://alistapart.com/article/blending-modes-demystified)

[Chris Coyier 的 CSS 混合模式基础知识](https://css-tricks.com/basics-css-blend-modes/)

[Lea Verou 的 CSS3 模式图库](http://lea.verou.me/css3patterns/)

[乌娜·克拉韦茨的 6 集 CSS 图像特效系列](http://una.im/vintage-washout/#%F0%9F%92%81)

[我可以使用…背景混合模式支持吗](https://caniuse.com/#search=background-blend-mode)

Bennett Feely 的 CSS 图像效果

[Bennett Feely 的背景混合模式属性的新 CSS 渐变可能性](http://bennettfeely.com/gradients/)

[Rik Cabanier 的背景混合模式实验聚合填料](https://github.com/cabanier/poly)

[W3C 的合成和混合 1 级规范](https://www.w3.org/TR/compositing-1/)

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[使用 CSS 背景混合模式的高级效果](https://blog.logrocket.com/advanced-effects-with-css-background-blend-modes-4b750198522a/)最先出现在[博客](https://blog.logrocket.com)上。