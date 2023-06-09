# 我是如何给 BBC 修改代码的

> 原文：<https://dev.to/wincentbalin/audiowaveform-compilation-5eie>

## 简介

我正在做一个更大的项目，其中我需要可视化音频波形。令人惊讶的是，没有多少程序能够做到这一点。即使是所有音频的瑞士军刀， [SoX](http://sox.sourceforge.net/) ，也只绘制频谱图而没有波形。

但是，因为我选择了 JavaScript 库 [Peaks.js](https://github.com/bbc/peaks.js) (由 [BBC](https://github.com/bbc) 开发)在浏览器中绘制波形，我很快发现有一个来自同一作者的名为 [audiowaveform](https://github.com/bbc/audiowaveform) 的实用程序，它将音频文件转换为数据文件，而 [Peaks.js](https://github.com/bbc/peaks.js) 将数据文件用作输入。

只有一个小问题:没有适用于 audiowaveform 的 Windows 二进制文件，甚至还有一个相关的 [GitHub 问题](https://github.com/bbc/audiowaveform/issues/23)。

所以我坐下来，花了一些精力来编译 audiowaveform。我从来不喜欢 *DLL 地狱*，所以我的目标是生产静态二进制文件，32 位和 64 位都可以。我之前为工具`tshark`、`make`、`sed`和`sox`编译静态 Windows 二进制文件的努力在这里帮了很大的忙。

事实上，我通过两种不同的方式达到了目标。

## 第一种编译方式

第一种编译方式使用 [Docker](https://docker.com/) 。这里的编译工具是。你可以在 [README.md](https://github.com/wincentbalin/compile-static-audiowaveform/blob/master/README.md) 文件中了解更多关于设置的信息。

简而言之: [Dockerfile](https://github.com/wincentbalin/compile-static-audiowaveform/blob/master/Dockerfile) 编译所有依赖项(含义: *iconv* ， *zlib* ， *libmad* ， *libid3tag* ， *libogg* ， *libvorbis* ， *flac* ， *libsndfile* ， *libpng* ，*libimage)然后清理目录并重新编译 Win64 的另一个二进制文件。所有生成的二进制文件以及`.h`和`.a`文件都被压缩成 ZIP 档案，因为 Windows 可以不使用外部工具对它们进行解压缩，并使用`docker cp`命令将其复制到主机系统。执行脚本* 

```
./compile_audiowaveform.sh 
```

Enter fullscreen mode Exit fullscreen mode

自动化整个过程。

## 第二种编译方式

有一个免费图书馆的 MinGW 交叉编译环境叫做 [MXE](http://mxe.cc) 。它很简单(本质上是一个单独的`Makefile`)、维护良好(这里是)并且已经包含了所有(前面提到的)依赖项，所以这是一个自然的选择。

使用 MXE 编译的脚本(以及补丁)也驻留在[这个](https://github.com/wincentbalin/compile-static-audiowaveform/)存储库中。用 MXE 把两个文件都复制到机器上，然后执行脚本

```
./compile_audiowaveform_mxe.sh 
```

Enter fullscreen mode Exit fullscreen mode

在环境变量`MXE`中指定 MXE 的路径，在变量`JOBS`中指定并行作业的数量。因此，使用命令
在四核 CPU 上使用目录`/opt/mxe`中的 MXE 编译 audiowaveform with dependencies

```
MXE=/opt/mxe JOBS=4 ./compile_audiowaveform_mxe.sh 
```

Enter fullscreen mode Exit fullscreen mode

过一会儿(因为 MXE 编译了 *boost* 的所有部分，除了必需的部分)你将有两个静态的 Windows 二进制文件，`audiowaveform-win32.exe`和`audiowaveform-win64.exe`，在与脚本相同的目录中。

## 结果

我们现在有两种方式来编译[音频波形](https://github.com/bbc/audiowaveform):通过 Docker 编译[或者通过 MXE](https://github.com/wincentbalin/compile-static-audiowaveform/blob/master/compile_audiowaveform.sh) 编译[。这两种方法都会产生 Win32 和 Win64 二进制文件。](https://github.com/wincentbalin/compile-static-audiowaveform/blob/master/compile_audiowaveform_mxe.sh)

修复的唯一一个编译问题是文件名容器类型之间的差异 POSIX 上的`std::string`和 Windows 上的`std::wstring`。在测试了两种编译方式后，我联系了 [audiowaveform](https://github.com/bbc/audiowaveform) 、 [Chris Needham](https://github.com/chrisn) 的主要开发人员，向他询问将我的代码合并到主存储库中的事宜。他的反应很肯定，于是我做了一个[拉请求](https://github.com/bbc/audiowaveform/pull/61)，他合并了。

BBC 有我的代码修复。我非常兴奋。