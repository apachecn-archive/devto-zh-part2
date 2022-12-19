# 在 iOS 上的 HTML5 视频播放器中在线播放视频

> 原文：<https://dev.to/kvharish/play-videos-inline-in-html5-video-player-on-ios-2a18>

给个背景。我们使用的是旧版本的 VideoJS(5 . 4 . 4)，它不会阻止视频在原生 iOS 视频播放器中播放，如下图所示。所以我们对播放视频没有任何控制权。

[![iOS video player](../Images/df48051c892cb0cdb5b3dfa0a87f359f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LxJg7O02--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://cdn.iphonehacks.com/wp-content/uploads/2017/06/iOS-10-vs-iOS-11-Video-player.jpeg)

图片提供-[http://www.iphonehacks.com](http://www.iphonehacks.com)

随着 iOS 12 的发布，我们在科尔多瓦开发的移动应用程序在用户每次退出全屏时都会崩溃。

我们分析了这个问题，并找到了一个解决方案，强制视频在应用程序中内联播放，而不是让 iOS 控制视频。

将 *playsinline* 或 *webkit-playsinline* 添加到你的 HTML5 视频标签中。转到 Cordova config.xml 文件，为 iOS 平台添加以下首选项。

```
<preference name="AllowInlineMediaPlayback" value="true"/> 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们构建我们的应用程序时，我们成功地阻止了视频在原生播放器中播放，但我们可以看到视频播放器上的原生控件，单击它时，用户仍然可以切换到 iOS 原生视频播放器。我们尝试添加不同论坛和 stackoverflow 上建议的所有可能的 css 样式，但这些都没有帮助。我们最终不得不升级到最新的 VideoJS 版本，其中隐藏了原生控件。

希望这篇文章有帮助。