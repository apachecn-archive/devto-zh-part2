# Fluidsynth 黑客继续:双簧管驱动程序

> 原文：<https://dev.to/atsushieno/fluidsynth-hack-continued-oboe-driver-10fg>

## 自上次努力以来的更新

两周前，我终于得到了一个支持 OpenSLES 驱动程序的实用 fluidsynth 端口。那是在对构建系统进行大规模重组之后(构建 glib 是一项复杂的任务，由于 Android NDK 的发展，我的构建设置变得一团糟)。当我成功构建时，已经有了一个新的“更好”的音频驱动双簧管(它是现代的，比以前的延迟更低)。

给你，双簧管司机完成了:[https://github.com/FluidSynth/fluidsynth/pull/464](https://github.com/FluidSynth/fluidsynth/pull/464)

这篇文章是关于新 Oboe 驱动实现的后续文章。地面上有很多给我下药的陷阱...

## 混合 C++和 C

如果你像我一样不熟悉 CMake，你会发现很难找到不让 CMake 忽略 c++源代码的正确方法。您需要在`add_library`函数调用中显式地使用`CXX`,这需要一个用于编译的语言列表。CMake 会默默地忽略您的`*.cxx`文件，即使您在源代码列表中明确指定了它们。

## (历史)Oboe 需要安卓 NDK r17，Cerbero 指定 r16

这只是一个过去的问题，但 Cerbero `android.config`明确指定 Android NDK r16。大概 5 天前升级到 r18。

NDK r16 在 AAudio API 中缺少一些必需的常量定义，这导致 Oboe 构建失败。因此，无论如何都必须使用 NDK r17 或更高版本。

我最终破解了 Cerbero 资源(再次！)-这一次很简单，特定的音频问题得到了解决。但是另一个问题出现了...

## gnustl 和 libc++

[从安卓 NDK r17](https://github.com/android-ndk/ndk/wiki/Changelog-r17) 开始，它正在远离 gnustl，开始在 clang 中使用 libc++。随着 r18 的推出，gcc 和 co .完全消失了，这意味着再也没有 gnustl 了。

我一直在使用 Cerbero 构建系统来获得一个包含所有依赖项的 glib 构建。不幸的是，他们的构建脚本仍然在`config/cross-android-*.cbc`中指定 android-16 (JellyBean ),这引发了一些问题。让我们看看 NDK r17 发行说明...

> libc++现在是 CMake 和独立工具链的默认 STL。如果您手动选择了不同的 STL，我们强烈建议您迁移到 libc++。注意 ndk-build 仍然默认没有 STL。更多细节，请看这篇博文。

也...

> 平台静态库(libc.a，libm.a 等。)已更新。
> 
> *   所有 NDK 平台现在都包含这些静态库的现代版本。以前它们都是姜饼(也许更古老)或棒棒糖。
> *   由于 libc.a 和 libandroid_support 之间的符号冲突，以前的 NDK 不能使用具有现代 NDK API 级别的静态库。这个问题已经通过移除对现代 API 级别的 libandroid_support 解决了。这样做的一个副作用是，您现在必须至少以 android-21 为目标才能使用静态库，但是这些二进制文件仍然可以在旧设备上工作。"

让事情变得复杂的是 Cerbero android build，它通常(即`android/external/cerbero/packages/base-system-1.0.package`)将“gnustl”指定为依赖项的一部分。gnustl 是来自 GCC 的标准 C++ STL 实现库。参见 NDK r17 发行说明中的第一段引文。他们现在已经转向 libc++，并且“强烈鼓励”转向 libc++...

如果我们混合使用两者会发生什么？一些“未解析的引用”错误是由于缺少任何一个 at 链接。

不幸的是，gnustl 在许多食谱中被广泛使用，这应该由任何参与 Cerbero well(希望不是很复杂)的人来处理。

## 解决方案:两个共享库

与上面的问题相比，处理起来要容易得多，但是 Oboe 的另一个小问题是它需要 C++构建支持。而那稍微搞乱了现有的`CMakeLists.txt`。

为了解决这些问题，我最终构建了一个基于 C 的 oboe 共享库`liboboe-c.so`和`libfluidsynth.so`。

*   liboboe-c.so:基于 C++，链接 libc++
*   libfluidsynth.so:基于 C，链接 gnustl

虽然我一直试图让这个工作，有一些新的承诺在 Cerbero 和它已转移到 NDK r18。但是由于 gnustl 还在使用，符号期望不匹配的情况还是会发生。我决定保留这个`liboboe-c.so`,直到它变得完全没有必要...

(将来当 Android NDK r19 登陆时，它也将从黄金链接器迁移到 lld，这可能会带来其他类型的问题。)

(我以后可能会添加更多的行，但到目前为止，我会先发布这一行，让它显示我对它们所做的工作。)