# 摄像机、灯光和动画——A 帧虚拟现实开发第二部分

> 原文：<https://dev.to/hasanaliqureshi/camera-lights--animations--a-frame-vr-development-part-2-4m3n>

[![a-frame-vr-development-part-2](img/b11567ba1908b36863359f6351c54949.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VUaBKNM1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/a-frame-vr-development-part2.png)

欢迎来到 A-Frame 网络虚拟现实框架教程系列的第二部分。在第一部分中，我们学习了基本的 A 帧语法，创建了一个场景，在其中添加了一个盒子，并在其中设置了环境/天空盒。

现在我们将继续我们的文件 index.html。这是我们在第一部分中创建的，并切换相机设置，看看灯光如何影响场景和动画我们的对象。

## 照相机

默认情况下，A-Frame 会使用默认设置自动创建一个相机元素，如果您没有像在[第 1 部分](https://dev.to/hasanaliqureshi/getting-started--a-frame-vr-development-part-1-2ce7-temp-slug-284076)中那样创建它，我们没有创建相机实体，但 A-Frame 为我们创建了它，我们可以在场景中看到这些元素。

但是，如果您想要一个自定义设置的相机，那么您可以创建一个具有所需属性的相机元素。

> <a-entity>T3】</a-entity>

在<a-scene>的结束标签前添加上述标签。我在<a-entity>中封装<a-camera>标签的原因是，如果我们想要改变摄像机的位置，我们将定义<a-entity>的属性，因为如果我们直接在<a-camera>上设置属性，控件将覆盖它们。</a-camera></a-entity></a-camera></a-entity></a-scene>

这里看相机属性:[https://aframe . io/docs/0 . 8 . 0/primitives/a-camera . html #属性](https://aframe.io/docs/0.8.0/primitives/a-camera.html#attributes)

## 改变摄像机位置

要改变摄像机，只需在原语中设置位置属性。

> <a-entity position="“0">T3】</a-entity>

## 将光标添加到摄像机

任何虚拟现实环境中最关键的元素之一是光标，如果没有手控制器，它是允许在虚拟现实世界中交互的主要元素。谷歌纸板游戏使用光标与虚拟环境互动。

要将光标添加到摄像机，只需将<a-cursor>标签放在<a-camera>标签内</a-camera></a-cursor>

> <a-entity position="“0"><a-camera><a-cursor></a-cursor></a-camera></a-entity>

现在你会在屏幕中央看到一个圆圈。

[![a-frame-vr-development-part-2](img/5720a00786a01e3128990998b51345bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CPPCIF14--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/01-1-1024x586.png)

在未来的教程中，当我们创建完全交互式的虚拟现实环境时，我将向你展示如何制作光标凝视效果以及如何与物体互动。

## 添加灯光

灯光使 3d 环境更加真实。

在<a-scene>中添加<a-light>原语。我们添加的灯光是定向的，它的颜色是白色(#FFF)，强度是 0.5，位置是-1 1 2。</a-light></a-scene>

> type = " directional "
> 
> color = " # FFF "
> 
> intensity = " 0.5"
> 
> position = "-1 1 2">
> </a-light>

你会看到盒子的颜色是如何变化的。

[![a-frame-vr-developmet-part-2](img/1010d2382c676c7786d7bd18aa394f94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--stYc165j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/02-1024x586.png)

## 制作对象动画

要激活屏幕上的对象，请将<a-animation>标签放入该对象标签中。一个<a-animation>标签有不同的属性，您可以根据这些属性来配置动画。让我们制作盒子的动画。用下面的代码替换<a-box>标签。</a-box></a-animation></a-animation>

> <a-box position="”0" rotation="”0" scale="”2" color="”red”"><a-animation
> 
> attribute = " rotation "
> 
> from = " 0 45 45 " to = " 0 410 410 "
> 
> direction = " alternative " dur = " 4000"
> 
> repeat = "不定" ease = " ease ">
> 
> </a-animation>
> 
> </a-box></a-box>

[![a-frame-vr-development-part-3](img/8351024f7481b46311937a05478687a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4UFOOd1g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/03.gif)

不仅仅是旋转，你还可以动画显示物体的位置和大小/比例。检查动画属性并使用它进行试验[https://aframe . io/docs/0 . 8 . 0/core/animations . html # attributes](https://aframe.io/docs/0.8.0/core/animations.html#attributes)

我们已经介绍了 A-Frame 框架的基础知识。现在，从下一个教程开始，我们将开始制作虚拟现实项目，在其中我们将学习先进的概念，并深入研究 WebVR 开发。