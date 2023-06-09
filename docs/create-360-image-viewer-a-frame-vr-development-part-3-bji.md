# 创建 360°图像浏览器-A 帧 VR 开发第 3 部分

> 原文：<https://dev.to/hasanaliqureshi/create-360-image-viewer-a-frame-vr-development-part-3-bji>

[![](img/3c66912769ca706ae4b11e18d9d3aabb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqUuK788--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/360-image-viewer.png)

这是我们第一个使用 A-Frame VR 框架的虚拟现实项目。我将使用 A-Frame 制作一个 360 图像或 photosphere 查看器。这将是超级容易的，我们还会添加互动元素。让我们开始吧

A 帧 360°图像浏览器

1.  首先，用基本的 Doctype 代码创建一个 HTML 文件，并将 a-frame.js 文件添加到其中

[![360-image-viewer-a-frame-vr](img/84f7c478cd37a14a02c1cdc067db1183.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u2vPOSR7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/01-2-1024x559.png)

1.  现在从谷歌或任何地方下载任何 360 图片或 photosphere。我用的是这张图([https://drive . Google . com/drive/folders/0 bx 2 k1 bqmoviiw 9 HQ 2 ktm 05 zvk 0](https://drive.google.com/drive/folders/0Bx2k1bqMoviiWW9hQ2ktM05zVk0))

2.  现在，为了在 A 帧中查看该图像，我们将创建一个场景，并将带有属性 src 链接的“”图元添加到该图像

3.  现在，如果你打开 HTML 文件，你会看到你的 360 图像，你可以通过拖动鼠标来移动它。

[![](img/86298c2f984a243b5f0b6278a10fca21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--megoargJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/02-1-1024x522.png)

这就是在 A-Frame 中观看 360°图像的简单方式。现在让我们给 360°图像添加文本。

要添加文本，我们将使用原语。我将在场景中添加以下代码。

> rotation="0 5 0 ">

现在刷新后，您将使用 360°图像发送文本。

[![360-image-viewer-a-frame-vr](img/cef1854995489c0cf74caf9ae40d9bec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vW2z6RaA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2018/10/04-1-1024x522.png)

在下一个教程中，我将向你展示如何使用 A-Frame 创建一个虚拟旅游体验，并创建可以通过点击改变 360 度图像的交互点。

Github 上的源代码:[https://Github . com/hasanaliqureshi/A-Frame-VR-Development/blob/master/A-Frame-VR-Development-Part-3 . html](https://github.com/hasanaliqureshi/A-Frame-VR-Development/blob/master/A-Frame-VR-Development-Part-3.html)