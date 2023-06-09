# 在 Pharo Smalltalk 中启用声音

> 原文：<https://dev.to/sebnozzi/enabling-sound-in-pharo-4890>

在这篇文章中，我将向你展示如何在 [Pharo Smalltalk](https://pharo.org/) 中启用声音回放，这在默认情况下是不包含的。

* * *

Pharo-VM 能够播放声音，但是默认情况下，这样做的类不包含在映像中。你需要先加载" [PharoSound](http://catalog.pharo.org/catalog/project/PharoSound) "包。

有两种方法可以做到这一点:

1.  交互式地，通过“目录浏览器”或
2.  以编程方式，通过代码

让我们探索这两种方式。

## 互动地

对于交互式变体，通过在空白区域的任何地方点击来调出“世界”菜单。

然后，在“工具”下，选择“目录浏览器”。像这样:

[![Opening the "Catalog Browser"](img/c01c57168ffce3eb893c8bdaf1164edd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lnmAWoFd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/attuiqq0yhm7h3jo1u4o.png)

在目录浏览器中，在过滤器中输入“sound”并按 enter 键:

[![Searching for "sound" in the packages](img/e16dc86f5256b03917aa8a6dce0e7866.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RyK6Qv1n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r7q687hj8x6fsedridok.png)

右键单击“Pharo Sound ”,选择“安装稳定版本”,如下所示:

[![Installing stable package version](img/a977ed5a9cd0078639583ddf9bb1ecdc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1TUwI6Be--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zh5xnman5lssfgttk9wt.png)

因为它只在 Pharo 5 上测试过，系统会警告你它没有在你当前的 Pharo 版本上测试过。用“是”接受警告，因为它应该在最新版本中工作。

[![Accepting the warning](img/afc219ba77fb29b6fde70afc9beb9d1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AYz5KY0j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t1vzry73kfch3wcomsxj.png)

(免责声明:这是在 Pharo 6.1/OSX 下测试的)

## 程序化地

通过目录浏览器完成的同样工作也可以直接通过评估代码来实现。在游乐场窗口对此进行评估:

```
Metacello new
    smalltalkhubUser: 'Pharo' project: 'MetaRepoForPharo50';
    configuration: 'PharoSound';
    version: #stable;
    load. 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。Pharo 现在知道如何播放声音了。

## 确保它正常工作

为了确保它有效，你可以打开操场的窗户，尝试一些东西...

要播放基本声音，请执行以下操作:

```
SampledSound beep. 
```

Enter fullscreen mode Exit fullscreen mode

若要使用合成的单簧管声音演奏大和弦，请执行以下操作:

```
clarinet := FMSound clarinet.
(AbstractSound majorChordOn: clarinet from: #c4) play. 
```

Enter fullscreen mode Exit fullscreen mode

要打开一架迷你虚拟钢琴，你可以这样评估:

```
PianoKeyboardMorph new openInWorld. 
```

Enter fullscreen mode Exit fullscreen mode

你可以从 Wave 文件(或 AIFF)中载入声音，然后像这样播放:

```
sampledSound := (SampledSound fromWaveFileNamed: '/path/to/file.wav').
sampledSound play. 
```

Enter fullscreen mode Exit fullscreen mode

如何学习用声音做什么？

探索“AbstractSound”类及其子类。从课堂开始。在那里你会发现许多有趣的东西。闲聊最好通过探索来学习。

最后，我留给你们一首巴赫的赋格曲:

```
AbstractSound stereoBachFugue play 
```

Enter fullscreen mode Exit fullscreen mode

祝 Smalltalk 玩得开心！