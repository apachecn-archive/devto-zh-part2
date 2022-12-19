# 入门——A-Frame VR 开发第 1 部分

> 原文：<https://dev.to/hasanaliqureshi/getting-started--a-frame-vr-development-part-1-1i1i>

[![](../Images/9a95a1635d6dc9febd891e930a796719.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mmxtaxJv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/Copy-of-Rufus-Strikes-Wins1.png)

这是 A-Frame 虚拟现实开发系列的第一部分，在这一部分中，我将解释 A-Frame 框架的基本组件和语法，并将使用 A-Frame 创建一个简单的 Hello World 虚拟现实项目。

在开始之前，如果你不知道基本的 web 开发技术，并且从未从事过 web 工作，那么请首先学习一些 HTML、CSS 和 Javascript 的基本技能，然后按照本教程学习。

在整个教程中，我将一步一步地用图片分享代码，这样你就可以很容易地理解。所以让我们开始吧。

创建一个*index.html*文件，并在其中编写基本的 HTML DOCTYPE 语法。

[![](../Images/1b9108796dee29ec94459d542080784d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Puupk9f0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/01-1024x586.png)

将 A-Frame 框架脚本插入到`\<head\>\</head\>`容器中

```
\<script src="https://aframe.io/releases/0.8.0/aframe.min.js"\>\</script\> 
```

`Note : You can also install A-Frame using NPM. Use` $ npm 安装框架`command.`

现在让我们探索一下 A 帧语法。A-Frame 基于自定义创建的 HTML 元素。如下图所示，每个对象都由一个自定义的 HTML 元素表示。

[![](../Images/c9f281d8b081021a93cea9708112c8ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kypl-U0U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/02-1024x579.jpg)

## 创建场景

首先，我们必须创建一个场景，这将为我们配置像画布，渲染，相机，灯光等基本组件。这是一个容器，虚拟现实环境的所有元素都将进入其中。

在主体内部添加`_\<a-scene\>\</a-scene\>_`标签来初始化场景。

[![](../Images/d1006e0dec328baf08bb57d75e193b07.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3llPcKSU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/03-1024x586.png)

如果你在浏览器中打开*index.html*文件，你会注意到在初始化场景后，页面上会出现一个小的虚拟现实图标。

## 添加立方体/盒子

让我们在其中插入一个 3d 盒子。

添加`_\<a-box color=”red”\>\</a-box\>_` `inside the` *< a-scene >* `标签。

现在，如果你刷新 index.html，你将什么也看不到。但是如果你按键盘上的 S 键，相机将开始向后移动，你会看到一个红色的框。你没有首先看到盒子的原因是相机和盒子的位置是相同的，两者都位于屏幕中的同一点(原点[0，0，0])。

[![](../Images/e46b479f0883f4e3511af21aabddfddf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2_soqMbM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/04-1024x586.png)

现在，在添加更多元素之前，让我告诉你什么是 A-Frame 中的图元和实体。

`_\<a-scene\>_`、 `_\<a-box\>_`、 `_\<a-sphere\>_`等都是由 A-Frame 预定义的自定义 HTML 标签，是易于使用的标签，称为原语。

一方面`\<a-entity\>`可以由任何元素组成。它可以是盒子、场景、球体、灯光或相机。所有的`_\<a-scene\>_`、 `_\<a-box\>`、*、` _<a-cube>*`etc are the pre-defined entities for ease of use. However, if you want custom shapes and advanced features like sound in your VR environment`<a-entity>`都会在那里帮到你。

例如，在`_\<a-box\>_`标签下有一个带有预定义位置和几何图形的`_\<a-entity\>_`标签:

*<a-实体 id = " box " geometry = " primitive:box " material = " color:red "></a-实体>*

我希望你明白了原语和实体之间的区别。如果你仍然感到困惑，不要担心，一旦我们使用<a-entity>标签，你会更好地理解它。</a-entity>

## 更改属性

改变原语的属性也非常容易。让我们把盒子放在照相机前面。

在`a-box`标签中添加一个位置属性。Position 属性指定元素在 X、Y 和 Z 轴上的位置。

*< a 盒 position = " 0 2-5 " color = " red "></a 盒>*

在`a-box`标签中添加一个旋转属性。旋转属性指定元素在 X、Y 和 Z 轴上的旋转。

*<a-box position = " 0 2-5 " rotation = " 0 45 45 " color = " red "></a-box>*

并添加 scale 属性来缩放 X、Y 和 Z 轴上的元素。

*<a-box position = " 0 2-5 " rotation = " 0 45 45 " scale = " 2 2 2 " color = " red "></a-box>*

现在你的盒子看起来会像这样。

[![](../Images/e1d65aa43019a1d3d667dc5b86841716.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hf9nf91d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/05-1024x586.png)

摆弄一下盒子的属性都列在这里:[https://aframe . io/docs/0 . 8 . 0/primitives/a-box . html # attributes](https://aframe.io/docs/0.8.0/primitives/a-box.html#attributes)

## 添加背景/天空盒

天空盒是 3D 世界的背景。`_\<a-sky\>\</a-sky\>_`用于在场景中放置背景。

*<阿天 color = " # 94c 5d 6"></阿天>*

在`_\<a-scene\>_`容器中添加`_\<a-sky\>_`标签。我使用了简单的十六进制编码的颜色作为天空颜色，但是你也可以使用图像作为天空背景。添加天空后，你的场景看起来会像这样

[![](../Images/4b6da27a1a690a1e47d503eef539fc54.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BpvrNpWi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/06-1024x586.png)

在这一部分中，我们已经介绍了 A-Frame 框架的基本语法，制作了一个带有盒子的简单场景，并为其添加了属性。在下一个教程中，我们将研究相机，灯光和动画各自的属性。

Github 上的完整代码:[https://Github . com/hasanaliqureshi/A-Frame-VR-Development/blob/master/A-Frame-VR-Development-Part-1 . html](https://github.com/hasanaliqureshi/A-Frame-VR-Development/blob/master/A-Frame-VR-Development-Part-1.html)

虚拟现实巴媒出版:[https://medium.com/vr-pakistan](https://medium.com/vr-pakistan)

虚拟现实巴基斯坦脸书集团:[https://www.facebook.com/groups/VirtualRealityPK/](https://www.facebook.com/groups/VirtualRealityPK/)