# 受管理的 midi 更新

> 原文：<https://dev.to/atsushieno/managed-midi-updates-5e01>

早在一月份，我写了一篇关于[管理的 midi](https://github.com/atsushieno/managed-midi) 的介绍[博文](https://dev.to/atsushieno/managed-midi-the-truly-cross-platform-net-midi-api-56hk)。从那时起已经 4 个月了，有几个更新，所以很高兴总结一下。

### 支持 MIDI 输入

我现在有几个 MIDI 输入设备，尤其是 Seaboard Block 很酷(虽然它对我的实际使用来说很没用:p)。

也许你认为我只是在炫耀我的酷玩具，但事实远不止如此。Seaboard Block 基于 BLE MIDI，以前只在 iOS 上支持。Android 6.0 也支持 BLE MIDI 作为其 Android MIDI API 的一部分。但是 Linux 没有机会使用它，直到最近的 [Bluez 和 Linux 内核获得了对那个](https://blog.felipetonello.com/2017/01/13/midi-over-bluetooth-low-energy-on-linux-finally-accepted/)的支持。我用的是 Ubuntu 17.10，所以我也需要切换到[的一些补丁版本](https://bugs.launchpad.net/ubuntu/+source/bluez/+bug/1713017)。

无论如何，我有理由在托管 midi 中支持 MIDI 输入，而且已经完成了。我也有 M-AUDIO KeyStation Mini 32，但这两种设备给出的输入信息完全不同...Seaboard 带来的是[MPE](http://expressiveness.org/2015/04/24/midi-specifications-for-multidimensional-polyphonic-expression-mpe)——每次按键都会发送 pitchbend、PAf 等。根据规范，这是大量的信息(如加速度计多轴输入)。一旦我能想到应用程序并支持更好地处理那些 MPE 消息，那将会很有趣。

### 基于 netstandard2 的 NuGet 打包

我不太关心 nuget 打包，但是传播这个库的使用是有好处的(我也不关心...).如[上一篇](https://dev.to/atsushieno/managed-midi-the-truly-cross-platform-net-midi-api-56hk)中解释的，Xamarin。Mac Full 项目被阻止，它没有改变。可能很少有人在乎，但我在乎。Mac full 是我能让 xwt 工作的唯一配置文件，因此我的基于 Xwt 的 [xmdsp](https://github.com/atsushieno/xmdsp) 也能工作。

总之，Xamarin，这是我“打包”managed-midi 的主要原因，已经迁移到。NET 标准 2.0 世界从基于 PCL 的世界。所以我也向前看了。

应该有。这个库的 NET Core 版本(与 netstandard2.0 库不同，它将包含 ALSA 支持等。)，希望在不久的将来。

### 精确时间控制器

managed-midi 曾经是一个愚蠢、简单的 midi 播放器，从不处理延迟问题。这件事意义重大，不容忽视，不久前报道了这件事[。这也是我的一大担忧。](https://github.com/atsushieno/managed-midi/issues/14)

managed-midi 附带了一个名为 MidiTimeManager 的计时器类。通常我们使用默认定时器，它等待指定的时间间隔(简单如 Task。Delay())，我们也可以使用虚拟时间管理器。这就像 Rx.NET 的 TestScheduler。当运行单元测试时，你不想永远等待。

所以，从技术上来说，计时器可以做的不仅仅是简单和愚蠢的等待。任何 MIDI 库都会处理这个问题。直到最近，我才真正开始实现它。

虽然仍然不可能测试这种改进(除非我实际播放一些歌曲并在单元测试中测量实际播放时间...)，我用我的 xmdsp MIDI 播放器考察最新的精度。我曾经创作过非常复杂的歌曲文件(或者是拷贝或者是原创歌曲),我用它们来喂狗。

### 接下来是什么

我不知道 TBH。我的目标是转向基于原始音频的东西(如 android-fluidsynth)和一些 MIDI 作曲环境。但由于 managed-midi 是我所有现有应用的基础，它将继续下去。