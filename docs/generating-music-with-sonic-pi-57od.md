# 用 Sonic Pi 生成音乐

> 原文：<https://dev.to/jessicagarson/generating-music-with-sonic-pi-57od>

*[本帖于 2018 年 5 月 17 日首次发布在 Twilio 博客上](https://www.twilio.com/blog/2018/05/generating-music-with-sonic-pi-and-ruby.html)*

[![sonic-pi](img/f4bc6bea4872fea102b666d7c823b64c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4cRl24K9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/05/jBYyTufwkMUFyBD_Ak6pRbLjb8tirVezDiXA0Oa6zS-g0LKMj6caqRLVlEkr4smnPSuq_CwtGhahVUr6B33fyyhIwrOS4v8OL5jAqe96Sbt5GI9sUbCc0I2BlCPInqE2c3fJ6x7O.png)

Sonic Pi 是一个用 Ruby 写音乐的集成开发环境(IDE)。我使用 Sonic Pi IDE 用 Ruby 写代码，并以艺术家的名字 Messica Arson 用 [Audacity](https://www.audacityteam.org/) 对着它尖叫。我创作的声音很大程度上受到噪音音乐的奇怪声音的启发，结合我在朋克乐队领唱时完善的尖叫声。

在本教程中，我们将一起演练如何创作这首歌曲。

## 什么是音速 Pi？

有了 Sonic Pi，我们可以像使用合成器一样与计算机进行交互。Sonic Pi 是超级对撞机的 Ruby 包装器。载入了样本和合成器，您可以通过无数种方式与之互动。您也可以像我们在本教程中所做的那样添加自己的示例。

[下载索尼克 Pi](https://sonic-pi.net/) ，然后继续这篇文章的其余部分。如果您有旧版本的 Sonic Pi，请务必更新到最新版本(3.1 版)。要更新，进入`help`菜单，然后点击显示`updates`的地方，点击`check now`查看是否有最近的更新。

## 循环是音速π的核心

由于电子音乐是建立在重复循环的基础上的，所以理解循环是如何产生的是有效使用 Sonic Pi 的一个关键概念。实时循环允许您随时进行更改。

Sonic Pi 中循环的基本语法如下:

```
live_loop :name_your_loop do
  synth :name_of_synth
  sleep 0.25
end 
```

创建循环的第一步是命名循环。从那里，您可以调用 synth 或您正在处理的样本。睡眠可以让你调整循环的时间。对于睡眠，数字越小声音越快，数字越大声音越慢。需要注意的是，如果你不包括睡眠，这个循环就不起作用。

## 让我们一起开始写歌吧

现在我们知道了 Sonic Pi 中循环的基本语法，我们可以在 IDE 的`Buffer 0`中编写我们的第一个循环。在 Sonic Pi 中，缓冲区是你写代码的地方。您可以在一个缓冲区中编写代码，然后移动到另一个缓冲区来编写更多代码，同时您可以不停地创作歌曲。一些开发人员选择在多个缓冲区中编写代码，以保持事情有条理。有 10 个缓冲器从 0 开始计数。今天我们将在一个缓冲区中编写代码。

让我们创建一个名为 start 的循环，它使用了`Buffer 0`中的`tri` synth，这是 IDE 中的第一个缓冲区:

```
live_loop :start do
  synth :tri
  sleep 0.25
end 
```

要运行循环，按下`Run`按钮或`alt/cmd R`。要停止声音，按下`alt/cmd S`或停止按钮。tri synth 创造了一种美妙悦耳的旋律，听起来就像[这首](https://soundcloud.com/jessica-garson/tri)。Sonic Pi 的完整快捷键列表可以在[这里](https://github.com/samaaron/sonic-pi/blob/master/etc/doc/tutorial/B.02-Shortcut-Cheatsheet.md)找到。

## 给我们的歌添加一个样本

我们现在可以在名为`start`的循环中添加一个样本。让我们使用我最喜欢的内置样本之一，名为`loop_safari`。编辑您的循环，如下所示:

```
live_loop :start do
  synth :tri
  sample :loop_safari
  sleep 0.25
end 
```

声音现在应该有更多的打击元素，听起来像这样
。

## 添加第二个带低音的循环

到目前为止，我们一直在一个循环中工作，但如果我们添加一个不同的循环，我们可以调整时间。由循环休眠表示的定时，以便它以较慢或较快的速度播放。为了给我们的歌曲创造一个更完整的声音，我们将让我们的低音比第一个循环更慢。

就在你的`start`循环下面，让我们添加第二个循环:

```
live_loop :second do
  sample :bass_thick_c
  sleep 0.5
end 
```

正如你所听到的，低音让我们创造的声音看起来更加饱满。

## 添加效果

我们可以添加失真等效果。失真会增加声音增益的幅度，从而产生模糊效果。我们将加入另一段带有模糊铃声的旋律。让我们在名为 second 的循环下添加第三个使用失真效果的循环:

```
live_loop :distort_everything do
  with_fx :distortion do
    synth :pretty_bell
    sleep 0.75
  end
end 
```

这是我们现在应该听到的。

## 使用笔记

我们也可以通过在`distort_everything`循环下添加第四个循环来弹奏音符环。让我们添加这个循环:

```
live_loop :notes do
  use_synth :pluck
  n = (ring, :d3, :d1, :d2)
  play n
  sleep 0.15
end 
```

我们刚刚添加了一个合成器，听起来像用音符 d3、d1 和 d2 拨动吉他弦。添加了这个循环后，我们的歌曲现在听起来像 [this](https://soundcloud.com/jessica-garson/notes) 。

## 添加音频样本

在教程的这一点上，[你可以下载我使用的文件](https://www.dropbox.com/s/n1gm0zjm3999cks/no.wav?dl=0)或者你可以使用你选择的另一个音频文件。你也可以自己试一试。为了给自己取样，我用 usb 线连接的麦克风录下了自己在 [Audacity](https://www.audacityteam.org/) 中的尖叫。我可以使用我在录制过程中创建的文件的路径来创建这样一个循环:

```
live_loop :my_own_voice do
  sample "/path/to/soundfile.wav"
  sleep 9
end 
```

确保用您自己在 Audacity 中创建的音频文件或下载的文件的文件路径替换`path/to/soundfile.wav`。

这个循环放在我代码的底部，在`notes1`循环的下面。

## 全码

歌曲的完整代码如下:

```
live_loop :start do
  synth :tri
  sample :loop_safari
  sleep 0.25
end

live_loop :second do
  sample :bass_thick_c
  sleep 0.5
end

live_loop :distort_everything do
  with_fx :distortion do
    synth :pretty_bell
    sleep 0.75
  end
end

live_loop :notes do
  use_synth :pluck
  n = (ring, :d3, :d1, :d2)
  play n
  sleep 0.15
end

live_loop :my_own_voice do
  sample "/path/to/soundfile.wav"
  sleep 9
end 
```

## 我最喜欢的索尼克 Pi 的功能

当我开始使用 Sonic Pi 时，我写了 0 行 Ruby 代码。这是我最喜欢的学习新编程语言的方式，因为我可以听到我的代码发出很酷的声音。如果你稍微修改一下代码，看看它对声音的影响会很有趣。即使您已经在播放音乐，也可以通过按`Run`或`alt/cmd R`实时更新声音。

[![in IDE documentation](img/db4d9236f10abddb35ee8855c11d4c00.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t3F4Epxz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2018/05/dKxXnEtmWVGci_zaIj6N_W-TrwMLa5psaQtHFhKeU1x6CwQobheCwJHbe97HxXqcr8sCX-GDoiTvV8Tu-uZdYeuCHCjG7p7sP2QVweKWWgLdgj1sTJbJBmp3gZP8oXKRarW51NAI.png)

Sonic Pi 最酷的一点是文档内置在 IDE 中。当我第一次开始使用 Sonic Pi 时，我采用了文档中的例子，并对它们进行了修改，直到它们变成了我自己的声音。当你刚开始或遇到困难时，这个特性也会派上用场。

现在你已经知道了我在 Sonic Pi 创作歌曲的过程，希望这能给你入门的灵感。您可以通过按下`Rec`按钮并保存`.wav`文件来录制您创作的歌曲。请务必在 [@JessicaGarson](https://twitter.com/jessicagarson) 发微博给我，让我知道你用音速小子发出了什么样的酷声音。