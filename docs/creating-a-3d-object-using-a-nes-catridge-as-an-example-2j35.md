# 以 NES 墨盒为例创建 3D 对象

> 原文：<https://dev.to/straleb/creating-a-3d-object-using-a-nes-catridge-as-an-example-2j35>

[![alt text](../Images/74674ebd7f96c20f9144a0e5e7a556f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iayVfLdl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27luep5dmwvj6z2dkbwa.jpg)

很长一段时间，我一直计划写一篇关于某事的文章，我不知道该写什么。最近，我看到了几个由 Web 开发人员完成的关于 3D 对象的帖子，所以我决定自己做一个项目，并以 NES 墨盒为例分享我创建 3D 对象的方法。我用过的游戏封面是 1988 年卡普空出品的游戏《超级男人 2 》,是系列中最著名的游戏之一。

## HTML 设置

在下面的 HTML 中，我在 div 标记中设置了包装器 id，在 section 标记中设置了 front 类，这是突出于插件的部分，用 li 标记表示插件中的多个矩形，如代码中所示。我要做的一件主要的事情是给 Id 和类起更有创意的名字，所以请原谅我。

```
 <div id="wrapper">
      <div class="catridge">
    <section class="front">
    <img src="http://www.mobygames.com/images/covers/l/43085-mega-man-2-nes-front-cover.jpg" 
alt="nes cover"
      id="cover-pic" >
<span class="triangle-down"></span>
      <ul>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
        <li id="lastLine"></li>
<!-- small rectangles left to the picture-->
      </ul>
  </section>
</div>
  <!-- front side of the catridge-->
    <div class=leftSide></div>
    <div class="rightSide"></div>
<!-- sides-->
<div class="top"></div>
<div class="bottom"></div>
  <!--Top and bottom-->
<div class="catridgeHoleLeft"></div>
    <div class="catridgeHoleRight"></div>
    <div class="catridgeHoleBackSide"></div>
</div>
</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

此外，我已经把 Z 轴的侧面放在了左边和右边，还添加了墨盒的顶部和底部。随着 CSS 的加入，我们将在下面的代码中为对象添加形状、大小和颜色:

```
 #wrapper{
  background:rgb(156, 156, 156);
  width: 30.95em;
  height: 35.7em;
  margin:8% auto 10%auto;
  border-bottom-right-radius:6px;
  border-bottom-left-radius: 6px;
  transform-style:preserve-3d;
  border: solid 1px black;
  }

.front{
  background: rgb(156, 156, 156);
  height: 40em;
  width:26.3em;
  margin:0px 2.15em auto;
  box-shadow: 0px 4px 0px 0px  rgba(0, 0, 0, 0.6);
}

ul{
  width:14%;
  margin:-415px 15px;
  border-right: 1px solid black;
  border-left: 1px solid black;
  text-align: center;
   box-shadow:inset 0 0 3px rgba(0, 0, 0, 0.66);
}

li{
    background:rgb(180, 176, 176);
    height: 1em;
    list-style: none;
    margin:10px 0px 10px -31px;
    box-shadow: 0px 0px 3px 0px rgba(0, 0, 0, 0.76);
    width:5em;
    border-radius: 1.5px;
  } 
```

Enter fullscreen mode Exit fullscreen mode

最后的造型和调教变成了这样:
[![alt text](../Images/234ef26001329780750a51fe6066c5a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--41hzSIM9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/co17grerslrt2ydpavmv.png)

当首页的成形完成后，剩下的一件事就是在 z 轴上成形墨盒的侧面。如果你想要一个关于 3D 物体的更详细的解释，我强烈推荐你去查看 CSS 和 JS 中的 3D 爬虫头部动画。作者 Yann Prono，他的文章极大地启发了我尝试 3D 物体创作。

## 回到正题🙂

在下面的代码中，我分享了我塑造 Z 轴侧面的方法

```
.catridge .leftSide{
  position:absolute;
  height: 35.7em;
  width:3em;
  background-color: rgb(156,156,156);
  margin: -40.05em  -10em;
 border:1px solid;
 transform: rotateY(100deg) translateZ(132px);
}

.catridge .rightSide{
  position:fixed;
  height: 35.6em;
  width:3em;
  background-color: rgb(156,156,156);
  margin: -40.05em  34em;
  box-shadow: 0px 0px 0px 0px rgba(0, 0, 0, 0.44);
  border:1px solid;
transform: rotateY(255deg) translateZ(70px);
  box-shadow: 0px 1px 5px 0px  rgba(0, 0, 0, 0.6);
}

.catridge .top{
  width: 30.9em;
  height: 1em;
  border-top: 200px ;
  border-bottom: 40px solid rgb(156,156,156);
  border-left: 9px solid transparent;
  border-right: 14px solid transparent;
  margin: -40.25em -0.55em;
  position: fixed;
  transform: rotatex(270deg) translateZ(-25px) translateY(13px);
  }

.catridge .bottom{
  width: 31em;
  height: 1em;
  border-top: 200px ;
  border-bottom: 40px solid rgb(156,156,156);
  border-left: 9px solid transparent;
  border-right: 13px solid transparent;
  transform: rotatex(270deg) translateZ(-100px) translateY(20px);
  margin:0 -0.6em;
}

.catridgeHoleRight{
  transform: rotateY(265deg) translateZ(100px) translateY(-5px) translateX(-10px);
  height: 4.2em;
  width: 3em;
  background:rgb(156,156,156);
  margin:3.9em 33.2em;
  border:1px solid;
  box-shadow: 0px 2px 9px 0px  rgba(0, 0, 0, 0.6);
}

.catridgeHoleLeft{
  transform: rotateY(95deg) translateZ(150px);
  height: 4.2em;
  width: 3em;
  background:rgb(156,156,156);
  border:1px solid;
  margin:-7.9em -8.9em;
}

.catridgeHoleBackSide{
  height: 1.1em;
  width:26.58em;
  background-color: rgb(156,156,156);
  transform: translateZ(-40px) rotateY(180deg) translateX(0px);
  margin:-5.5em 2em;
  border:1px solid;
  box-shadow: 0px 2px 8px 2px  rgba(0, 0, 0, 0.6);
} 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中使用 Yann Pronos 方法设置光标跟踪方法时，我使用了同样的风格，只做了一点小小的编辑。它确实工作得很完美，只是 x 轴和 y 轴的最大角度更大。

```
const followCursor = (element, options = {}) => {
options = Object.assign({}, {
  maxAngleX: 30,
  maxAngleY: 18,
}, 
```

Enter fullscreen mode Exit fullscreen mode

我已经决定在第一个 3D 项目中使用“em”度量。成品是为笔记本电脑和台式电脑设置的，在一些未来的项目中，我会把媒体查询放在较小的屏幕上。这是成品的样子，最好在 CodePen 上查看一下🙂

[https://codepen.io/SBabic/embed/ervmRY?height=600&default-tab=result&embed-version=2](https://codepen.io/SBabic/embed/ervmRY?height=600&default-tab=result&embed-version=2)

这是我写的第一篇文章，希望你喜欢。通过做更多的项目，我每天都在进步，我准备学习新的东西，写更干净更好的代码，我希望有更好的文章😃。

再次想给 Yann 一个巨大的欢呼，因为他的精彩文章是一个巨大的灵感。
再次小小提醒一下，看看他关于 3D 物体动画
[的精彩文章，用 CSS 和 JS 制作一个 3D 爬虫的头部动画！](https://dev.to/mcdostone/animating-a-3d-creepers-head-in-css-and-js--2j6a)