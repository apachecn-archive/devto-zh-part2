# 为 HomeKit 网络摄像头复活树莓 Pi

> 原文：<https://dev.to/samjarman/resurrecting-a-raspberry-pi-for-homekit-webcam-10j6>

你好，

最近，我在新西兰惠灵顿参加了一个非常棒的惠灵顿家庭自动化黑客会议。受这些谈话的启发，我决定翻出我的树莓皮，做点什么！

所以，忽略了我的其他副业，我投入了进去。2014 年以来第一次插上自己的 Pi，担心最坏的情况。令我惊喜的是，它启动得很好，甚至可以相对轻松地连接到 wifi。我运行了`apt-get update`和`apt-get upgrade`(一个获取最新版本的东西，另一个实际安装它们)。我[在推特上发布了这件事](https://twitter.com/samjarman/status/1035081742200188928)，然后离开去做家务。

我回到 twitter，回复说我可能应该重新刷新一下。经过一天左右的思考，我想是的，不妨。

所以，我去了 raspberry pi 网站，下载了最新的 raspbian。我想，哦，我需要 GUI(主要是想看看 4 年来的进展-剧透-太棒了)。然而，一旦下载并尝试按照这些说明用 Etcher 作为[进行刷新。然而，由于我的 SD 卡只有 4GB，我不得不选择精简版的操作系统，所以我选择了它。](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)

从那里，我可以通过按顺序运行以下命令从 Raspbian Lite(全新安装)升级到带有 4GB 卡的 PIXEL GUI 的 Raspbian:

```
sudo apt-get updatesudo apt-get install --no-install-recommends xserver-xorgsudo apt-get install --no-install-recommends xinitsudo apt-get install raspberrypi-ui-mods #PIXEL GUIsudo apt-get install lightdm 
```

Enter fullscreen mode Exit fullscreen mode

这需要一些时间，一旦重启完成，我们就有了一个 GUI。从那里开始，是时候建立 Homebridge 了。

至少可以说，这里有一些问题，所以我遵循了这个指南。我最终为我的 PI*运行了这些命令。10.9.0 是当时最新的。* 

```
wget https://nodejs.org/dist/v10.9.0/node-v10.9.0-linux-armv6l.tar.xztar xJvf node-v10.9.0-linux-armv6l.tar.xzsudo mkdir -p /opt/nodesudo mv node-v10.9.0-linux-armv6l/\* /opt/node/sudo update-alternatives --install "/usr/bin/node" "node" "/opt/node/bin/node" 1sudo update-alternatives --install "/usr/bin/npm" "npm" "/opt/node/bin/npm" 1 
```

Enter fullscreen mode Exit fullscreen mode

然后我安装了 Avahi 和其他依赖项，这是 HAP-NodeJS 库中的 mdns 包所需要的。(显然，我不知道，只是在看指南...*)

```
sudo apt-get install libavahi-compat-libdnssd-dev 
```

Enter fullscreen mode Exit fullscreen mode

从那里，我可以安装 [HomeBridge](https://github.com/nfarina/homebridge) 。我跑了

```
sudo npm install -g --unsafe-perm homebridge 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，我发现 PI 上的文本编辑很棘手(如果你不知道 Vi，请举手...).所以我在 Mac 上为它创建了 config.json 文件，[打开 SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/) (首先更改默认密码)，并用 SCP 复制它，这是 linux 中最简单的命令。(讽刺)。

然后，我按照这些指令通过 ffmpeg 添加网络摄像头。

它需要重新启动，并尝试了几次与我手机上的 Home.app 配对，但最终，它只是工作。我可以看到自己 720 便士的饲料。这两天是值得的。反正这里的镜子也很烂。

这种修补的下一步是将 Homebridge 添加到系统服务中，以便它在启动后自动启动，我也在研究 homebridge 兼容的墙壁插座，因为我这里有一个 Lava 灯，我认为可以通过我的手机更好地控制。

直到下次...

萨姆（男子名）

是的，我只是盲目地按照网上的安装说明进行安装。我相当漫不经心，我不推荐你的日常驾驶电脑使用这种方法。但这是一个 Pi，如果我弄坏了什么东西，我可以闪一下，然后重新开始。