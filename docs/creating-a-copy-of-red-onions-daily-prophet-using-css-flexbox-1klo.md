# 我用 CSS Flexbox 创建了一份红洋葱的《预言家日报》

> 原文：<https://dev.to/analizapandac/creating-a-copy-of-red-onions-daily-prophet-using-css-flexbox-1klo>

如果你曾经看过 [Red Onion 的《预言家日报》页面](http://redonion.se/cssgrid/)，那么你就会知道这是一个非常棒的、创造性的布局，它是使用 CSS Grid 构建的。这类似于老式的印刷布局，在网络上复制似乎很有挑战性。如果你想学习如何使用 flexbox 布局，使用 flexbox 而不是 css grid 来创建它的克隆将是一个很好的挑战。(*我猜我学长发给我链接的时候也是这么想的。*)

虽然 flexbox 和 css grid 已经出现了一段时间，但我并没有真正使用它们，因为我大部分时间都依赖于现有的 css 框架(我知道)。

所以我决定做这个练习，看看它是否真的像看起来那么复杂，下面是我学到的东西。

## 在盒子里看东西

当你第一次看的时候，页面布局看起来非常复杂，但是我发现如果我把每一部分都看成一个盒子，会更容易。这是页面在框中的样子。

[![Daily Prophet's sections as seen in boxes](img/0db4f5ec22d4fa27c1662e92af9dfa3a.png "Daily Prophet's sections as seen in boxes")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tanWJNNF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7gb2hsh0c147gqemi5so.png)

## 内嵌和阻塞元素

一些框相互内联显示，而另一些则成块显示。默认情况下，当一个弯曲的父长方体有几个子体时，这些子体会并排显示在一行中。

[![Items displayed inline](img/d02efbd36a96d0dd06ce13edef69b4a9.png "Items displayed inline")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yFael_iy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/igvz7gvuslim29vm9u1e.png)

为了在每个子块中显示每个子块，可以使用 **flex-direction** 属性作为**列**。

[![Items displayed in blocks](img/4e24c0ce89a6265c112cb984f3fbf709.png "Items displayed in blocks")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hhCxbSFW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1tgngg2z64e2t04egg44.png)

## 包裹重叠元素

同样要考虑的最重要的事情之一是页面在更小的设备中的外观(*或页面响应性*)。默认情况下，使用 flexbox 时，项目不会换行。因此，随着屏幕变小，一些内容将看不到。为了使它们适应新的视口大小，您必须将**伸缩包裹**属性声明为**包裹**。

```
.parent {
    display: flex;
    flex-wrap: wrap;
    flex-direction: row;

    .child {}
} 
```

Enter fullscreen mode Exit fullscreen mode

## 将事物放在一个元素的中心

如果我需要在元素内部垂直和水平居中文本，在过去，我会在父元素和子元素上使用定位。

但是对于 flexbox，你可以使用 **justify-content** 和 **align-items** 来实现这种行为。

```
<div class="exclusive-story__marker">exclusive</div>

.exclusive-story__marker {
    display: flex;
    align-items: center;
    justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 其他学习

### 当原始字体不可用时，使用替代字体

原始页面使用**标题一 HPLHS** 作为故事标题，但我在谷歌字体上找不到确切的字体，所以我决定使用衬线字体，因为它看起来也适合报纸标题。如果有完全相同的字体就好了，但目前没有必要。我的目标是学习如何使用 flexbox，而不是制作一个像素完美的页面副本。

### 避免在 div 上使用高度，以防止较小屏幕上的内容重叠

以方框的形式查看页面部分是一个非常有用的方法，但它也让我站在高处思考。是的，高度。我开始给那些看起来有特定高度的盒子分配高度，比如出版物名称部分和独家报道部分。

但是当我减小视口的尺寸时，其中的文本和图像与 flex 容器重叠，所以我决定删除这些高度声明。

### 最后...

1.  使用对象适配:封面裁剪图像并显示中心

2.  使用首字母伪代码使段落的首字母大于句子的其余部分，就像它在报纸上的表现一样。

总的来说，我真的很喜欢做这个练习。我对 flexbox 的工作原理有了更多的了解。还有很多 flex 属性我没有使用过，比如 flex-basis、order、flex-shrink，但是现在还可以，我可以继续学习。

感谢阅读，可以查看克隆页面 [@CodePen](https://codepen.io/analizapandac/pen/NLMemQ) 。

有反馈/意见，请告诉我。

**预言家日报版权归[红洋葱](http://redonion.se/en/home/)所有。*

*附:没有我的学长/导师迪内什·潘迪扬 [@flexdinesh](https://dev.to/flexdinesh) 的鼓励，这是不可能的。谢谢你阿宝。*