# 构建不带 Xcode 的独立 OpenGL 应用程序，带有捆绑的单独着色器文件

> 原文：<https://dev.to/shadoweaver/building-standalone-opengl-application-without-xcode-with-bundled-separate-shader-file-229a>

#### 背景

我有一个“Hello Rectangle”项目，当在 Terminal.app 中调用时，它可以正常运行。但是我在用它创建一个独立的 macOS 包时遇到了问题。

这么说吧，用全 Xcode 是没问题的。我对磁盘空间非常紧张，而且我不做 iOS 开发。
我用的是自制的工具链，包括`gcc-8`、`glfw`和`glew`。

代码库如下所示:

```
src
├── include
│   ├── draw.hpp
│   └── public.hpp
├── resc
│   ├── rect.frag
│   └── rect.vert
├── draw.cpp
├── init-shader.cpp
└── window.cpp 
```

Enter fullscreen mode Exit fullscreen mode

实际的编译命令是:

```
$ g++-8 -lglew -lglfw -framework OpenGL -o <binary> <sources> 
```

Enter fullscreen mode Exit fullscreen mode

目前，在`make`之后，可以在终端中调用二进制文件，前提是两个着色器文件存在于同一个目录中。`init-shader.cpp`中的文件阅读器只接受文件名，而不接受前面的路径。

#### 问题

我不太想暴露着色器文件，如果我读到这个[所以回答](https://stackoverflow.com/a/20443864)正确，它建议要么你在最终版本中打包着色器文件(我正在尝试)，要么你在其他来源中硬编码它们(甚至更不希望)。另外，我想确保当我将它作为独立的`.app`包分发时，它可以开箱即用(只要满足最低 OpenGL 版本要求)，而不是必须获得所有酝酿的依赖关系。

我通读了此 [SO 问题](https://stackoverflow.com/questions/1596945/building-osx-app-bundle)，尝试了上述操作，即:

1.  使用`-headerpad_max_install_names`标志构建。
2.  创建`Info.plist`并按照 macOS 的指示构建目录。
3.  在可执行文件上使用`otool -L`找到所有需要的 dylibs，并递归地在它们上面使用`otool -L`以避免 DLL 地狱(因为缺少 macOS 特定的名称)，排除苹果已经保证的，并将它们复制到`a.app/Contents/Frameworks`。其中四个是在我的箱子里找到的，都在家酿啤酒的地窖里:
    *   3.dylib
    *   libGLEW.2.1.dylib
    *   libstdc++.6.dylib
    *   libgcc_s.1.dylib
4.  使用`install_name_tool -change`将二进制文件中对 dylibs 的引用修改为复制的引用。
5.  将`Info.plist`中的入口点封装到一个 shell 脚本中，这样无论在哪里调用，工作目录都是正确的。

终端一路没有抱怨。但是它不会运行，无论是使用`open -a a.app`还是直接调用实际的二进制文件或那个微小的脚本。

我发现了这篇[的博文](http://seaneshbaugh.com/posts/creating-an-opengl-4-1-program-with-glew-and-glfw-in-xcode)，它暗示我还需要在(2)和(3)之间调用`install_name_tool -id`，在(3)和(4)之间调用`codesign`。

但还是跑不起来。

在前一种情况下，着色器链接器说着色器程序已损坏，但编译将成功，但编译日志为空；在后一种情况下，应用程序会崩溃并且`dyld`会抱怨。

我怀疑前者大多是正确的，因为当我盲目地将文件夹名称从标准的`Contents`更改为`content`(或任何其他名称)时，如果我从终端调用二进制文件，它就会运行，甚至当我调用`brew unlink` `glew`和`glfw`时，这表明二进制文件知道在哪里查找 dylibs 和着色器文件。

我错过了什么？

#### 探查

此时，我的发布目录如下所示:

```
Contents
├── Frameworks
│   ├── libGLEW.2.1.dylib
│   ├── libgcc_s.1.dylib
│   ├── libglfw.3.dylib
│   └── libstdc++.6.dylib
├── MacOS
│   ├── hello-rect    <- the actual binary
│   ├── launcher.sh   <- entry wrapper for working dir as the above link suggests
│   ├── rect.frag
│   └── rect.vert
└── Info.plist 
```

Enter fullscreen mode Exit fullscreen mode

因为非捆绑版本显然除了`cwd`之外什么也看不到，所以我认为捆绑版本也会如此。后来，Google 让我相信我需要`CoreFoundation` API 来正确读取捆绑文件，这毁了我的一天。我了解到 bundle 里应该有一个`Resources`文件夹，然后加了 CF 代码，不知什么原因总是给 segfault 11。我甚至在 SO 上提出了一个新问题。

#### 真正的告诫

纯属偶然，我看到了另一个 [SO 问题](https://stackoverflow.com/questions/17001488/osx-bundle-throwing-exception-only-when-app-in-macos-folder-and-resources-in-res)，它最终拯救了我的一天。**结果是，如果 GLFW 在 bundle 中看到`Resources`，它会将工作目录更改为那个目录！**

#### 解

因此，要点是:如下构建发布文件夹:

```
Contents
├── Frameworks
│   └── <dylibs>
├── MacOS
│   ├── hello-rect
│   └── launcher.sh
├── Resources
│   └── <shader files>
└── Info.plist 
```

Enter fullscreen mode Exit fullscreen mode

然后，遍历前面的列表，没有`install_name_tool -id`或`codesign`，我们就完成了。

最后一个问题:`make`适合把这些都包装成一个脚本吗？