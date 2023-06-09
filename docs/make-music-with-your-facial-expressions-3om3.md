# 用你的面部表情创造音乐

> 原文：<https://dev.to/yellzheard/make-music-with-your-facial-expressions-3om3>

我目前正在参加一个名为 [Recurse Center](https://www.recurse.com/) 的项目(你可以随意问我，它很棒，是为那些想成为更好的程序员的人准备的！)

他们鼓励配对，所以在过去的几周里，我和[ok](https://github.com/okayzed)配对，让[面对音乐](https://face-the-music.glitch.me)，这允许你用你的面部表情制作音乐，然后将其录制为 webm 文件！

[![Face the Music](img/fac376aaa0e35b5ffc3f90567c831fbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gPr0RpjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i2d2fg6ifrfjhlhxel1e.gif) 
它的用途:

*   [clmtrackr.js](https://github.com/auduno/clmtrackr) 用于人脸追踪。
*   [Tone.js](https://tonejs.github.io/) 为音乐。
*   [p5.js](https://p5js.org/examples) 和 p5.dom.js 用于画布上的绘制。
*   [teoria.js](https://github.com/saebekassebil/teoria) 乐理
*   [来自媒体流记录 API](https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder) 的 MediaRecorder，用于记录画布

它非常具有实验性，我认为它应该是项目中使用的大多数 javascript 库的最终候选。无论如何，你可以在 glitch 上重新混音:

[https://glitch.com/embed/#!/embed/face-the-music?path=index.html](https://glitch.com/embed/#!/embed/face-the-music?path=index.html)