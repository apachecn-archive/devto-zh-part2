# 带有视频的 html2canvas

> 原文：<https://dev.to/protium/javascript-rendering-videos-with-html2canvas-3bk>

如果你曾经使用过 html2canvas 来获得你的网络应用的“截图”,你可能知道 html2canvas 还不能处理视频，它呈现一个空白的正方形。他们在做这个专题(我想)。因此，让我们同时编写一个解决方法。

在调用 html2canvas 方法之前，我们需要捕获每个视频。通过阅读一些源代码和 github 资源库中的问题，我发现 html2canvas 可以渲染它找到的所有图像，所以如果我们有一个图像作为视频的背景，它会在最终的画布中渲染它。

```
let canvas = document.getElementById('canvas') // declare a canvas element in your html
let ctx = canvas.getContext('2d');
let videos = document.querySelectorAll('video')
let w, h
for (let i = 0, len = videos.length; i < len; i++) {
    const v = videos[i]
    if (!v.src) continue // no video here
    try {
        w = v.videoWidth
        h = v.videoHeight
        canvas.width = w
        canvas.height = h
        ctx.fillRect(0, 0, w, h)
        ctx.drawImage(v, 0, 0, w, h)
        v.style.backgroundImage = `url(${canvas.toDataURL()})` // here is the magic
        v.style.backgroundSize = 'cover' 
        ctx.clearRect(0, 0, w, h); // clean the canvas
    } catch (e) {
        continue
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

目前我们能看到的一个问题是**背景尺寸**样式。它可能与您的视频大小不同，因此您应该做一些调整。

接下来照常使用**html 2 canvas**

```
html2canvas(document.body)
    .then((canvas) => {
        // display, save as jpeg
     }) 
```

Enter fullscreen mode Exit fullscreen mode

使用最新版本[v 1 . 0 . 0-α12](https://github.com/niklasvh/html2canvas/releases)进行测试

下面举个工作例子
[![Edit n9wrv84jlj](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/n9wrv84jlj)

### 为什么我需要对我的 web 应用进行截图？

原因可能有很多，但我首先想到的是**侵犯用户隐私！**。但是当我在一家**数字标牌**公司的项目中工作时，我了解到截图是一个强制性的功能。用户希望随时看到他们的远程显示器上正在(或曾经)显示什么。

就这样吧，我希望网络空间里有人会觉得这个有用
(我的第一篇帖子，yaaaai)