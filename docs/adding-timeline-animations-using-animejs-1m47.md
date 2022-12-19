# 使用 anime.js 添加时间轴动画

> 原文：<https://dev.to/superissarah/adding-timeline-animations-using-animejs-1m47>

## 概述

这篇文章介绍了我在项目中添加一些动画的过程。我讲述了我想添加什么动画以及为什么要添加，我如何决定使用哪个动画库，我如何使用时间线来制作一些形状，以及我如何处理 CSS 动画与 JavaScript 库动画冲突的问题。

撰写发布时间:2018 年 10 月
使用:anime.js v2.2

## 项目

我做了一个 [Glitch](https://glitch.com/) app，列出了你发布到 GitHub 页面的 GitHub 库。可以在[好奇-octocat.glitch.me](https://curious-octocat.glitch.me/) 查看。

尽管页面上没有太多让你分心的东西，但我还是想添加一些动画来清楚地将注意力引向输入框，然后引向它下面显示结果的地方。我认为后者更需要帮助——因为结果可能在文件夹下完全不可见地加载。出于连续性的考虑，我希望有一个类似的动画指向输入框，这样访问者就会熟悉这个动画和它的意思。

这是我计划出来的原始设计。

[![a screenshot of a mock up with around 8 shapes in an inverted triangle point to an input box](../Images/5df9d28e7c9e20d2608ef8436cdd936f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MXYNbUC_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/suql5qalbwxr875mq1lb.PNG)

### 目标

☐有形状从页眉落下形成一个指向输入框的模糊箭头
☐有形状在被聚焦时落入输入框
☐有形状从输入框落下指向结果出现的地方
☐有形状在用户没有发布回复时变成一张闷闷不乐的脸
☐有一些形状不断旋转
☐在形状悬停时做一些小动画

## 挑选一个动画库

一开始我想过用 [p5.js](https://p5js.org/) 。我的想法是在输入后面绝对放置一块画布，然后按计划在它周围制作一些形状的动画。当我想到另一条路的时候，我大概只能走到这里了。当我试图弄清楚如何控制动画在 p5 上播放时，我想起了我以前尝试过的另一个动画库: [anime.js](http://animejs.com/) 。

当我重温他们的文档时，我意识到他们的例子正是我想要做的，而且他们也有暂停和播放动画时间线的清晰例子。所以尽管我很确定你可以用 p5.js 做我想做的所有事情，我还是决定用 anime.js 来代替。

## 使形状下降

我最终为每一个下落形状的动画制作了一个时间线:从头部落下，落入输入，从输入落下，从输入落下变成一张皱眉的脸。起初，我想创建一个大的时间线，并根据需要播放和暂停，但我没有看到在时间线中跳跃的方法。制作更短的时间线最终会更容易编辑和处理每个动画。

下面是你如何用 anime.js 制作时间线:

```
 this.fallFromHeader = anime.timeline({
      autoplay: false,
      duration: 600,
    }); 
```

我在类`Shapes`中有我的，这就是为什么我把它附加到`this`上。你可以很容易地把它保存到一个变量:`const timeline = anime.timeline();`。每个时间线都被设置为不自动播放(`autoplay: false`)，所以我可以在该播放的时候播放。给定的持续时间(`duration: 600`)不是整个时间线的总持续时间，而是添加到时间线的每个动画的默认持续时间。

要将动画添加到时间线，您可以使用它的`add()`方法，并向它传递一个对象，该对象包含该动画的更改和属性:

```
 this.fallFromHeader.add({
      targets: '#shapes .one',
      delay: 1000,
      translateY: [0, 90],
      translateX: 100,
      offset: '-=400'
    })
    .add({
      targets: '#shapes .two',
      translateY: [0, 130],
      translateX: 150,
      offset: '-=400'
    })
    .add({
      targets: '#shapes .three',
      translateY: [0, 170],
      translateX: 120,
      offset: '-=400'
    })
    .add({
      targets: '#shapes .four',
      translateY: [0, 150],
      translateX: 180,
      offset: '-=400'
    })
    .add({
      targets: '#shapes .five',
      translateY: [0, 60],
      translateX: 50,
      offset: '-=300'
    })
    .add({
      targets: '#shapes .six',
      translateY: [0, 80],
      translateX: 200,
      offset: '-=300'
    })
    .add({
      targets: '#shapes .seven',
      translateY: [0, 80],
      translateX: 145,
      offset: '-=250'
    })
    .add({
      targets: '#shapes .eight',
      translateY: [0, 220],
      duration: 1200,
      translateX: 65,
      offset: '-=200' 
```

我使用的属性是:

*   目标:标识动画元素的 CSS 选择器
*   延迟:将动画延迟指定的毫秒数
*   持续时间:动画应该持续多少毫秒
*   translateY:数组中的第一个数字是元素的起始 Y 坐标，第二个数字是元素的结束 Y 坐标
*   translateX:将元素放置在 X 坐标上
*   offset:告诉动画在上一个动画结束之前开始一定数量的毫秒

下面是同样的时间轴:
[![triangles, cricles, and squares falling from a header](../Images/d2b995b502db56ccd03101ceb10cd9b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yokSoQM0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ate6hgb9in7ifcgby875.gif)

在对所有形状使用不同的 X 和 Y 坐标后，我用相似的代码完成了剩余的时间线。

☑️有形状从标题上落下来形成一个指向输入框的模糊箭头
☑️有形状在它被聚焦时落入输入框
☑️有形状从输入框落下来指向结果出现的地方
☑️有形状在用户没有已发布的回复时落入一张皱眉的脸

## 使形状旋转并在悬停时做出反应

### 问题

当我开始致力于使某些形状旋转的目标时，我决定使用一个简单的 CSS 动画。用 anime.js 完全可行，但由于这是一个简单的动画，我不需要控制它何时开始或停止，我认为使用 CSS 关键帧是一个不错的方法。

我决定让所有的三角形旋转。下面是我写的代码:

```
 .triangle {
  /* more CSS rules n' stuff */
  animation: spin 3s infinite linear;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(-360deg);
  }
} 
```

下面是我刷新页面时发生的事情:
[![the same shapes falling from a header, but missing the triangles](../Images/1e98b70e61e8aa010f8189ba154bd3b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XfGOaprl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wsihktz2ibwjd2c1tjre.gif)

我的三角形不见了！当我检查大教堂时，我看到他们在他们出发的地方发抖，旋转。
[![a div element in the header is highlighted and shown spinning](../Images/43bd4ac1163568f77d2dba78be726180.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--zV-4iQB6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3uczdmrx0oqpdbc0bowi.gif)

我意识到 CSS 动画和 anime.js 动画有冲突。他们都在改变三角形的属性。即使 anime.js 内联应用了更新的变换值，更新`transform`属性的 CSS 动画似乎会覆盖它们，而不会应用`translateY`或`translateX`的更改。

### 我的解决方案

我仔细考虑了一个可能的解决方案，通过获得一个三角形的结束位置并使用 CSS 变量和动画填充模式或其他方式更新 CSS，将`translateX`和`translateY`值添加到关键帧动画中，但最终我想到了一个更简单的想法:将不同的动画应用到不同的元素。基本上，在一个移动的 div 中嵌套一个旋转的三角形。

这些是我为形状准备的原始元素:

```
<div id="shapes">
   <div class="shape circle six"></div>
   <div class="shape circle large one"></div>
   <div class="shape triangle two"></div>
   <div class="shape triangle eight"></div>
   <div class="shape triangle five"></div>
   <div class="shape triangle four"></div>
   <div class="shape square three"></div>
   <div class="shape square seven"></div>
</div> 
```

下面是嵌套 div 的形状:

```
<div id="shapes">
   <div class="one"><div class="shape circle large"></div></div>
   <div class="two"><div class="shape triangle"></div></div>
   <div class="three"><div class="shape square"></div></div>
   <div class="four"><div class="shape triangle"></div></div>
   <div class="five"><div class="shape triangle"></div></div>
   <div class="six"><div class="shape circle"></div></div>
   <div class="seven"><div class="shape square"></div></div>
   <div class="eight"><div class="shape triangle"></div></div>
</div> 
```

我甚至不需要修改 CSS 或 JavaScript。anime.js 动画已经使用数字类将元素作为目标，而形状是使用 shape 和 shape name 类创建的。

当我刷新页面时，它完美地工作了！
[![the shapes falling from the header with spinning triangles](../Images/c05ce0f0797ba8ed91f17b251033c07e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--0bQyRa-X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z8smq31ok15u3ca4ymct.gif)

☑️有一些形状不断旋转

将形状嵌套在移动的 div 中也使我的最后一个目标很容易实现，因为我想再次使用 transform 属性。当一个形状被悬停在上面时，它只会增长一点点。
[![a mouse hovering over shapes that grow](../Images/0d33a690737c8f1c8d60745ec4ca9541.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--FwnihX_k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mjbm8g1hi64maiepxhfv.gif)

下面是代码:

```
.shape {
  /* more CSS rules n' stuff */
  transition: transform .5s;
}

.shape:hover {
  transform: scale(1.2);
} 
```

我应该注意到三角形不受影响，尽管它们也有一个 shape 类。我觉得还是以前的问题:持续运行的 CSS 关键帧动画在使用 transform 属性，所以它不能被其他设置它的尝试打扰。

☑️做一些小动画时，一个形状是悬停

## (大部分)都在一起

最后，这是大部分动画的 gif 图:从头部落下的形状，落入输入框的形状，从输入框落下的形状，以及旋转的三角形。

[![using the app to see all the repos a user has published, with shapes falling from the header, into the input box, then below it](../Images/bc58668a201bcd4c4e32fb480e1212f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6KR2bhLy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cl1yy6tbzl0u2yuskgqa.gif)

## 仍在进行中

我还想给这个项目添加很多东西。我希望每次焦点在输入框时，这些形状都以动画的形式回到输入框中，但是是从它们所在的位置开始。如果用户在没有提交的情况下点击输入框，我希望形状放在边上。我想让输入框对进出它的形状做出反应。也许当所有的形状都在里面的时候它会变大，当它们离开的时候会缩小一点。或者也许它会反过来朝相反的方向推一下。我还不知道！

## 感谢阅读！

我很想听听你在做什么动画，或者你对我遇到的问题有什么反馈或其他解决方案。