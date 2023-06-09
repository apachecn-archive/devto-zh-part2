# Youtube-dl-gui。批量下载 youtube 视频的明智方式

> 原文：<https://dev.to/aswathm78/youtube-dl-gui-a-sane-way-to-download-youtube-videos-in-bulk-3cja>

# 如何批量下载 YouTube 视频？

这是谷歌问得最多的问题之一。

但答案包括许多捷径和付费软件，如 4k 视频下载

现在我正在使用一个软件，它是开源的，可以免费下载，没有任何限制。

[Youtube-dl](https://rg3.github.io/youtube-dl/)

## 历史

它最初是一个下载 youtube 视频的命令行工具，现在它支持许多网站。

注意:这里的[是指向受支持网站的链接](https://rg3.github.io/youtube-dl/supportedsites.html)

过了一段时间，GUI(图形用户界面)也被制作成易于使用的
[![gui-for-Youtube-downloader](img/bccd0cbec9138406fbef0a85014a6261.png)](https://camo.githubusercontent.com/4721687954e26a917f324cd4283527702f89ed50/687474703a2f2f692e696d6775722e636f6d2f49346f585057732e706e67)

这个博客是一个简单的教程，告诉你如何不用谷歌搜索就能使用它

首先，

## 为您的方便配置软件

下载整个播放列表时，最好在软件中设置**自动递增**选项。

playlist-index 将相应地为视频添加索引

```
 %(playlist_index)s-%(title)s.%(ext)s 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/f38127512a27405602c4625fcb10ab06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WWQgPR11--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0t6845kscwv13bonmehs.png)

### 添加合适的格式

下载视频时，您需要正确的分辨率类型和视频类型，可以从主屏幕中选择，如
[![](img/41f6e8bb2fcded7179a6646c86ef820d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j1XcGGMV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ad9zwxbtdzxaa5fi55e0.png)

但是也可以自定义这个，使用*选项*中的*格式*标签

[![](img/4eac16dd8f0fb0eec367560fcb5ec240.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eH0X7sDj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lc1cy2oswsb9uvu7ptkg.png)

### 字幕

字幕是观看视频时的重要功能之一，因此不同的人群可以从视频内容中受益，即使他们不理解内容创作者的语言

你可以下载你选择的字幕语言，如果你需要你也可以嵌入它(只在 mp4 视频上)！！！

[![](img/da11ee9cf8d1873bbb5f5692dac7c667.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zTWrs2US--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/adhqaswyl6sw7yu2p84p.png)

### 高级

您可以在高级选项卡
[![](img/1ee2ad43dd31348c581466dc7ea8722f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A_j7Y7P4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z7dim1kkuvr9fsffkarx.png) 中添加认证和代理等高级功能

### 杂项

有时网络会中断，软件没有恢复选项，

但是还有其他可用的东西

当你使用

`-c or --continue`

结合视频的索引

`---playlist-start NUMBER`

从您停止的地方继续

# 结论

**Youtube-dl-gui** 对我很有帮助。但是没有很棒的初学者帖子。

希望有帮助！！！！