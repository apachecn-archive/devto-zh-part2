# 博文:听觉反馈

> 原文：<https://dev.to/jonasbn/audible-feedback-4adg>

很久以前，我看了一个电视节目，它发生在一个韩国造船厂。我不记得它是否是这些巨型结构方案中的一个或类似的方案，但我确实记得当船厂里的巨大起重机移动时，它们播放的是音乐而不是警报，就像卡车逆向行驶或类似的情况。我想了很多这背后的基本原理，并得出结论，这是很重要的，创造一个独特的声音，将站在我们从所有其他的噪音在造船厂。

今天我在一个有 3 个屏幕的工作站工作，我有太多不同的窗口、编辑器、终端、数据库界面、电子邮件客户端等。

我使用带噪音消除功能的耳机，因为我坐在一个开放的办公室空间，我想保护我的注意力范围。我听音乐，主要是电子乐，没有歌词，因为我发现如果我听有歌词的曲子，我更容易分心(*如果有人能解释这种现象，我会洗耳恭听*)。一些[推荐](https://medium.com/@jonasbn/there-are-some-nice-streams-on-soma-fm-40d36d631324)对于[编码的背景音乐](https://medium.com/level-up-web/youtube-background-music-for-coding-99b592a74dc8)除了:[musicforprograming.net](http://musicforprogramming.net/)，我经常和 Spotify 上一些很酷的播放列表一起听。

编码时，我的眼睛被源代码、脚本输出、编译器/解释器/工具链反馈所占据。

同时一个有长有短的进程，在前台或后台运行。这些通常是工作，例如:

*   试运转
*   **Docker** 构建
*   测试数据库构建

所以当我发现[@ Tara-Gibbs](https://gist.github.com/tara-gibbs)`hey`utility([Gist:嘿](https://gist.github.com/tara-gibbs/cf91bc86c580f244de0ae9f5978edaac))时，我很激动，我把它用上了，过了一段时间，我决定扩展我对声音反馈的使用。

```
#!/bin/bash # 

REF: https://gist.github.com/tara-gibbs/cf91bc86c580f244de0ae9f5978edaac 

/usr/bin/osascript -e "display notification \"$\*\" with title \"Hey!\"" 

/usr/bin/say -v Samantha "Hey! $\*" 

# Setup: Add this to your /usr/local/bin in a file called hey. chmod +x it to make it an executable. 
# Usage: sleep 2 && hey wake up! 
```

Enter fullscreen mode Exit fullscreen mode

这是我的本地版本，包括参考塔拉的原始要点。如果你使用/喜欢泰拉的要旨，请*星*。

基于 Tara 的脚本，我决定为 **Docker** build 写一个特殊的脚本，命名为:`docker-build`(要点: [docker-build](https://gist.github.com/jonasbn/030008b965bebad2bb896aeb5518e07b) )，有点类似于`docker-compose`。

> 免责声明，这些脚本不是为发行版而实现的，需要调整后才能在您的机器上运行，它们是在 OSX 上实现的。然而，应该有可能在其他操作系统上挖掘出类似的实用程序，用于发出警报、语音输出和播放声音文件，使脚本在其他平台上工作。

```
#!/bin/bash

say -v Samantha "Docker build commencing...";

# Receives: messages and return value from Docker build
function emit() {

    if [ $2 -eq 0 ]
    then
        # green output
        echo -e "\033[92m$1";
    else
        # red output
        echo -e "\033[91m$1";
    fi

    # Resetting colouring
    echo -e "\033[0m";

    hey $1;
}

./docker-build.sh

if [ $? -eq 0 ]
then
    /usr/bin/afplay $HOME/Sounds/success.wav
    emit "Docker build completed" 0
else
    /usr/bin/afplay $HOME/Sounds/warning.wav
    emit "Docker build failed" 1
    exit 1
fi

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

请注意，它使用了差异化的彩色输出和基于 **Docker** 构建成功的声音。我所有的 **Docker** 构建都有一个`docker-build.sh`和`docker-run.sh`(以及`docker-entrypoint.sh`)，以避免为不同的项目反复输入相同的值，并保持项目的某种统一性——您的 shell 历史可能不会追溯到您的工作日志。GitHub 上有一个使用这种方法的例子。

在永远迷人的互联网上，我发现了一些科幻电影中的声音循环。我发现“ [Nostromo 环境发动机噪音](https://youtu.be/_AmQbNItyko)”特别刺激，我注意到它在背景中有一些离散的哔哔声，像是有什么东西在指示脉冲或什么东西在运行，这让我想起了韩国造船厂的起重机，在那里我仍然无法挖掘出任何片段或例子。

无论如何，我决定看看我是否能在我长期运行的脚本中加入同样的*脉冲*。这在某种程度上解决了我没有捕捉到终止信号的问题，我可以听到脉冲不再发射。

原型由两个脚本组成(*请原谅我非常基础的 Shell 脚本知识和风格，这不是我主要编写的代码，所以改进建议总是受欢迎的*)。

`alive.sh`每 5 到 10 秒发出一次声音，永远运行*。* 

```
#!/bin/bash 

while [1]; do 
    /usr/bin/afplay $HOME/Sounds/alive.wav; 
    sleep 2; 
done

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

`fork.sh``alive-beep.sh`的 forks，做应该做的事情，当完成时，它终止(`kill` ) `alive-beep.sh`，并发出一个声音来表示它已经完成，至于 docker-build 脚本，这可以改为反映运行的结果，区分声音和颜色。

```
#!/bin/bash

alive-beep.sh & 

sleep 6; 

kill $! 

/usr/bin/afplay $HOME/Sounds/success.wav 

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将它应用于 docker-build，它可能看起来如下:

```
#!/bin/bash

say -v Samantha "Docker build commencing...";

# Receives: messages and return value from Docker build
function emit() {

    if [ $2 -eq 0 ]
    then
        # green output
        echo -e "\033[92m$1";
    else
        # red output
        echo -e "\033[91m$1";
    fi

    # Resetting colouring
    echo -e "\033[0m";

    hey $1;
}

alive-beep.sh &

./docker-build.sh

kill $!

if [ $? -eq 0 ]
then
    /usr/bin/afplay $HOME/Sounds/success.wav
    emit "Docker build completed" 0
else
    /usr/bin/afplay $HOME/Sounds/warning.wav
    emit "Docker build failed" 1
    exit 1
fi

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

(要点: [docker-build](https://gist.githubusercontent.com/jonasbn/030008b965bebad2bb896aeb5518e07b/raw/1937fae99fdc44e667a67a6989514e6a95c459dc/docker-build)

我还远没有完成听觉输出的实验，我需要尝试其他的声音，不同的音量和与我的更多脚本的一般集成。

如果这篇文章给了你灵感，让你想到了很酷的东西，请告诉我。如果你发现脚本有问题，请告诉我。*