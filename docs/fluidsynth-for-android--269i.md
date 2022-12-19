# Android 版 Fluidsynth

> 原文：<https://dev.to/atsushieno/fluidsynth-for-android--269i>

## 动机

我的一个不为人知的项目是 Android 的 Fluidsynth。从 Android 6.0 开始，它支持自己的 [MIDI API](https://developer.android.com/reference/android/media/midi/package-summary.html) 以及通过 BLE 的 MIDI 连接，因此将 MIDI 设备连接到 Android 成为可能(如果你想阅读我的日文文章，它是书[“Android Masters！”](https://booth.pm/ja/items/126263))。一个技术上看起来很酷的功能是它支持虚拟 MIDI 设备服务，因此任何人都可以实现一个 MIDI 设备服务，该服务可以提供 MIDI 输入设备或 MIDI 输出设备(或两者都有),其他应用程序可以作为客户端连接并播放它们，就像其他操作系统(Windows、Mac、Linux、iOS...).

它被设计成能够通过服务运行软件 MIDI 合成器，那么为什么不移植任何现有的位呢？我是这样认为的，并最终将 [Fluidsynth](http://www.fluidsynth.org/) 带入了 Android 领域。

## 构建系统

这并不简单；首先，没有声音输出是没有意义的。Fluidsynth 是一个软件合成器，支持各种音频 API，但不支持 Android。对于 Android，AudioTrack 和 [OpenSL ES](https://developer.android.com/ndk/guides/audio/opensl/index.html) 是可用的选择(我在实现的时候；既没有音频也没有双簧管)。Fluidsynth 在他们的 [`drivers`](https://github.com/FluidSynth/fluidsynth/tree/master/src/drivers) 源目录中有音频抽象层，所以我唯一要做的就是在那里再添加一个，对吗？

事情没那么简单。

首先，fluidsynth 需要用 Android NDK 工具链来构建。最初的 fluidsynth 不支持 Android 构建。这尤其是因为油嘴滑舌的依赖性；没有直观的方式为 Android 构建。glib 是基于自动工具的，NDK 没有很好地使用它。

...等等。有少数依赖 glib 的应用程序和库可以在 Android 上运行。还有 [Gimp](https://play.google.com/store/apps/details?id=org.dandroidmobile.xgimp) 吧？...但它运行在某个奇怪的 X 服务器上。接下来，GStreamer 怎么样？已知[支持安卓](https://gstreamer.freedesktop.org/modules/gst-android.html)。它是如何建造的？...我就是这样找到了[地狱犬](https://gstreamer.freedesktop.org/documentation/installing/building-from-source-using-cerbero.html)的建造系统。它以自动工具的方式为 Android 和其他目标构建一切，包括 glib。

所有这些依赖库都可以用“recipe”来构建，它定制每个库的构建。cerbero 的最终目的是建造 GStreamer，但是如果将配方添加到树中，它可以是任何东西。将 fluidsynth 添加到目录中非常容易。

我必须做一些修改来支持定制的 Android NDK 设置，所以我的 cerbero 树最终变成了与原始树不兼容的分支，但我最终可以为 Android 构建 libfluidsynth.so。

一切都变成了回购:[https://github.com/atsushieno/android-fluidsynth/](https://github.com/atsushieno/android-fluidsynth/)

## Android/OpenSLES 实现

其次，我必须添加 opensles 实现。源代码结构非常好，我可以很容易地将`fluid_opensles.c`添加到树中。一个小问题是几乎只有一个[参考样本](https://bitbucket.org/victorlazzarini/android-audiotest/src)作者维克多·拉扎里尼([他的博客](https://audioprograming.wordpress.com/category/android/)过去是公开的，但现在是私人的...).甚至日文版 NDK 书籍的样本也几乎与这些样本相同。

我必须实现的另一件事是支持 SoundFont 文件的定制流加载器。Fluidsynth 只提供简单使用本地文件 I/O 的基于文件名的加载器，所以我必须扩展 fluidsynth 本身以接受定制的 SF 加载器——抽象 API 和基于 Android 资产的实现。

因为我的应用程序是用 C#编写的，所以我将这些扩展添加到了我的 P/Invoke 包装器中(这只是我必须做的——如果我使用 Java，我还必须添加 JNI 入口点...).

## NFluidsynth 和 FluidsynthMidiService

Fluidsynth 是跨平台的，在 Linux 上运行良好，这让我更容易开发 C#绑定。Fluidsynth 本身作为一个虚拟的 MIDI 合成器工作，但是为了从系统的 MIDI 服务入口点桥接，我们必须使 fluidsynth 函数可调用并从这些入口点映射。

因此，我使用 P/Invoke 为 fluidsynth API 进行绑定，发布为[https://github.com/atsushieno/nfluidsynth](https://github.com/atsushieno/nfluidsynth)。并将其制成 Xamarin。安卓库几乎是零成本。我只需要关心专为 Android 构建的 Android 扩展。

最后，我在这个 NFluidsynth.Android 上创建了一个 Android MIDI 设备服务。要构建这样一个服务，我们必须实现 Android . media . MIDI . midideviceservice。整个 API 在输入和输出方向方面非常怪异，但实现起来并不困难。

在这次尝试中，我发现支持 Android MIDI API 几乎没有任何好处。我的 managed-midi API 是跨平台的，受 Web MIDI API 的启发，为它实现 Android 后端更有意义。因此，我提出了两种实现方式；一个用于 MidiDeviceService，另一个用于我的 API。

它们最终变成了回购:[https://github.com/atsushieno/fluidsynth-midi-service](https://github.com/atsushieno/fluidsynth-midi-service)

## CMake 交换机和 android-fluidsynth 端口

所有这些作品都是早些年完成的，所以我在 2018 年写这个帖子有点奇怪。我以为会有更多的软件 MIDI 合成器为 Android 发布，但它似乎没有发生。相反，我得到的是一些关于我的 android-fluidsynth 端口的询问。由于整个构建系统相当棘手，大多数尝试构建的人都失败了(对此我感到很遗憾...).

它没有被记录下来的原因之一是当前的状态(对我来说)是非常暂时的——当我开始这个项目时，它被托管在 SourceForge 上，并且是基于 autotools 的。现在它在 Github 托管[，构建系统转移到了 CMake。](https://github.com/Fluidsynth/fluidsynth/)

CMake 现在有问题——cerbero 在技术上支持 CMake，但它似乎从来不支持 Android。cerbero 所做的是指定自定义工具链(CC、LD 等。).当你覆盖一些由 cmake toolchains config 指定的工具链时(Android NDK 中有一个)...它将检测不一致的构建规范并重新启动构建，而不指定 cmake 选项(！idspnonenote)。).结果忽略了我的构建选项，构建失败了。我仍然没有找到解决这个问题的答案。

升级地狱犬是一个选项，这个选项有一些选择——地狱犬在我们的构建系统中，因为它解决了油嘴滑舌的依赖问题。为了消除 cerbero，我们必须找到解决油嘴滑舌建设问题的方法。

[volcano mobile/fluid synth-Android](https://github.com/VolcanoMobile/fluidsynth-android)是一个选项；它消除了所有油嘴滑舌的依赖。然而这棵树变得和原来的树不相容，我对此感到不舒服。然而，[有一个努力](https://github.com/degill/fluidsynth-android-opensles)将我的 OpenSLES 实现合并到那个不同的版本中。我在考虑将二进制构建工件的基础切换到这棵树上。

其他选择是 [Google cdep](https://github.com/google/cdep/) (我不喜欢他们的基本想法，即分叉源代码以进行自己的更改，这使得合并 origin 变得不必要的混乱)，或者合并 [mono eglib](https://github.com/mono/eglib) 而不是依赖 glib。但是到目前为止，我对上面的无油嘴滑舌的端口还满意。

更新(2018-03/12): eglib 不提供 gthreads，所以不是选项。

## 从用户的角度看有什么不同

Fluidsynth 自动为每个平台选择可用的构建选项。而 opensles 将会是我 Android 端口的默认驱动。

SoundFont loader API 有一些变化，Android 开发者希望使用它(上面解释了一点)。但是除此之外，和原来的源码树没有区别。