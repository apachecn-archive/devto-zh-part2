# 让我们建造查克·诺里斯！-第二部分:SQLite 和柯南

> 原文：<https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-2-sqlite-and-conan-5acj>

*最初发表于[我的博客](https://dmerej.info/blog/post/chuck-norris-part-2-sqlite-conan/)。*

*注意:这是[的第二部分，让我们来打造 Chuck Norris！](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)系列。*

在前一集，我们编写了一个简单的 C++库，它返回了一个硬编码的 Chuck Norris 事实。

现在是时候将几个 Chuck Norris 事实放入数据库，并在被询问时随机选择一个。

为此，我们将使用 [sqlite](https://www.sqlite.org/index.html) 。

首先，让我们修改我们的类声明:

*include/chuck Norris . HPP*:

```
#include <string> 
#include <sqlite3.h> 
class ChuckNorris
{
  public:
    ChuckNorris();
    // Make sure you cannot copy Chuck Norris
    ChuckNorris(ChuckNorris const&) = delete;
    ChuckNorris(ChuckNorris &&) = delete;
    ChuckNorris& operator=(ChuckNorris const&) = delete;
    ChuckNorris& operator=(ChuckNorris &&) = delete;
    ~ChuckNorris();

    std::string getFact();

  private:
    sqlite3* _db;
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后，是时候修改`ChuckNorris.cpp`文件了:

在构造函数中，我们打开一个内存数据库并用大量事实填充它。

然后，在`getFact()`方法中，我们运行一个简单的 SQL 查询:

*src/ChuckNorris.cpp* :

```
// Note: Error handling omitted for brevity.

#include <sqlite3.h> 
ChuckNorris::ChuckNorris()
{
  sqlite3_open(":memory:", &_db);

  auto const sql = R"(
CREATE TABLE chucknorris(id PRIMARY_KEY, fact VARCHAR(500));
INSERT INTO chucknorris (fact) VALUES
  ("Chuck Norris can slam a revolving door.");
INSERT INTO chucknorris (fact) VALUES
  ("Chuck Norris can kill two stones with one bird.");
  ...
  )";

  auto res = sqlite3_exec(db, sql, 0, 0, nullptr);
}

std::string ChuckNorris::getFact()
{
  sqlite3_stmt* statement;
  sqlite3_prepare_v2(_db,
      R"(SELECT fact FROM chucknorris ORDER BY RANDOM() LIMIT 1;)",
      -1, &statement, 0);
  rc = sqlite3_step(statement);
  auto sqlite_row = sqlite3_column_text(statement, 0);
  auto row = reinterpret_cast<const char*>(sqlite_row);
  auto res = std::string(row);
  sqlite3_finalize(statement);
  return res;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们试着编译它！

# 第三方库

因为我们不是`sqlite`库的作者，所以我们说它是一个*第三方*库。(与我们刚刚编写的`chucknorris`库相反)。

向 C++程序添加第三方依赖项的方法有很多，从简单地将源代码添加到项目文件，到“在系统中”安装它们(在 macOS 上使用`homebrew`或者在 Linux 上使用发行版的包管理器)。

在本文中，我们将使用一个名为[柯南](https://conan.io)的包管理器，它将把`sqlite3`包安装在我们的主目录中的某个地方(称为*缓存*)。

这意味着我们不需要任何管理特权(相对于在系统中安装`sqlite3`库)，而且这个库可以在多个 C++项目中使用(相对于在我们的 source 文件夹中添加`sqlite3`的源代码)。

当我们稍后开始交叉编译东西时，使用柯南会派上用场，但我有点超前了。

# 手工添加依赖关系

先从“手动”做作品开始，再讲柯南是怎么做的。

## 建筑广场 3

首先，我们转到 sqlite3 项目的下载页面[。](https://www.sqlite.org/download.html)

然后我们获取“almagation”档案，并提取它:

```
$ cd $HOME
$ mkdir -p 3rdpart/sqlite
$ cd 3rdpart/sqlite
$ wget https://www.sqlite.org/2018/sqlite-amalgamation-3220000.zip
$ unzip sqlite-amalgamation-3220000.zip
$ cd sqlite-amalgamation-3220000
$ ls
$ shell.c sqlite3.c sqlite3.h sqlite3ext.h 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们只需要构建一个`sqlite3.c`和一个可以包含的`sqlite3.h`头。

让我们建立一个静态库:

```
$ gcc -c sqlite3.c -o sqlite3.o
$ ar qf libsqlite3.a sqlite3.o
$ ranlib libsqlite3.a 
```

Enter fullscreen mode Exit fullscreen mode

并且，只是为了清理一下，让我们创建一个`lib`和`include`文件夹:

```
$ mkdir include lib
$ mv libsqlite3.a lib/
$ mv sqlite3.h include/ 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须修改 Chuck Norris 源代码中的`CMakeLists.txt`,以使用我们新建的库。

## 查找和使用 sqlite3 库

用 CMake 的话来说，`sqlite3`不再是一个“常规”目标，因为它不知道自己是如何建造的。

所以我们需要创建一个*导入的*目标，并在其上设置`IMPORTED_LOCATION`和`INTERFACE_INCLUDE_DIRECTORIES`属性:

```
project(ChuckNorris)

# ...

add_library(sqlite3 STATIC IMPORTED)
set_target_properties(sqlite3
  PROPERTIES
  IMPORTED_LOCATION /path/to/sqlite3-<version>/lib/libsqlite3.a
  INTERFACE_INCLUDE_DIRECTORIES /path/to/sqlite3-<version>/include
)

add_library(chucknorris
  # ...
) 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们使用`target_link_libraries`在我们的`ChuckNorris`库和导入的目标之间添加一个依赖项，就像我们链接`cpp_demo`和`ChuckNorris` :
一样

```
target_link_libraries(chucknorris sqlite3) 
```

Enter fullscreen mode Exit fullscreen mode

让我们重新构建 ChuckNorris 项目:

```
$ cd build/default
$ cmake -GNinja ../..
$ ninja -v
Build CXX object ChuckNorris.cpp.o
/bin/c++
  ../../src/ChuckNorris.cpp
  -I../../include
  -isystem /path/to/sqlite-<version>/include
  -o ChuckNorris.cpp.o
  ...
Linking CXX executable cpp_demo
/bin/c++
  main.cpp.o
  -o cpp_demo
  libchucknorris.a
  /path/to/sqlite-<version>/lib/sqlite3.a 
```

Enter fullscreen mode Exit fullscreen mode

可以看到 CMake 在编译`ChuckNorris.cpp`时在一个`-isystem`标志<sup id="fnref1">T5】1</sup>中添加了 sqlite3 的包含路径，在链接`cpp_demo`可执行文件时添加了`libsqlite3.a`文件。

在我的机器上，我仍然遇到构建失败:

```
Linking CXX executable cpp_demo
FAILED: cpp_demo
/bin/c++
  main.cpp.o
  -o cpp_demo
  libchucknorris.a
  /path/to/sqlite-<version>/libsqlite3.a

libsqlite3.a(sqlite3.o): In function `pthreadMutexAlloc':
sqlite3.c:(.text+0x4275): undefined reference to `pthread_mutexattr_init'
...
libsqlite3.a(sqlite3.o): In function `unixDlOpen':
sqlite3.c:(.text+0x10bd8): undefined reference to `dlopen' 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么回事？

结果是，至少在 Linux 上，sqlite3 依赖于另外两个库，即`pthread`和`dl`。(这很常见，我可以从错误消息中缺少的符号的名称中猜出库的名称)

我们可以通过告诉 CMake 从`sqlite3`到`pthread`和`dl` :
的依赖关系来修复我们的编译失败

```
if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
  set_target_properties(sqlite3
    PROPERTIES
      INTERFACE_LINK_LIBRARIES "dl;pthread"
   )
endif() 
```

Enter fullscreen mode Exit fullscreen mode

```
$ ninja -v
/bin/c++
  main.cpp.o
  -o cpp_demo
  libchucknorris.a
  /path/to/sqlite-<version>/lib/libsqlite3.a
  -ldl
  -lpthread 
```

Enter fullscreen mode Exit fullscreen mode

这不是小事，我们必须对 sqlite3 源的位置进行硬编码。

现在让我们看看柯南如何帮助我们更容易地使用 sqlite3。

# 安装柯南

柯南是用 Python 写的，可以用`pip` :
安装

```
$ python3 -m pip install conan --user 
```

Enter fullscreen mode Exit fullscreen mode

然后，您应该在您的`PATH`中添加安装了`conan`二进制文件的目录

*   Linux: `export PATH="${HOME}/.local/bin:${PATH}"`
*   苹果电脑:`export PATH="${HOME}/Library/Python3.x/bin:${PATH}"`
*   Windows:无事可做；-)

柯南让你编写*食谱*并上传*二进制包*。

菜谱是用于构建包的一段 Python 代码；包只是一个包含预编译的二进制文件的档案。

在柯南世界中，任何用户都可以发布任何库的食谱和二进制包。(许多包经理对包名采用“先到先得”的方式，但柯南不是这样)。

和许多其他的包管理器一样，柯南可以在多个叫做 *remotes* 的地方寻找包:你所需要的就是在某个地方托管一个[柯南服务器](http://docs.conan.io/en/latest/uploading_packages/running_your_server.html)。

默认情况下，柯南带有预配置的`conan-center`遥控器。

# 创建一个 conanfile.txt

柯南使用一种简单的配置文件格式，在这里你可以指定依赖关系和一些“生成器”:

```
[requires]
sqlite3/3.21.0@bincrafters/stable

[generators]
cmake 
```

Enter fullscreen mode Exit fullscreen mode

字符串`sqlite3/3.21@bincrafters/stable`被称为*参考*。

有名称:`sqlite3`，版本:`3.21`，用户:`bincrafters`，频道:`stable`。

您可以将通道视为分支:`stable`通道表示配方和二进制包至少通过了最低限度的质量保证。(这适用于来自稳定通道内`conan-center`遥控器的任何包)。

`cmake`生成器告诉 conan 生成一些文件，这些文件包含关于 CMake 可用的依赖项的信息。

下面是调用柯南的方法:

```
$ cd build/default
$ conan install ../..
sqlite3/3.21.0@bincrafters/stable: Not found in local cache, looking in remotes...
sqlite3/3.21.0@bincrafters/stable: Trying with 'conan-center'...
...
PROJECT: Installing /path/to/conanfile.txt
Requirements
    sqlite3/3.21.0@bincrafters/stable from 'conan-center'
Packages
    sqlite3/3.21.0@bincrafters/stable:6ae331b72e7e265ca2a3d1d8246faf73aa030238
PROJECT: Retrieving package 6ae331b72e7e265ca2a3d1d8246faf73aa030238
...
PROJECT: Generator cmake created conanbuildinfo.cmake 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了几件事:

*   首先柯南在本地缓存中寻找这个包，没有找到。

*   它使用了一个名为`conan-center`的*遥控器*，并在那里找到了一个具有特定 ID ( `6ae331b72e7e265ca2a3d1d8246faf73aa030238`)的兼容包。

*   然后，它从远程下载二进制包并将其存储在缓存中。

*   最后，它在构建文件夹中生成了一个名为`conanbuildinfo.cmake`的文件。

# 改编 CMakeLists.txt

然后，我们可以使用`include()` CMake 函数和指向构建文件夹
的预定义 CMake 变量`CMAKE_BINARY_DIR`,恢复我们在`CMakeLists.txt`文件中的更改，并包含 conan 为我们生成的文件

```
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们调用`conan_basic_setup()`方法，并使用`CONAN_PKG::`前缀:
引用柯南定义的目标

```
project(ChuckNorris)
set(CMAKE_CXX_STANDARD 11)

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)

# ...

target_link_libraries(chucknorris CONAN_PKG::sqlite3) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以调用`cmake`和`ninja`，就像我们在前一部分的结尾所做的一样:

```
$ cmake -GNinja ../..
...
-- Conan: Using cmake targets configuration
-- Library sqlite3 found /home/dmerej/.conan/data/.../lib/libsqlite3.a
...
$ ninja
[1/3] Building CXX object CMakeFiles/chucknorris.dir/src/ChuckNorris.cpp.o
[2/3] Linking CXX static library lib/libchucknorris.a
[3/3] Linking CXX executable bin/cpp_demo
$ ./bin/cpp_demo
The easiest way to determine Chuck Norris's age is to cut him
in half and count the rings.
$ ./bin/cpp_demo
If, by some incredible space-time paradox, Chuck Norris would ever fight
himself, he'd win. Period. 
```

Enter fullscreen mode Exit fullscreen mode

好像管用！

在接下来的文章中，我们将[在我们的 C++ API](https://dmerej.info/blog/post/chuck-norris-part-3-a-c-wrapper/) 之上编写一个 C 包装器，并使用它在 Python 中实现一个`chucknorris`模块。

一会儿见:)

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  `-isystem`旗的工作方式几乎和`-I`旗一样。您可以在 gcc 手册页中找到所有关于血淋淋的细节。 [↩](#fnref1)