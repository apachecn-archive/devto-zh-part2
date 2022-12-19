# 我用数码钢琴和 JavaScript 做了一个临时房间

> 原文：<https://dev.to/peteranglea/i-made-a-breakout-room-with-a-digital-piano-and-javascript-53m5>

几个月前，我的妻子和我决定在我们的房子里创造我们自己的“会议室”体验来招待朋友和家人。

我决定巧妙地使用 JavaScript，创建一系列的锁，要求演奏者在数码钢琴上正确演奏特定的音符。钢琴通过 MIDI 电缆连接到电脑上。在电脑上，Chrome 浏览器窗口是打开的，并监听 Web MIDI API 的输入。

[![Willy Wonka demonstrating a piano lock](../Images/ccda5fdf53c024c87fdd4f445547bfd1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jJoSe6Jy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3k76yqazdfseuwfmhcx8.gif)

我写了一整篇文章，讲述我在这个过程中学到的一切。[立即在 Smashing Magazine](https://www.smashingmagazine.com/2018/03/web-midi-api/) 上阅读。

还有，我在 Smashing 杂志上发表了文章！！啊！

这里有一个视频演示游戏序列从粉碎的文章。Chrome 窗口下方显示的是一个虚拟 MIDI 键盘，有助于可视化当前正在播放的音符。对于正常的分支房间场景，这可以在全屏模式下运行，并且房间中没有其他输入(如鼠标或计算机键盘)来阻止用户关闭窗口。

[https://www.youtube.com/embed/cGXqjWvG_6k](https://www.youtube.com/embed/cGXqjWvG_6k)