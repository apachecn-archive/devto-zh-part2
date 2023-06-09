# 我如何用 CSS 网格设计

> 原文：<https://dev.to/huijing/how-i-design-with-css-grid-4l3o>

在向以前没有尝试过 CSS 网格的人介绍了几轮之后，我发现人们问的问题不是网格的实现，而是之前的一点。如何设置布局的实际规划。

如果你读过上一篇关于[向新人教授 CSS grid](https://dev.to/huijing/teaching-css-grid-to-newcomers-2c4g-temp-slug-9774851)的文章，我使用的一个类比是关于园艺的，即准确地管理你的绣球花、玫瑰和郁金香地块。好吧，我听不懂了。我猜园艺不是每个人都喜欢的🤷。

但老实说，自从我开始用 CSS grid 设计和构建以来，我发现自己用铅笔和纸勾画网格的次数增加了很多。网格语法本身是非常直观的，我在教网格的时候总是强调这一点。

看看简单网格的语法:

```
.grid {
  display: grid;
  grid-template-columns: 150px 150px 150px;
  grid-template-rows: 200px 200px;
} 
```

Enter fullscreen mode Exit fullscreen mode

您已经可以看出网格有 3 列和 2 行。这一点在我非常喜欢的`grid-template-areas`中更加明显。

```
.grid {
  display: grid;
  grid-template-columns: 12em 1fr 15em;
  grid-template-rows: 10em 20em 1fr 10em;
  grid-template-areas: 'a a b'
                       'c d d'
                       'c d d'
                       'e e e';
}

.grid-item__a { grid-area: a; }
.grid-item__b { grid-area: b; }
.grid-item__c { grid-area: c; }
.grid-item__d { grid-area: d; }
.grid-item__e { grid-area: e; } 
```

Enter fullscreen mode Exit fullscreen mode

以代码布局的方式在浏览器中可视化网格是非常直观的。这太棒了，我不认为我们有任何其他的属性以这种方式表现。一个可视化的展示需要一个可视化的工具，而我们用 CSS grid 得到了它。

所以大致来说，这是我将一个静态印刷设计“网络化”的过程，或者是我当时的灵感来源。

1.  检查原始设计中的所有元素
2.  将设计转化为铅笔和纸草图
3.  确定网格需要如何构建，以便它可以很好地适应不同的视口大小
4.  根据设计指定柔性和固定轨道
5.  开始用代码构建设计，并在浏览器上查看
6.  调整轨道大小，直到完全煮熟，哦，等等，对不起，这不是一锅奶酪辣椒的食谱😏

虽然我不是在开玩笑关于调整和微调的部分。我调整浏览器大小的频率超出了一般网络用户的想象。更频繁地成倍增长😌。

## 用例:第谷艺术家简介

<figcaption>第谷艺术家简介由[德鲁里文](https://dribbble.com/shots/3002189-Tycho/)</figcaption>

[![Tycho Artist Profile by Drew Sullivan](img/75d2ab64c948bce482f0ad7e1ab2c826.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dnVvt7hj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/design-grid/tycho-640.jpg)

我在 Dribbble 上看到了这个设计，并立即对自己说，嘿，我完全可以在网上这样做，而不是作为一个图像。我熟悉[第谷](http://www.tychomusic.com/)，因为我用他的[专辑封面](http://blog.iso50.com/)作为我在[CSS conf](https://2016.cssconf.asia/)[第一次会议演讲](https://www.youtube.com/watch?v=gJA5sdyCWNQ)的灵感。亚洲回到 2016 年。

### 检视

<figcaption>这是当我看到一个网格可变的设计</figcaption>

[![General idea of grid structure](img/59d510a2951219eb2a4903e0b14f754b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4k1H58Tc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/design-grid/init-640.jpg) 时在我脑海中发生的事情

该设计可分为 6 列和 4 行。也许你看到的东西不同，你可以直观地看到 5 列或其他东西，这完全没问题。我对设计的态度是非常自由放任的，你做你的，所以想出任何适合你的东西。

### 翻译，结构&指定

铅笔和纸既便宜又快捷。它让我在脑海中反复思考我希望我的轨迹如何表现，同时帮助我可视化最终效果。第一次注意到我的网格结构的人问我一个问题，为什么我有第四个(从左边起)灵活的列？

<figcaption>我喜欢模拟素描，它适合我</figcaption>

[![Pencil and paper sketch of the grid](img/484e017d616681af492584eb0f9c6881.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PuM2OBNr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/design-grid/sketch-640.jpg)

我选择添加一个额外的列，以便更好地控制主文本和特征图像之间的灵活间距。但是当涉及到做 CSS 的时候，总有不止一种方法，如果你想要更少的列，那就去做吧。

最后，代码时间！

### 打造

总是先加价。不管这些天孩子们在做什么，我坚持自己的观点，首先从涨价开始。一个有趣的实验(也许对你来说不是，但对我来说肯定是)是看看你的网站在 Lynx 上的阅读情况。它确实可以很好地衡量网站内容的结构是否合理。

<figcaption>在我看来有道理</figcaption>

[![Web page rendered in Lynx browser](img/37e2aa4e9691af6ef1ef16ab085efa22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RFyy7w-3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/design-grid/lynx-640.jpg)

基本的视觉风格接下来是字体、颜色和文本格式。因为浏览器有自己的风格，我自己做了一些最小的重置，只是为了边距和填充，以及默认设置`box-sizing`为`border-box`。只是个人喜好。

```
main {
  max-width: 45em;
  margin: 0 auto;
  position: relative;
  padding: 1em;
}

_:-ms-input-placeholder, :root main {
  display: block;
}

h1 {
  font-family: $header-font;
  color: $accent;
  font-size: calc(3em + 7vw);
  margin-bottom: 0.25em;
}

h2 {
  text-transform: uppercase;
  font-size: calc(1em + 0.5vw);
  color: lighten($text, 50%);
  margin-bottom: 1em;
}

hr {
  opacity: 0;
}

.about {
  line-height: 1.3;
  margin-bottom: 1em;
}

a {
  display: block;
  text-transform: uppercase;
  text-decoration: none;
  color: $text;
  margin-bottom: 2em;
  font-weight: bold;
}

img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.location {
  text-transform: uppercase;
  line-height: 1.5;
  font-weight: bold;
}

button {
  border: 0;
  background-color: $accent;
  color: $main;
  text-transform: uppercase;
  font-size: 100%;
  padding: 1em 2em;
  position: absolute;
  right: 1em;
  bottom: 1em;
} 
```

Enter fullscreen mode Exit fullscreen mode

基本布局应该利用几乎是通用的 CSS，也就是到处都支持的属性。即使我总是说网页不需要在每个浏览器中都看起来完全一样，我的朋友们，破损是一种设计模式。这个基地布局可能看起来有点简单和无聊，但嘿，如果我想去看第谷，所有的信息都在那里，供我舒适地消费。

<figcaption>香草还好，真的</figcaption>

[![Basic fallback layout](img/7afd052f95a478e574eb543f68577f46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RhG3lRD5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/design-grid/fallback-640.jpg)

现在，我们可以开始玩网格游戏了。我选择灵活的单位，如 fr 和 viewport 单位，因为我希望在空间允许的情况下，布局完全占据窗口而不溢出。因此，如果您以前从未使用过 grid，那么看看我的 grid-template-columns 属性，它看起来有点疯狂。

所有的行都是相对于视口高度的，这就有点棘手了。我已经记不清我的初始值是多少了，因为有几轮调整，接着是疯狂地调整浏览器大小，然后是更多的调整……你明白了吧。

### 调整、拧动、冲洗、重复

最终我选定了这个:

```
@supports (display:grid) {
  @media (min-width: 42em) and (min-height: 27em) {
    main {
      max-width: none;
      padding: 0;
      display: grid;
      grid-template-columns: 2fr minmax(10em, max-content) minmax(14em, max-content) minmax(1em, 1fr) fit-content(28em) calc(2em + 0.5vw);
      grid-template-rows: 35vh 40vh 15vh 10vh;
    }

    h1 {
      grid-column: 3 / 6;
      grid-row: 1 / 2;
      z-index: 2;
      padding-left: 0.25em;
      margin-bottom: initial;
    }

    h2 {
      grid-row: 1 / -1;
      grid-column: 6 / 7;
      writing-mode: vertical-rl;
      margin-bottom: initial;
      color: $text;
    }

    hr {
      grid-column: 5 / 6;
      grid-row: 2;
      height: 6px;
      background-color: $text;
      width: 20ch;
    }

    .about {
      grid-column: 5 / 6;
      grid-row: 2;
      align-self: end;
      padding-bottom: 4vh;
      margin-bottom: initial;
    }

    a {
      grid-column: 5 / 6;
      justify-self: end;
      align-self: center;
      margin-bottom: initial;

      &::before {
        content: '';
        display: block;
        height: 4px;
        background-color: $accent;
        width: 4ch;
        margin-bottom: 1em;
      }
    }

    img {
      grid-column: 1 / 4;
      grid-row: 1 / 4;
    }

    .location {
      grid-column: 3 / 4;
      grid-row: 3 / 4;
      z-index: 2;
      background: $main;
      text-align: center;
      display: flex;

      p {
        margin: auto;
      }
    }

    button {
      grid-column: 2 / 3;
      grid-row: 4 / 5;
      position: initial;
      padding: 0;
    }
  }

  @media (min-width: 48em) and (min-height: 27em) {
    hr {
      opacity: 1;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最小高度查询放在那里是为了处理主标题下面的黑线。使用视口单位进行布局的问题是，布局中肯定会有断点。这就是为什么媒体的询问是非常重要的。因此，当上下文不再有意义时，您可以切换出视口单位。

这是最终的结果:

<video loop="" controls=""><source src="https://www.chenhuijing.com/assets/videos/grid-tycho.mp4" type="video/mp4">Sorry, your browser doesn't support embedded videos, but don't worry, you can [download it](https://www.chenhuijing.com/assets/videos/grid-tycho.mp4)and watch it with your favourite video player!</video> 

最初的灵感在风景视图上效果最好，只是因为它在肖像方向上效果不好，并不意味着我们不能采用它。我们的工作是思考如何为动态媒体设计，老实说，我觉得这非常有意义，也非常有趣。

除了在一个固定的维度中思考，还有什么媒介存在？我们开始考虑我们的设计如何在窄屏幕或旧浏览器上变形，以及它如何出现在具有最新功能的浏览器上。对我来说，这就是我们的媒体真正与众不同的地方。

## 包装完毕

我真诚地相信 CSS grid 将鼓励设计者和开发者探索更有创造性的布局，并且因为其直观而强大的本质而更少地依赖于预装的 CSS 框架。我不认为 CSS 框架会消失，而且肯定有它们的用例，但是我期待着 CSS grid 成为构建 web 布局的首选技术的那一天😎。