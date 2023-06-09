# Android 版 Fluidsynth 2.0.x

> 原文：<https://dev.to/atsushieno/fluidsynth-20x-for-android-4j6b>

## TL；速度三角形定位法(dead reckoning)

*   我的 fluidsynth Android 端口又上线了，赶上了最新的 fluidsynth 开发(2.0.x)。
*   有基于 JNA 的 Java 版 fluidsynth。
*   现在有了原生资产加载器。

## 回顾上一篇文章

早在三月份，我在 Android 的 Fluidsynth 端口上写了一篇文章。从那以后，我花了一些时间或者一直忙于其他事情(比如作为管理团队参加最近的 [TechBookFest5](https://sangorrin.blogspot.com/2018/10/techbookfest-5.html) )。但是现在我没有工作，有了更多灵活的时间，我可以恢复我的 Fluidsynth 端口以及我一直在做的许多其他音乐相关软件。无论如何...

概括一下最新的状态，我有了一个 Android 的 fluidsynth 的端口，使用了 T2 的 Cerbero 的一个分支，由我自己的名为 T4 的 android-fluidsynth 模块组织。构建是复杂的，几乎没有人可以使用我的成果，最初的 Fluidsynth 已经[转移到 github](https://github.com/Fluidsynth/fluidsynth) 进行不断的变化，例如迁移到 CMake 构建系统。Cerbero 上的 CMake 构建系统并不能很好地适用于 Android。

## 复活和重组

更糟糕的是，当谷歌将 Android NDK 更新到我都不记得的版本时，我的 android-fluidsynth 版本被破坏了——我不得不在我的 Cerbero fork 树中管理 Android NDK 支持，这有点烦人。NDK 做了各种改变，使得更新变得混乱——支持 aarch64，切换到统一标题(这意味着文件查找路径改变)，默认工具链切换到 Clang(gcc 过时了)。

因此，我从重构整个构建依赖关系开始。这是我开始时有的:

```
atsushieno/fluidsynth-midi-service
  atsushieno/android-fluidsynth (submodule)
    atsushieno/cerbero (submodule)
      atsushieno/fluidsynth (git checkout) 
```

我的 cerbero 更改是为了 1) NDK 查找更改，和 2)额外的 fluidsynth 构建配方。我决定从那里删除 fluidsynth 构建。这意味着，cerbero 仍然构建 glib(仍然需要作为一个依赖项)，但我可以只使用结果，并从另一个基于 CMake 的 fluidsynth 构建(这是当前的构建系统)中引用它。

(我甚至可以为 glib 打包那些原生二进制文件，但现在只是为了检查和运行 cerbero build，这是由他们自己的团队维护的，对我来说足够简单。)

为了与此保持一致，我最终创建了另一个 fluidsynth 分支，它基于最新的原始树(github 上的 Fluidsynth/fluidsynth)并移植了我的 opensles 附加物(需要一些额外的更改，但这在这里并不重要)。

用这种方法，我可以大大减少我的依赖树:

```
atsushieno/fluidsynth-midi-service
  atsyshieno/fluidsynth-fork (new, submodule)
    GStreamer/cerbero (on-the-fly checkout at build time. Not on github) 
```

...并重新构建(！)

一旦我可以向最初的 Fluidsynth repo 提交一个 PR 并被接受，那么依赖关系将看起来更简单，但让我们以后再考虑它。

## 基于 Kotlin 的应用

在早期，我一直在使用 Xamarin。Android 作为这个 fluidsynth 端口的主要应用程序开发框架。然而，在我退出开发团队后，在 Linux 上继续使用 IDE 变得不可能了。我仍然继续开发，但是没有 IDE 它变得相当困难。

经过许多思考，我最终开始将我的 C#应用程序的一部分移植到 Kotlin(T1)上，用 to start)代替 T4 的 NFluidsynth(T5)。实际的 API 绑定几乎是使用[jnforer](https://github.com/nativelibs4java/JNAerator)自动生成的。因为我不想惹 JNI，这对我是一个很大的帮助。(过去我是 BridJ 的撰稿人，但现在 JNA 看起来更容易依赖。)

移植的应用远非功能对等端口，但至少足以喂饱 fluidsynth Android 端口。最后将会有完整的功能 MidiDeviceService 实现。

## 为 Android 资产定制声音字体加载器

Fluidsynth 2.0 中最大的 API 变化之一是完全重写的自定义 SoundFont 加载器。在 Fluidsynth 1.x 中，我们必须完全实现 SF loader，它不仅提供自定义流处理器，还必须构建整个 SoundFont 结构，这使得几乎不可能进行自定义。Fluidsynth 2.0 提供了一种新的方式来提供一组自定义的“流回调”(用于打开/读取/查找/告诉/关闭)，让我们实现自己的流加载器。

我以前的端口做了一些修改，提供了具有完全相同目的的自定义流加载器，所以我可以完全删除这些修改。相反，现在我(有点)必须为 Android 资产提供定制的 SF loader 功能，以适应新的方式。

有两种选择:

*   在本机(如 NDK 方式)API 中实现自定义回调。
*   在包装语言中实现回调。Android，面向序数 Android 开发者的 Java)。

让前者(原生 API approeach)讨厌的是，NDK 资产 API 要求`JNIEnv`和`jobject`获得`AAssetManager`。这对于我新开发的基于 Kotlin 的应用程序来说尤其烦人。最后我在安卓端口加了`Java_fluidsynth_androidextensions_NativeHandler_setAssetManagerContext()`功能。(这很讨厌，尤其是因为任何试图使用这个 API 的 Java 代码都需要在`fluidsynth.androidextensions`包中有`NativeHelper`类，我在我的 Kotlin 应用中提供了这个包。我甚至把我的 Kotlin 应用程序包从`name.atsushieno.fluidsynth`改名为`fluidsynth`，让它不那么讨厌...)

第二种方法(用包装语言编写回调)看起来可行，但是需要特别注意提供这些回调功能的 Java 对象的 GlobalRefs 每当 GC 移动您的方法引用时，资产 SF loader 就会崩溃！我还没有用我的 JNAerated API 解决这个问题(C#版本的工作原理是我用`GCHandle`“钉住”它)。本机互操作是一个没有足够崩溃信息或调试机会的领域...

## 构建可调试的 libfluidsynth.so

即使在我获得了一个“成功的”构建之后，它也从未成功运行过。长期存在的问题是一个意想不到的信号。这个问题甚至比开始时更幸运地被注册，因为它有一个可调试的二进制文件(！idspnonenote)。)

有各种各样的机会表明`libfluidsynth.so`是在没有调试符号的情况下构建的。例如，你必须...

*   在 CMake 中显式指定调试选项如`-Denable-debug=on`，并给出额外的`-fsanitize=undefined`和`-fsanitize-trap=undefined`标志，
*   确保通过`-g`或那些`-fsanitize=undefined`等。gcc 执行(独立工具链使用)，或
*   指定`NDK_DEBUG`并确保为`ndk-build`杀死`cmd-strip`

经过这些努力，我终于可以让 lldb 和我的`libfluidsynth.so`一起工作了。这是一次长途旅行。

根据这次经历，我最终为我们的独立科技书籍《科技图书节》写了一篇关于这些技巧的长篇文章...

## 对抗西格

我在上一节开头提到的 SIGILL 问题实际上花了一些时间...调试器没有给出太多关于“为什么”的信息(尽管它给出了“在哪里”，这仍然是信息性的)。相关功能是:

```
static int chunkid(unsigned int id)
{
    unsigned int i;
    const unsigned int *p;

    p = (const unsigned int *)&idlist;

    for(i = 0; i < sizeof(idlist) / sizeof(int); i++, p += 1)
    {
        if(*p == id)
        {
            return (i + 1);
        }
    }

    return UNKN_ID;
} 
```

这很奇怪，尤其是因为[有问题的代码](https://github.com/FluidSynth/fluidsynth/blob/da6a2e7a91820bf97f89d3bcdb15b76e94e90bc2/src/sfloader/fluid_sffile.c#L494)在过去运行良好。

实际上，这是有问题的。这是 clang 报道的:

```
/sources/fluidsynth-midi-service-j/external/fluidsynth/src/sfloader/fluid_sffile.c:494:9: warning: 
      cast from 'const char (*)[117]' to 'const unsigned int *' increases
      required alignment from 1 to 4 [-Wcast-align]
    p = (const unsigned int *)&idlist;
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~
1 warning generated. 
```

如果你在谷歌上搜索信息的“增加所需对齐”部分，你会发现它确实会导致未定义的行为，比如 SIGILL。

一个简单的解决方法解决了这个问题。

可能是最新的 NDK 从 gcc 到 clang 的变化或者 Fluidsynth build(在 CMakeLists.txt 中)中可能的编译器选项变化触发了这个破坏，但是我没有确切的想法。

## 修复音频故障

即使在以前使用旧 Fluidsynth 端口的工作状态下，音频播放也“有问题”。中间总是有噪音，就像合成样本之间有一些空白。

还有一些恼人的警告，OpenSLES 在设备日志上显示播放缓冲区不足，这意味着有太多的合成缓冲区需要在使用它们之前进行查询。看起来我排队太多了。

在继续之前，我应该解释一下缓冲的内部机制。有两种方法可以将合成音频缓冲区排入队列:

*   运行一个独立的音频循环。获取合成的缓冲区，并在它还活着的时候将它们排队。
*   注册 OpenSLES 回调。获取合成的缓冲区，并在回调中将它们排队。

在早期的代码库中，我有一个“调整”延迟以计算精确缓冲时间的简单方法，这通常通过同步 usleep()调用来调整时间。当它运行一个独立的音频运行线程(以前的模型)时，它仍然有意义，但只有当缓冲“早于预期的时间表”时。试图通过 OpenSLES 回调来调整延迟是没有意义的。

让调查变得复杂的是，我花了一段时间才发现由设置 API 切换的缓冲选项没有像预期的那样工作(由于我自己代码中的伪默认值检索)。我也被两个不同的“回调”搞糊涂了，一个是 OpenSLES，一个是 Fluidsynth。毕竟，对代码的精确理解和减少混乱的代码清理让我找到了正确的解决方案...

解决这个问题后，所有与缓冲相关的问题都消失了，端口变得真正可用了。仍然需要一些设置来让工作进入良好状态，但我对结果很满意。

## 下一步

安卓音频形势不断向前发展。自 Android O (API 级别 26)以来，谷歌已经引入了新的 [AAudio API](https://developer.android.com/ndk/guides/audio/aaudio/aaudio) ，这为通过少量方式(直接缓冲、高优先级音频回调、API 中的低延迟模式规范)降低延迟带来了机会。谷歌首先声明 AAudio 将被反向移植到早期的 Androids，但实现的是一个名为 [Oboe](https://github.com/google/oboe) 的新音频 API，它提供了一个统一的 API 来切换两个后端实现(AAudio 和 OpenSLES)。

当我写上一篇文章时，Oboe 处于预览状态(或者说，当我开始移植时它甚至还不存在)，但现在 Oboe 已经正式稳定，支持它比支持 OpenSLES 更有意义。公平地说，支持 OpenSLES 变得完全多余...所以，接下来就是支持 Oboe 了。