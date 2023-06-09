# 让我们建造查克·诺里斯！-第 6 部分:Android 的交叉编译

> 原文：<https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-6-cross-compilation-for-android-1nd3>

*原载于[我的博客](https://dmerej.info/blog/post/chuck-norris-part-6-android-cross-compilation/)T3。*

*注意:这是[的第 6 部分，让我们打造 Chuck Norris！](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)系列。*

# 简介

在我们讨论了如何用 [Python](https://dmerej.info/blog/post/chuck-norris-part-5-python-cffi/) 包装 C++ 之后，我们现在准备开始使用 C++代码编写 Android 应用程序。

我们将从一个简单的挑战开始:尝试在模拟器中和我们的手机上运行一个用 C 编写的简单的“Hello，world”程序。

我们开始吧！

## 天真的尝试

让我们看看如果我们天真地试图在手机上运行一些 C 代码会发生什么。

*   首先，让我们安装 Android SDK，这样我们就可以使用`adb`工具。
*   然后，让我们激活手机上的开发者模式，并将其插入我们的笔记本电脑。

现在让我们编译一个简单的 C 程序:

```
/* in hello.c */
#include <stdio.h> 
int main() {
  printf("Hello, world\n");
  retrun 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ gcc hello.c -o hello 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们用`adb push`在手机上复制二进制文件，用`adb shell`试着运行它:

(注意，`/data/local/tmp`是我找到的唯一可以运行可执行文件的目录):

```
$ adb push hello /data/local/tmp
$ adb shell
$ cd /data/local/tmp
$ ./hello
/system/bin/sh: ./hello: not executable: 64-bit ELF file 
```

Enter fullscreen mode Exit fullscreen mode

哼。刚刚发生了什么？

## CPU 架构

当你使用编译器时，你从源代码中得到一个二进制文件。你可以把这个二进制文件想象成一个指令列表，准备供 CPU 使用。

诀窍在于不同的 CPU 有不同的*指令集*。

例如，很可能你笔记本电脑上的 CPU 是一个`x86_64` CPU，而你手机上的 CPU 是一个`armv7`。 <sup id="fnref1">[1](#fn1)</sup>

这意味着你为一个`x86_64` CPU 构建的二进制文件不能在一个`armv7` CPU 上运行。

## libc

好的，但是 Android SDK 也自带模拟器，我们可以选择 CPU 架构。

就不能用一个`x86_64`安卓模拟器吗？

如果我们用`x86_64`模拟器做同样的事情，我们会得到一个更奇怪的错误:

```
$ adb push hello /data/local/tmp
$ adb shell
$ cd /data/local/tmp
$ ./hello
/system/bin/sh: ./hello: no such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

该文件确实存在，但 Android 不知道如何运行它。为什么？

还记得我们在[的前一篇文章](https://dmerej.info/blog/post/chuck-norris-part-4-python-ctypes/#using-the-chucknorris-shared-library)中使用`LD_TRACE_LOADED_OBJECTS`的时候吗？

如果我们重新运行刚刚构建的二进制文件，我们可以看到它加载了几个`.so`文件:

```
$ export LD_TRACE_LOADED_OBJECTS=1
$ ./hello

  linux-vdso.so.1 (0x00007ffc1ad96000)
  libc.so.6 => /usr/lib/libc.so.6 (0x00007fb017147000)
  /lib64/ld-linux-x86-64.so.2 (0x00007fb017503000) 
```

Enter fullscreen mode Exit fullscreen mode

这些文件在 Android 上不存在，因为即使 Android 基于 Linux，它仍然是一个不同的操作系统。

看到关于`libc.so.6`的台词了吗？这是包含我们刚刚使用的`printf`函数代码的库。

我们称这样的库为“libc”，这有点误导。

“libc”有几个*实现*。在你的笔记本电脑上，你可能正在使用`glibc`。例如，还有其他类似于`musl`的实现。Android 使用另一种叫做`bionic`的实现。

因此，如果想要实现我们的目标(在模拟器和手机上运行“Hello，world”C 程序)，我们必须做两件事:

1.  确保我们可以告诉编译器关于 CPU 架构的信息
2.  确保我们使用正确的 libc

这很棘手，因为在默认情况下，编译器使用相同的 CPU 架构和相同的 libc，由它们在 <sup id="fnref2">[2](#fn2)</sup> 上运行的操作系统使用。

这就是我们所说的“交叉编译”。

## 使用 NDK 手工编译

谷歌提供了一套被称为 *NDK* 的工具来帮助我们交叉编译 Android 代码。

这里我们用的是版本`r16`。

如果我们下载并提取 NDK，下面是我们如何编译和链接我们的“Hello，World”程序:

```
export NDK_ROOT=/path/to/ndk

${NDK_ROOT}/toolchains/llvm/prebuilt/linux-x86_64/bin/clang
  --target=x86_64-none-linux-android
  --gcc-toolchain=${NDK_ROOT}/toolchains/x86_64-4.9/prebuilt/linux-x86_64
  --sysroot=${NDK_ROOT}/sysroot
  -isystem ${NDK_ROOT}/sysroot/usr/include/x86_64-linux-android
  -pie -o hello.c.o -c hello.c

${NDK_ROOT}/toolchains/llvm/prebuilt/linux-x86_64/bin/clang
  --target=x86_64-none-linux-android
  --gcc-toolchain=${NDK_ROOT}/toolchains/x86_64-4.9/prebuilt/linux-x86_64
  --sysroot ${NDK_ROOT}/platforms/android-21/arch-x86_64
  -pie hello.c.o -o hello 
```

Enter fullscreen mode Exit fullscreen mode

一些注意事项:

*   我们必须指定 Android API 级别(这里是 21 ),就像任何其他针对 Android 的项目一样
*   大部分的魔术是由`--sysroot`、`--gcc-toolchain`和`--target`选项完成的。
*   我们必须指定`-fPIE`，一个表示*位置独立可执行文件*的标志。它的作用与我们在[上一篇文章](https://dmerej.info/blog/post/chuck-norris-part-4-python-ctypes/#building-the-shared-library)中见到的`-fPIC`旗是一样的。

现在我们可以上传并在`x86_64`模拟器上运行二进制文件:

```
$ adb push hello /data/local/tmp
$ adb shell /data/local/tmp/hello
Hello, world 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们可以对`arm`做同样的事情，但是请注意从`x86_64`到`arm` :
的变化是多么微妙

```
export NDK_ROOT=/home/dmerej/Android/Sdk/ndk-bundle

${NDK_ROOT}/toolchains/llvm/prebuilt/linux-x86_64/bin/clang
  --target=armv7-none-linux-androideabi
  --gcc-toolchain=${NDK_ROOT}/toolchains/arm-linux-androideabi-4.9/...
  --sysroot=${NDK_ROOT}/sysroot
  -isystem ${NDK_ROOT}/sysroot/usr/include/arm-linux-androideabi
  -pie
  -o hello.c.o -c hello.c

${NDK_ROOT}/toolchains/llvm/prebuilt/linux-x86_64/bin/clang
  --target=armv7-none-linux-androideabi
  --gcc-toolchain=${NDK_ROOT}/toolchains/arm-linux-androideabi-4.9/...
  --sysroot ${NDK_ROOT}/platforms/android-21/arch-arm
  -pie hello.c.o -o hello 
```

Enter fullscreen mode Exit fullscreen mode

如果我们再试一次:

```
$ adb push hello /data/local/tmp
$ adb shell /data/local/tmp/hello
Hello, world 
```

Enter fullscreen mode Exit fullscreen mode

成功！

我们的下一个目标是在我们的手机和 Android 模拟器上运行我们用来测试第 1 部分中的 Chuck Norris 库的`cpp_demo`可执行文件。

由于`sqlite3`的依赖性和代码是用 C++编写的事实，事情会变得更加棘手。

但是肯定有比猜测如何调用编译器和连接器更好的方法。

# 柯南来了

当然，我们本可以直接使用 Android Studio 的原生插件。

相反，我们将使用柯南，我们在[第二部分](https://dmerej.info/blog/post/chuck-norris-part-2-sqlite-conan/)中谈到的工具。

使用 Conan 是抽象上述复杂性的一个好方法，不会失去任何控制(就像依赖 IDE 插件一样)。

另外，我们可以将我们在柯南中学到的东西应用到其他环境中，比如在 iOS 上编写 C++代码。

## 独立工具链

如果你看一下 Android NDK 的内容，你很快就会意识到那里有很多东西。

在交叉编译器二进制文件中，sysroot 包含了我们在上一节中使用的目录，我们还有一个基于 Makefiles 的完整构建系统，名为`ndk-build`。

NDK 还包含从 14 到 27 的每个 Android API 和每个处理器架构(aarch64、arm、mips、x86、x86_64)的支持文件。

那是一大堆东西，需要大约 3G 的磁盘空间。

然而，在`build/tools`中有一个 bash 脚本叫做`make-standalone-toolchain.sh`。让我们来看看:

> 为给定的 Android 目标创建工具链安装。
> 
> 这个工具的输出是一个更典型的交叉编译工具链。它旨在与现有的构建系统(如自动工具)一起使用。

啊啊！听起来像是我们所需要的，特别是如果以后我们开始依赖 ChuckNorris 项目的自动工具构建的库。

## 构建需求

现在是时候介绍一个新的柯南特性了，构建需求。

基本上，构建需求是只有在从源代码构建时才需要的包。更多信息见[柯南文档](https://docs.conan.io/en/latest/devtools/build_requires.html)。

计划是这样的:

*   首先，让我们有一个下载和提取 NDK 的食谱(希望只有一次)
*   然后让我们用第二个方法来运行`make_standalone_toolchain`脚本，并设置我们需要用 CMake 为 Android 进行交叉编译的内容。

开始之前请注意:我将只向您展示食谱代码的一小部分。这是出于教育目的:为了保持可读性，我必须省略很多细节。

## NDK 食谱

这里有一个简单的 NDK 配方:

```
class AndroidndkConan(ConanFile):
    name = "android-ndk"
    version = "r16"
    settings = "os_build", "arch_build"
    ...

    def source(self):
        url = "https://...android-ndk-%s-linux-x86_64.zip" % self.version
        tools.download(url, "ndk.zip")
        tools.unzip("ndk.zip", keep_permissions=True)
        tools.unlink("ndk.zip")

    def package_info(self):
        tools_path = os.path.join(self.package_folder, "build", "tools")
        ...
        env_info = self.env_info
        env_info.PATH.append(os.path.join(tools_path) 
```

Enter fullscreen mode Exit fullscreen mode

`source()`方法除了获取和提取 NDK 之外什么也不做。

`package_info()`更有趣:它在`env_info.PATH`变量中添加了`build/tools`目录。因此，任何将 NDK 包作为构建需求的配方都可以调用`self.run()`来运行工具目录中的任何二进制文件，因为`PATH`将被相应地设置。

## Android 工具链配方

我们现在可以进入下一个配方，Android 工具链:

```
class AndroidtoolchainConan(ConanFile):

    name = "android-toolchain"
    lib_version = "r16"
    package_version = "r4"
    version = "%s-%s" % (lib_version, package_version)
    license = "GPL/APACHE2"
    url = "https://github.com/lasote/conan-android-toolchain"
    settings = "os", "arch", "compiler"
    build_requires = "android-ndk/r16@dmerej/test"
    ...

    def build(self):
      ...

      toolchain = get_toolchain_str(self.settings.arch)

      command = (
        "make-standalone-toolchain.sh"
          "--verbose"
          "--toolchan=%s",
          "--platform=android-%s"
        ) % (toolchain, self.settings.os.api_level)
        self.run(command)

    def package_info(self):
        sysroot = os.path.join(self.package_folder, "sysroot")

        self.env_info.CC = ...
        self.env_info.AR = ...

        self.env_info.CFLAGS = ... 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们能够使用`self.run()`直接运行`make-standalone-toolchain.sh`脚本。

另一个重要部分是`package_info()`方法，它:

*   设置 sysroot
*   告诉包的使用者编译和链接期间使用的二进制文件的路径(CC 和 AR)
*   通过`CFLAGS`变量设置一些编译标志。

还记得我们手工构建 C 二进制文件的时候吗？

我们可以在我们使用的命令行中找到这些数据的踪迹:

```
${NDK_ROOT}/.../bin/clang                   <- this is CC
  --sysroot ${NDK_ROOT}/...                 <- this is the sysroot
  --target=armv7-none-linux-androideabi     \
  --gcc-toolchain=..                         |<- those are compile flags
  -pie                                      /
  hello.c.o
  -o hello 
```

Enter fullscreen mode Exit fullscreen mode

请注意，配方取决于我们希望编译后的代码运行的架构。这意味着我们将为每个 CPU 架构构建一个`android-toolchain`包。还要注意，NDK 菜谱有一个了解 Android API 级别的设置。

我们可以在哪里指定所有这些配置值？

## 安卓简介

有一个优雅的方法来解决这个问题。

我们在`~/.conan/profiles/android` :
创建一个全球柯南档案

```
[build_requires]
android-toolchain/r16@dmerej/test

[settings]
os=Android
os_build=Linux
os.api_level=21
compiler=clang
compiler.version=5.0

[options]
*:pic = True 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们定义了所有 Android 配置之间的所有公共设置(如 API 级别、编译器和编译器版本)。

注意`[options]`部分的`*:pic`。这将确保所有东西都是用独立于位置的代码构建的，这是运行在 Android 上的任何东西的一个要求。

然后如果我们需要构建表单`x86_64`，我们可以这样调用柯南:

```
$ conan install --profile android --setting arch=x86_64 
```

Enter fullscreen mode Exit fullscreen mode

将使用 android 配置文件中的所有设置，加上我们刚刚在命令行中设置的`arch`。

## 交叉编译 sqlite3

让我们为 Android 交叉编译`sqlite3`:

```
$ conan create . dmerej/test --profile android --setting arch=x86_64
sqlite3/3.21.0@dmerej/test: Exporting package recipe
...
Cross-build from 'Linux:x86_64' to 'Android:x86_64'
...
Requirements
    android-toolchain/r16@dmerej/stable
Packages
    android-toolchain/r16@dmerej/test
...
sqlite3/3.21.0@dmerej/test: Calling build()
-- Android: Targeting API '21' with
      architecture 'x86_64',
      ABI 'x86_64',
      and processor 'x86_64'
...
sqlite3/3.21.0@dmerej/test: Package built 
```

Enter fullscreen mode Exit fullscreen mode

完成:)

## 交叉编译 chucknorris 库

现在让我们尝试构建`ChuckNorris` :

```
$ cd cpp
$ mkdir -p build/android/x86_64
$ cd build/android/x86_64
$ conan install ../../.. --profile android --setting arch=x86_64
....
Cross-build from 'Linux:x86_64' to 'Android:x86_64'
sqlite3/3.21.0@dmerej/test: Already installed! 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们准备构建:

```
$ cd build/android/x86_64
$ cmake -GNinja ../../..
$ ninja
-- The C compiler identification is GNU 7.3.0
-- The CXX compiler identification is GNU 7.3.0
-- Check for working C compiler: /bin/cc
-- Check for working C compiler: /bin/cc -- works
...
-- Check for working CXX compiler: /bin/c++
-- Check for working CXX compiler: /bin/c++ -- works
...
CMake Error at build/android/x86_64/conanbuildinfo.cmake:452 (message):
  Incorrect 'clang', is not the one detected by CMake: 'GNU' 
```

Enter fullscreen mode Exit fullscreen mode

啊哈:CMake 正在使用默认编译器`/bin/cc`。这没用的。

但是柯南知道如何为 Android 交叉编译`sqlite3`！不能让柯南也建 chucknorris 吗？

## 为 chucknorris 制作食谱

我们当然可以！让我们去`cpp/ChuckNorris`运行`conan new` :

```
$ conan new ChuckNorris/0.1 --source 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们把生成的`conanfile.py`编辑成有:

```
from conans import ConanFile, CMake

class ChucknorrisConan(ConanFile):
    name = "ChuckNorris"
    version = "0.1"
    license = "MIT"
    url = "https://github.com/dmerejkowsky/chucknorris"
    description = "Chuck Norris does not need a description"
    settings = "os", "compiler", "build_type", "arch"
    options = {"shared": [True, False]}
    default_options = "shared=False"
    generators = "cmake"
    exports_sources = "CMakeLists.txt", "src/*", "include/*"

    def build(self):
        cmake = CMake(self)
        cmake.configure()
        cmake.build()

    def package(self):
        self.copy("cpp_demo" dst="bin", keep_path="false") 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们没有使用`source()`方法从远程位置获取源代码(就像我们对`sqlite3`所做的那样)，而是使用`exports_sources`告诉柯南构建包所需的源代码。

还要注意我们如何在`package()`方法中复制`cpp_demo`二进制文件。我们将用它来检查我们用柯南构建的二进制文件是否真的可以*运行*。

然后我们创建 chucknorris 包:

```
$ cd cpp/ChuckNorris
$ conan create . dmere/test --profile android --setting arch=x86_64
ChuckNorris/0.1@dmerej/test: Exporting package recipe
...
Cross-build from 'Linux:x86_64' to 'Android:x86_64'
...
-- Build files have been written to: ...
../bin/clang++
  --target=x86_64-none-linux-android
  --gcc-toolchain=...
  -sysroot=...
  -isysroot=...
  -fPIC
  -fPIE -pie
  .../sqlite3.a
  ... 
```

Enter fullscreen mode Exit fullscreen mode

嗯，它确实构建了，Ninja 运行的命令与我们在上一节中手写的命令非常相似。

让我们检查它运行！

```
$ cd ~/.conan/data/ChuckNorris/0.1/dmerej/test
$ cd package/<hash>/bin
$ adb push cpp_demo /data/local/tmp/
$ adb shell /data/local/tmp/cpp_demo
CANNOT LINK EXECUTABLE "/data/local/tmp/cpp_demo":
  library "libc++_shared.so" not found 
```

Enter fullscreen mode Exit fullscreen mode

什么？

## libc++

还记得我们谈论`libc`的时候吗？嗯，对于 Android 上的`C++`,有两种可能的选择。

您必须在`gnustl`库或`libc++`库之间做出选择，它们都有两种风格(静态或共享)。

默认情况下，我们的二进制文件被编译成与共享版本的`libc++`链接，因此文件名为:`libc++_shared.so`。

幸运的是，我们可以使用柯南的另一个功能来帮助我们:`imports()`功能。这个函数在构建之前被调用，可以用来从依赖包中复制文件。

我们可以这样做:

*   使用`imports()`将`libc++_shared.so`从`android-toolchain`包复制到构建目录。
*   使用`keep_imports`以便导入的文件不会从构建目录中删除。
*   在`package()`方法中添加一个`copy()`调用，以便`libc++_shared.so`文件出现在最终的包中。

按照柯南的说法，这被称为“重新包装”。

```
class ChucknorrisConan(ConanFile):
    ...
    keep_imports = True

    def imports(self):
        self.copy("*libc++_shared.so", dst="lib")

    def package(self):
        self.copy("bin/cpp_demo", dst="bin", keep_path=False)
        self.copy("lib/libc++_shared.so", dst="lib", keep_path=False) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以再试一次，使用`LD_LIBRARY_PATH`环境变量告诉链接器在哪里寻找共享库:

```
$ conan create . dmere/test --profile android --setting arch=x86_64
$ cd ~/.conan/data/ChuckNorris/0.1/dmerej/test
$ cd package/<hash>/
$ adb push bin/cpp_demo /data/local/tmp
$ adb push lib/libc++_shared.so /data/local/tmp/
$ adb shell
$ cd /data/local/tmp
$ LD_LIBRARY_PATH=lib ./cpp_demo
When a zombie apocalypse starts, Chuck Norris doesn't try to survive.
The zombies do. 
```

Enter fullscreen mode Exit fullscreen mode

万岁！

我们也可以检查它在 arm 手机上的工作情况:

```
$ cd cpp/conan/android-toolchain
$ conan create . dmerej/test --profile android --setting arch=armv7
$ cd cpp/conan/sqlite3
$ conan create . dmerej/test --profile android --setting arch=armv7
$ cd cpp/ChuckNorris
$ conan create . dmerej/test --profile android --setting arch=armv7
$ cd ~/.conan/data/ChuckNorris/0.1/dmerej/test
# This would be a different hash since the settings have changed:
$ cd package/<hash>/
$ adb push bin/cpp_demo /data/local/tmp
$ adb push lib/libc++_shared.so /data/local/tmp/
$ adb shell
$ cd /data/local/tmp
$ LD_LIBRARY_PATH=lib ./cpp_demo
Giraffes were created when Chuck Norris uppercutted a horse. 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

我们最终设法直接在 Android 上运行一些`C++`代码。

但是 Android 应用是用 Java(或者 Kotlin)写的，所以我们还是需要用 Java 包装 C++库。

下集[第七部](https://dmerej.info/blog/post/chuck-norris-part-7-android-jna/)见:)

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  您可能正在使用其他体系结构而没有意识到这一点，但不要对此过于担心。 [↩](#fnref1)

2.  其实是一件*好事*。否则，您将无法运行和调试刚刚编译的二进制文件。 [↩](#fnref2)