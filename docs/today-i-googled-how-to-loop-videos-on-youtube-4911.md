# 今天我谷歌了一下:如何在 YouTube 上循环播放视频

> 原文：<https://dev.to/andy/today-i-googled-how-to-loop-videos-on-youtube-4911>

当音乐视频开始在 YouTube 上变得非常大时，许多网站会弹出来做一些你认为 YouTube 会为你做的事情:循环播放你正在播放的视频。当时，我也不知道 HTML 是什么，甚至无法想象自己会做出这样的应用。最近，我一直在想这会是一个不错的兼职项目，并认为这是一个挑战，因为人们为它制作了成熟的网站。

虽然副业不是我的强项，但我的懒惰会默认点击搜索词`youtubeonrepeat`的第一个结果。出于某种原因，我得到一条错误消息:“播放当前不可用。”

呃。嗯，我的耳虫案例(笼统的说法，对吧？)并没有消失，我仍然感到沮丧，需要为此做些什么。今天碰巧让甘比诺最新的音乐视频卡在脑子里:
[https://www.youtube.com/embed/F1B9Fk_SgI0](https://www.youtube.com/embed/F1B9Fk_SgI0)

根据我有限的使用 HTML `<audio>`标签的经验，我认为可能有一种方法可以在我的浏览器控制台中自动播放它。对于`<audio>`元素有一个`play()`和`pause()`函数，所以我认为对于`<video>` HTML 元素也是一样的。我上了 YouTube 页面，检查了视频的元素，并将其缩小到这个元素:

```
<video 
  style="width: 853px; height: 480px; left: 280.833px; top: 0px;"
  tabindex="-1" 
  class="video-stream html5-main-video" 
  controlslist="nodownload"
  src="blob:https://www.youtube.com/cbd57a70-6691-bc44-bb4d-3e53926e5df1">
</video> 
```

Enter fullscreen mode Exit fullscreen mode

嗯，有趣的属性，尤其是`controlslist="nodownload"`！但是我跑题了。遗憾的是没有`id`，但是`class`已经足够好了。用一点 JavaScript，我抓取了视频元素:

```
document.getElementsByClassName('video-stream html5-main-video')
// returns an array of the elements with that class. In this case, there was only one. 
```

Enter fullscreen mode Exit fullscreen mode

太好了，现在我们有进展了。在这里，我试着通过 JS:
播放和暂停视频

```
const video = document.getElementsByClassName('video-stream html5-main-video')[0]
//=> returns the HTML video tag from above
video.play
//=> function play()
// The function exists!
video.play()
// The video started playing!
video.pause()
// The video paused! 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。这是一个好的开始。现在，如果我能算出视频有多长，然后可能有一个 setTimeout 函数自动为我播放它，我可能需要一个 if 语句来检查它是否与结束时间匹配...

等等。等等等等。一定有更简单的方法。我是程序员！一定有更简单的方法。于是，我做了一些搜索，寻找`html video element`。我的第二个结果是我需要的:Mozilla 的开发者文档。从那以后，我的直觉告诉我，我完全不需要阅读任何东西，只需要使用浏览器`find`和`cmd + f`就可以了。

零个结果，“找不到短语”。呃。谢谢你，浏览器。我已经向下滚动并真正阅读了。高效的解决方案到此为止。嗯，我滚动到我需要的，在“属性”部分，它是:

> loop:布尔属性；如果指定，浏览器将在到达视频结尾时自动搜索回起点。

哇，一个布尔！比我想象的还要容易。我回到我的 YouTube 页面，在控制台中，我检查并设置视频的`loop`属性:

```
video.loop
//=> false
video.loop = true
//=> true
video.play()
// might as well not click while we're here, right? 
```

Enter fullscreen mode Exit fullscreen mode

当然，我必须看看它是否真的会循环。我点击到视频的结尾，在座位边上等着(实际上，但主要是因为我养成了奇怪的坐姿习惯)，看看它是否会正确地~~重复~~循环。如果你想知道为什么我没有直接点击到最后，这可能是因为一些不理智的原因。不管怎样，时间在流逝。还剩 3 秒...还剩 2 秒...还剩 1 秒...0.视频循环播放！！！

就像那些人一样，我找到了一个在 YouTube 上循环播放视频的方法！我对它的简单明了感到高兴，也很高兴我不必使用一个充满广告的网站。也就是说，我可能会开发一个浏览器扩展，只需点击一个按钮就可以做到这一点，因为我经常一只手放在鼠标上，另一只手放在食物上。可能不会。不管怎样，我学会了一个巧妙的小技巧，只需要一点谷歌搜索和直觉。感谢阅读！