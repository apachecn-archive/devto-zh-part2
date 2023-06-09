# Raspbian:通过过扫描设置放大屏幕边界

> 原文：<https://dev.to/nabbisen/raspbian-enlarge-screen-boundaries-via-overscan-settings-2ef>

这是一个关于 [Raspbian](https://www.raspberrypi.org/) [桌面](https://www.raspberrypi.org/downloads/raspbian/)调整的小技巧。

### 一个问题

Raspbian 有时会在物理屏幕和逻辑桌面或控制台的边缘之间输出不吉利的黑色间隙，以防它通过 HDMI 连接到屏幕。

我想尽可能广泛地使用屏幕☹

### 前:不吉利的缺口

[![before](img/70e8a71dd2935f0ca8a16346998be7a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yu7Z0k1_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ll03p5drca40lmt1bfik.jpg)

是“过扫描”造成的。在 Raspbian 安装程序中，有时会默认设置过扫描选项。

根据[维基百科](https://en.wikipedia.org/wiki/Overscan)的说法，过扫描是:

> 过扫描是某些电视机中的一种行为，其中输入图像的一部分显示在屏幕的可视边界之外。它之所以存在，是因为从 20 世纪 30 年代到 21 世纪初，阴极射线管(CRT)电视机在视频图像在屏幕边界内的定位方面存在很大差异。然后，在图像周围有黑边的视频信号就成了普遍的做法，而电视就是要以这种方式丢弃这种黑边。

并且是通过编辑 **`/boot/config.txt`** :
固定的

```
disable_overscan=1 
```

Enter fullscreen mode Exit fullscreen mode

**注意:当这一行存在并被注释掉时，去掉注释“#”。不存在的时候，加上这行。*

**注:或者，也可以在同一个文件中自定义指定方向的设置:*

```
#overscan_left=10
#overscan_right=10
#overscan_top=15
#overscan_bottom=15 
```

Enter fullscreen mode Exit fullscreen mode

并重启:

```
$  sudo reboot 
```

Enter fullscreen mode Exit fullscreen mode

### 后:吉祥和谐

[![after](img/8e7da245b729a23cbb3f0ca340fda2af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TIyyfLwD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5sdszh03vgo3ugr5yi10.jpg)

是😃