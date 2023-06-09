# 带树莓派和谷歌助手的声控灯

> 原文：<https://dev.to/peiche/voice-activated-lights-with-raspberry-pi-and-google-assistant-379m>

我为我女儿的房间设计了一个基于树莓派的谷歌助手，这样她就可以用声音打开灯。但是在我开始之前，我想先介绍一下背景。

我女儿是残疾人。我们住在公寓里，这意味着我不能做出剧烈的改变，也意味着公寓里的大部分东西对她来说都不是真正可以接触到的。我们已经做了我们能做的，但是我们不能对线路做任何事情。因此，如果我不能移动电灯开关(这将是一个坏主意，因为我不是电工)，我想我会改变她如何打开她的灯。

这是我女儿演示它是如何工作的:

[https://www.youtube.com/embed/J99fonZpTM4](https://www.youtube.com/embed/J99fonZpTM4)

我不想只买一个谷歌主页。相反，我买了一个树莓派和一些其他设备:

*   树莓 Pi 3
*   扬声器
*   麦克风
*   电线+ LED 灯
*   智能插头(我在亚马逊上找到了 2 个装的)

按照这个[教程](https://medium.com/exploring-code/turn-your-raspberry-pi-into-homemade-google-home-9e29ad220075)，我可以让 Pi 作为一个助理客户端连接到我的谷歌账户。在很大程度上，这个教程非常有效。不幸的是，有一些棘手的问题。

在设置扬声器和麦克风时，我必须将这两行上的`rate`行从“16k”改为“16000 ”,以避免出现无效参数错误:

```
arecord --format=S16_LE --duration=5 --rate=16k --file-type=raw out.raw
aplay --format=S16_LE --rate=16k out.raw 
```

当我最初运行`main.py`脚本时，我得到了另一个错误:

```
TypeError: __init__() missing 1 required positional argument: 'device_model_id' 
```

这是因为自从这篇文章发表后，谷歌更新了助手 API。(Google gonna Google。)由于做出了这些改变，开发者现在必须注册像 Raspberry Pi 这样的定制设备。几个额外的步骤被证明是相当容易做到的(正如 Google [在他们的 Python 文档](https://developers.google.com/assistant/sdk/guides/library/python/)上记录的那样)，并且树莓 Pi 已经作为 Google 助手运行了。

按照教程的指示，我能够很好地连接 LED。当 Pi 监听时，它会亮起，当 Pi 不监听时，它会熄灭。

[![Raspberry Pi 3 with speakers and microphone](img/d611234ee1b2302934fbfa05a29268f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X1urMViF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dz8vue2h5li04d2y2gy0.jpg)

教程到此结束，我必须自己想出接下来的步骤。我有两包智能插头，事实证明，它们有一个配套的应用程序:智能生活。

我插上了 Namine 房间的两个智能插头。我把她的灯插在一个插座上，把她的月亮夜灯插在另一个插座上。(灯插在电灯开关控制的插座上，Namine 够不着。现在它插在一个永远在线的插座上。)

回想起来，我可能应该拍一些设置过程的截图。无论如何，应用程序要求我创建一个帐户，然后它扫描可用的设备。它检测到了我刚刚插入并打开的两个，所以它让我添加它们并给它们命名。

[![Screenshot of Smart Life app](img/95045274f9bfcdc49dea0c71e98e7b6a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dwhIiUZd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a4sdl1ce84ybdu29zzp7.png)

该应用程序还允许我定义房间，所以如果我愿意，我可以关闭房间中的所有智能设备。该应用程序还具有定时功能，可以在一天中的特定时间开灯或关灯，等等。

但这已经足够了。智能生活应用程序只是达到目的的一种手段；我从来没有打算长期通过这个应用程序控制公寓里的任何东西。真正的目标是将设备连接到谷歌助手，事实证明，这就像最初的设备检测一样简单。

我打开谷歌助手应用，导航到设置>家庭控制。我单击了右下角的蓝色“加号”按钮来创建一个新设备——在本例中，连接一个现有设备。

从头开始，没有相关的服务。我向下滚动，在列表中找到了智能生活，从而允许我从谷歌助手中访问我的两个智能插头。因为 Assistant 被配置为在我的手机和 Raspberry Pi 上使用我的谷歌账户，我可以从任何一台设备上控制它们。(这也包括我不在家时控制灯光。)

Smart Life 中配置的房间与 Google Assistant 中的房间没有关系，所以我用相同的名称创建了新房间。这两个插头的名字来自智能生活:“灯”和“月亮”

这就是事情变得棘手的地方，因为尽管这些数字助理(谷歌助理，Siri，Alexa，Cortana 和其他人)被吹捧为人工智能，但它们仍然没有真正的自然语言能力。它也——至少在助手的情况下——还不能被教会如何发新单词或名字的音。

我女儿的名字叫 Namine。发音是"不要我不要"(如果你能认出它来自哪里，你选择的霍格沃茨房子加十分。；)不过，Google Assistant 的发音像“提名者”，所以将灯命名为“Namine 的灯”会导致 Assistant 无法识别它。

有趣的是，如果我说“打开灯”，它确实会打开灯，但它会回应，“打开被提名者的灯。”因此，我不仅没有办法教 Assistant 如何读她的名字，而且它也不认识所有格名词。不管怎样，它总是回应称之为“昵称”(即使我把它命名为“我的灯”，它仍然以“打开我的灯”作为回应。)

结果，我不得不在名字上有所创新。经过反复试验，我最终给它起了一个语音昵称“naameenayz 房间里的灯”(它试了几次才把 Namine 的名字读对。)

[![Screenshot of Google Assistant devices](img/830e10bc2bde31ea837136513fc6fbd5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uonrYzun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uvcde2i136pry9zu21o5.png)

尽管如此，它仍然无法识别命令“打开 Namine 的灯”，即使 Assistant 识别的语音名称与我们发音完全一致。另一方面，更简单的命令“打开灯”仍然有效。然后它会回应“打开 Namine 房间的灯”，这是可以接受的。对结果满意——或者说目前足够满意——我用同样的方法安装了纳米恩的月亮夜灯，挂在她的天花板上。

* * *

*这篇文章的一个版本最初发表于[eichefam.net](https://eichefam.net/2018/09/12/voice-activated-lights/)。*