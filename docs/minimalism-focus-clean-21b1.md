# 极简主义，专注，干净

> 原文：<https://dev.to/asaaki/minimalism-focus-clean-21b1>

从你现有的开始，但不要停止追求更好的。为什么我喜欢极简和简单的网页设计。

* * *

### 一:没有什么是永远

几天前，我让一位同事看了我刚孵化的博客，因为我想让他看看我最近写的文章。最后，我们甚至没有谈论职位，而是谈论网页设计和工作(他是一名网页设计师)，挑战和机遇，要做的有趣的事情，创造力和过去，以及提醒我不再是最年轻的人。后面一直唠叨的是对我(现已过去)设计的评论。

[![markentier.tech in old design](img/be2938c9df54525a9c0d058dbb9357db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1A-it6CS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/05/minimalism-focus-clean-redesign/mtt-old.png)

> 哦，伙计，你需要更新你的网站
> 
> 想要那个婊子的设计帮助吗？

虽然我很感谢大家的帮助和支持，但尤其是对于我的个人项目，我倾向于尽可能多地自己完成。口味也是高度主观的；我住在德国柏林，我看到很多东西，我知道我在说什么。我不需要认同别人的风格和展示，也不需要认同别人的作品。我点点头(或耸耸肩)，我走了，反正十分钟后可能就忘了。

但是上面的信息是有意义的。一些他甚至不知道的事情。这个设计是一个临时的解决方案，我从一个 4 年前的概念验证工作中挖掘出来的。这是让我的网站不显得太寒酸的最快的方法。我不需要在其他地方寻找一个预先做好的设计，也不需要花太多时间从零开始创造一个。

虽然一个旧的设计不一定是糟糕的，但我的设计并没有真正完成，也没有经过充分的思考。所以这激发了做点什么的冲动。

我也知道我想从一个重新设计独立地改变标志，但是现在所有的事情走到了一起。

[![m (reference logo file; SVG)](img/95b31149fde3ae651aa68d3a598a2297.png)T2】](/m.svg)

### 二:一切皆有可能

我喜欢极简主义，真的。我希望我能在日常生活中更多地遵循这个原则，因为现在数码相机已经足够了。

我以前的设计和主题并不都是极简、简单甚至易用的。有些是艺术的；我认为在 21 世纪初，艺术、图形和非常详细的主题是一件事。遗憾的是，我忘了将一些我最喜欢的加入书签(并截图)，我甚至不记得这些人的名字，所以没有提示如何再次找到他们。好吧，反正都是历史了。

最后，我总是喜欢一个没有超载的设计和布局。有时我会在这里或那里添加一些东西。过了一会儿，我想我应该再去掉一些东西。

还有:极简主义并不一定意味着完全的空虚或者什么都没有。但是我开始喜欢上了巨大的空白，和广阔无垠的平原。

```
<!-- basic SVG logo markup for markentier.tech -->

  <path fill="none"stroke="#0c1328"
        stroke-linecap="round"
        stroke-linejoin="round"
        stroke-width="64"
        d="M480 506v208-128s0-72 64-72 64 72 64 {…}"/>
 
```

### 三:有些事情正在改变

我喜欢矢量图形。所以我喜欢 SVG。我记得从发现开始就一直喜欢这种图像格式。令人难过的是，它也花了相当长的时间才有适当的跨浏览器支持。如果你忽略旧 IEs，那么现在的情况是可怕的。SVG v2 还没有出现(我真的很想去掉`xlink`名称空间)，但是当前实现的规范对于很多用例已经很好了。

一个完美的例子是标志和图标。正如你已经看到的，我的网站的徽标是以 SVG 格式提交和呈现的，我不能让它比 SVG 更小，任何 PNG、GIF 或 JPEG 总是比下面的路径定义大得多:

> m480 506 v208-128 s0-72 64-72 64 72 64 72 v128-128 s0-72 64 72 v128-128 s0-72 64 72 64-72 64 72 185 1128 c-1 28-24 54-24 54 54-126 161-348 207-527 111-179-99

是的，完全正确，这就是全部。这可能是我能使用的最紧凑的版本。因此使用了 1024x1024 的可视框和大数字，只是为了避免小数，因为小数带有额外的点。这是一个亲小费顺便说一句，总是在一个巨大的画布上设计你的东西 1000 像素或更大的尺寸。然后使用像 SVGOMG 这样的工具(或者已经在你的矢量图形程序中),你可以保存零小数的文件(所以只有整数),而不会丢失重要的信息。

如果您想对 SVG 更感兴趣:

[https://www.youtube.com/watch?v=lMFfTRiipOQ](https://www.youtube.com/watch?v=lMFfTRiipOQ)

[https://www.youtube.com/watch?v=ADXX4fmWHbo](https://www.youtube.com/watch?v=ADXX4fmWHbo)

肯定还有更多，但以上视频应该是一个不错的起点。将来我可能会回到这个话题。

[![markentier.tech new design](img/61497bbcc89f9d3d406a85758084ddee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CUsz9GSN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/05/minimalism-focus-clean-redesign/mtt-now.png)

### 四:万物都在进化

上面的截图可能看起来有点奇怪(当你在 [markentier.tech](https://markentier.tech/) 上阅读这篇文章时)，因为它完全符合当前的设计。

我觉得这代表了一种对极简主义的诠释。至少有一个我喜欢的。没有太多的过程或科学，至少不是有意识的。当然，我知道一些基本的颜色、对比和对可及性的基本理解。还不会完美。可能我不会做一些你想看到的改变。

但我确实相信我的文章应该是可消费的，没有太多的分心或疲劳或紧张。由于它的色彩范围大大减少，色盲(或有部分缺陷)的人应该能够阅读和浏览网站，而不会丢失任何东西或感到困惑。我没有测试过屏幕阅读器，但是我希望我没有使用会影响你在这里的体验的设计魔法。

[![markentier.tech new design, page scrolled down (shows tiny logo and sticky navbar)](img/e1d0d6653846f66003f25f86478470e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---pVkIEg_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://markentier.tech/posts/2018/05/minimalism-focus-clean-redesign/mtt-now-scrolled.png)

我也仍然致力于它的性能方面。我也推动更现代的浏览器。因为这是我的个人博客，我想我可以“失去”一些客户，或者至少不会因为不总是完全向后兼容而给他们带来完全的快乐。

* * *

如果你有问题，或者只是想更详细地了解这里的工作方式，可以在 [twitter](https://twitter.com/asaaki) 上给我发短信，或者在 [repo](https://github.com/markentier/markentier.tech/issues) 为这个网站发帖，我很乐意解释，很高兴你能学到一些东西，我也欢迎你的反馈和指正。