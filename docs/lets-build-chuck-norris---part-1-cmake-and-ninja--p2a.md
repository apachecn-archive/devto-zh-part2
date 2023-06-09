# 让我们建造查克·诺里斯！-第 1 部分:CMake 和忍者

> 原文：<https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-1-cmake-and-ninja--p2a>

*最初发表于[我的博客](https://dmerej.info/blog/post/chuck-norris-part-1-cmake-ninja/)。*

*注意:这是[的第一部分，让我们来打造 Chuck Norris！](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)系列。*

# 核心库

我们想要一个带有返回随机 Chuck Norris 事实的`getFact()`方法的`ChuckNorris`类。

现在让我们从一个硬编码的答案开始:

*include/chuck Norris . HPP*:

```
#pragma once
#include <string> 
class ChuckNorris {
  public:
    ChuckNorris();
    std::string getFact();
}; 
```

Enter fullscreen mode Exit fullscreen mode

*src/ChuckNorris.cpp* :

```
#include <ChuckNorris.hpp> 
ChuckNorris::ChuckNorris()
{
}

std::string ChuckNorris::getFact()
{
  return "Chuck Norris can slam a revolving door.";
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在`include/ChuckNorris.hpp`中有一个包含类*声明*的头文件，在`src/ChuckNorris.cpp`中有一个包含类*定义*的文件。

# 测试程序

为了确保该库确实可以在其他程序中使用，让我们添加一个`src/main.cpp`来检查我们是否设法获得了硬编码的事实:

*src/main.cpp* :

```
#include <ChuckNorris.hpp>
#include <iostream> 
int main()
{
  ChuckNorris chuckNorris;
  std::string fact = chuckNorris.getFact();
  std::cout << fact << std::endl;
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们所要做的就是不带参数地运行程序，并检查显示的内容。这可能足以检查我们的图书馆作品。

# 目标

让我们列举一下我们现在必须做些什么来构建一切:

*   确保我们在`src/`中编译代码时使用了`include/`目录
*   使用`src/ChuckNorris.cpp`构建一个库
*   使用与上述库链接的`src/main.cpp`构建一个名为`cpp_demo`的可执行文件。

我们可以说我们有两个*目标*:名为`chucknorris`的库和名为`cpp_demo`的可执行文件:

下面是在 Linux 上构建它们的命令:

```
# Compile the .cpp file into a .o
$ g++ -c -I include/ src/ChuckNorris.cpp -o libchucknorris.o
# Create an archive containing the .o
$ ar qc libchucknorris.a libchucknorris.o
# Run ranlib so that the archive can be used by the linker
$ ranlib libchucknorris.a
# Compile main.cpp into main.o
$ g++ -c -I include/ src/main.cpp -o main.o
# Tell g++ to link everything into an executable
$ g++ main.o libchucknorris.a -o cpp_demo
# Run the executable we've just built:
$./cpp_demo
Chuck Norris can slam a revolving door. 
```

Enter fullscreen mode Exit fullscreen mode

唷！

手动跟踪所有这些很快就会变得乏味，但幸运的是有工具可以帮助我们。

# CMake

CMake 让我们用代码描述我们想要构建的目标:

```
cmake_minimum_required(VERSION 3.10)

project(ChuckNorris)

add_library(chucknorris
    include/ChuckNorris.hpp
    src/ChuckNorris.cpp
)

target_include_directories(
  chucknorris
  PUBLIC
    "include"
)

add_executable(cpp_demo
  src/main.cpp
)

target_link_libraries(cpp_demo chucknorris) 
```

Enter fullscreen mode Exit fullscreen mode

`add_library`和`add_executable`命令描述了两个目标(名为`chucknorris`和`cpp_demo`)以及用于构建它们的源代码，`target_link_libraries`命令告诉 CMake`cpp_demo`*依赖于`chucknorris`*。

`target_include_directories`通知 CMake 在`include`目录中有头文件，这些头文件既可以在构建库本身时使用，也可以由库的消费者使用。(我们在建造`libchucknorris.o`和`main.co`时都使用了`-I include/`旗)

如果头文件仅用于编译库，我们将使用`PRIVATE`关键字，如果它们仅由库的消费者使用，我们将使用`INTERFACE`关键字。您可以在 [CMake 文档](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html)中了解更多相关信息。

# 忍者

既然我们已经描述了我们想要构建什么以及如何构建，我们仍然需要执行构建本身。

CMake 不知道如何*实际*执行构建。相反，它生成的文件将被另一个*工具使用。它被称为 T4 发电机。*

有大量的发电机可用，但现在我们只谈论忍者。我已经在另一篇博文中解释了为什么我更喜欢使用 CMake 和 Ninja。

在我们的第一次尝试中，我们直接在当前工作目录中生成了所有的二进制文件(`libchucknorris.a`、`.o`文件和`cpp_demo`可执行文件)。将它们放在专用的*构建文件夹*中会更干净:

*   我们只需将构建文件夹放入我们的`.gitignore`文件，而不是一堆文件
*   如果我们愿意，我们可以有几个构建文件夹，都使用相同的源代码，但使用不同的编译器或标志，而不会有混合不兼容的二进制文件的风险。

所以让我们创建一个名为`build/default`的文件夹并调用 CMake，要求它使用 Ninja 生成器。CMake 使用当前工作目录作为构建文件夹，并且您必须指定包含`CMakeLists.txt`文件的文件夹的路径作为命令行的最后一个参数:

```
$ mkdir -p build/default
$ cd build/default
$ cmake -GNinja ../.. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们使用 ninja 构建 build 并从 build 文件夹运行我们的可执行文件:

```
$ cd build/default
$ ninja
[1/4] Building CXX object CMakeFiles/chucknorris.dir/src/ChuckNorris.cpp.o
[2/4] Building CXX object CMakeFiles/cpp_demo.dir/src/main.cpp.o
[3/4] Linking CXX static library libchucknorris.a
[4/4] Linking CXX executable cpp_demo
$ ./cpp_demo
Chuck Norris can slam a revolving door. 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。

请注意，CMake 和 Ninja 相互合作，因此您只需重建需要重建的内容。

如果我们只改变`main.cpp`，我们只需要重建`main.cpp.o`并重新链接`cpp_demo` :

```
$ ninja
[1/2] Building CXX object CMakeFiles/cpp_demo.dir/src/main.cpp.o
[2/2] Linking CXX executable cpp_demo 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们改变`ChuckNorris.cpp`，除了 `main.cpp.o`之外的*都需要重建:* 

```
[1/3] Building CXX object CMakeFiles/chucknorris.dir/src/ChuckNorris.cpp.o
[2/3] Linking CXX static library libchucknorris.a
[3/3] Linking CXX executable cpp_demo 
```

Enter fullscreen mode Exit fullscreen mode

如果我们更改了`CMakeLists.txt`文件，Ninja 将为我们重新运行 CMake:

```
$ ninja
[0/x] Re-running CMake...
-- Configuring done
-- Generating done
-- Build files have been written to: /path/to/build/default
... 
```

Enter fullscreen mode Exit fullscreen mode

第一部分到此为止。请继续关注[第 2 部分](https://dmerej.info/blog/post/chuck-norris-part-2-sqlite-conan/)，在这里我们将引入一个外部依赖，并去掉硬编码的事实。

* * *

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)，了解更多与我联系的方式。