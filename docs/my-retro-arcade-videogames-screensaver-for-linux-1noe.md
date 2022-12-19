# 我的复古街机游戏屏幕保护程序

> 原文：<https://dev.to/cosimo/my-retro-arcade-videogames-screensaver-for-linux-1noe>

屏保很神奇，它们就像磁铁一样，至少对我来说是这样。我一直对它们很着迷。制作一个需要极大的创造力和编程能量。

我想我遇到的第一个屏幕保护程序可能是为 Windows 3.1 编写的，尽管 Commodore 64 的加载彩条可能也有资格成为屏幕保护程序。

我记得最棒的屏保之一是 Novell Netware 服务器自带的屏保。这是在 1997 年左右，看起来大概像这个视频:

[https://www.youtube.com/embed/RitHgCBNrNs](https://www.youtube.com/embed/RitHgCBNrNs)

这是一件艺术品。它是一个文本模式的屏幕保护程序，显示了几条“蛇”在屏幕上游走。每条蛇代表一个处理器。多核处理器在当时非常流行，但是我们使用一台 SMP 机器和两个 Pentium Pro 处理器运行 Netware 服务器，这在当时是非常棒的。

如果处理器占用率高，蛇会移动得更快，同时，IIRC 每个处理器上活动的进程数量越多，蛇就越长。简单而精彩。只要简单地看一下屏幕，您就可以立即知道系统加载了多少，这一点非常重要，因为 Netware 机器使数百名员工能够完成他们的日常工作。

我使用 Linux 作为我的工作站操作系统至少有十五年了，jwz 的 xscreensaver 包一直存在并且非常受欢迎，以至于有一天我开始了自己的屏保项目。很明显，我决定重写 Netware 的屏幕保护程序，我在过去的中写过这个[。](http://www.streppone.it/cosimo/blog/2011/03/loadsnake-aka-the-novell-netware-snake-screensaver-clone/)

所有这一切都是为了说明屏保对我来说是一件事:-)。

快进到现在，我想我找到了下一个层次的屏保的好处，我很满意。这是一个复古街机电子游戏的屏幕保护程序。当我换了工作，开始使用新的 Linux 工作站时，这股热潮就开始了。我注意到我所有运行 MacOS 的同事都在运行这个[相当惊人的空中视频](https://github.com/JohnCoates/Aerial)片段作为屏保。

我很快发现在 Linux 上也可以运行同样的程序，并且有一个名为 [xscreensaver-aerial](https://github.com/graysky2/xscreensaver-aerial/blob/master/atv4.sh) 的项目，你可以将其作为 xscreensaver 的一部分来运行。正当那辆

很酷的时候，它很快变得无聊起来。然而，xscreensaver-aerial 的想法很简洁。你可以全屏显示你自己的视频文件，只要用视频文件填满一个文件夹，当 xscreensaver 被调用时，其中一个视频就会被播放。上面链接的 shell 脚本在 **mpv** 的帮助下做到了这一点，这是一个不错的 Linux 视频播放器，尽管有点复杂。

接下来的步骤是显而易见的:使用 **mpv** 并修改 shell 脚本来做我想要的事情。从文件夹中随机挑选视频文件。我开始下载我最喜欢的视频游戏的 longplay 视频。比如，[的炸弹杰克](https://www.youtube.com/watch?v=XvHk6LTh-Ks)或者太空王牌，风靡 80 年代的街机或者阿米加游戏，对于
T5 来说，那都是不错的怀旧因素。

[https://www.youtube.com/embed/4iplXu8jXc0](https://www.youtube.com/embed/4iplXu8jXc0)

开始用这个版本有一段时间了。我运行一个双显示器设置，一个在纵向模式下用于代码和文本编辑，另一个在横向模式下用于浏览器和其他需要。

有时，屏幕保护程序会在两个监视器上播放相同的视频(蹩脚)，或者以不适合监视器的纵横比播放视频，使屏幕布满垂直或水平的黑条。

因此，最后的改进是自动检测每个显示器的长宽比，并从 **`landscape`** 或 **`portrait`** 视频文件夹中选择。

我使用的技巧就是一些 shell 代码:

```
#
# Overwrites $MODE with either `landscape` or `portrait`
#
check_monitor_mode() {
  MODE="landscape"
  local tmpfile="$(mktemp)"
  logger -t xscreensaver "xwinid: $XSCREENSAVER_WINDOW" xwininfo -id "${XSCREENSAVER_WINDOW}" > "${tmpfile}"
  local width=$(grep "Width: " "${tmpfile}" | awk '{ print $2 }')
  local height=$(grep "Height: " "${tmpfile}" | awk '{ print $2 }')
  unlink "${tmpfile}"
  if [$width -gt $height]; then
    logger -t xscreensaver "landscape mode (${width}x${height})"
  else
    logger -t xscreensaver "portrait mode (${width}x${height})"
    MODE="portrait"
  fi
} 
```

播放视频的命令必须保存为 xscreensaver 模块，命令为:

```
mpv --really-quiet --no-audio --fs --no-stop-screensaver --wid="$XSCREENSAVER\_WINDOW" "${themovie}" & 
```

通过自定义~/。可以添加你自己的可执行程序，给它们起个名字，等等，比如 my ~/。xscreensaver 包含以下内容:

```
...
programs:
  - "Apple TV Aerial" atv4
  - retro-arcade-screensaver
  - "Novell Netware Screensaver" loadsnake
... 
```

下面是最终结果:

[![](../Images/f8cec5bc04dbbe2dd700aaf185da8727.png)T2】](http://www.streppone.it/cosimo/blog/wp-content/uploads/2018/07/20180627_202625.jpg)