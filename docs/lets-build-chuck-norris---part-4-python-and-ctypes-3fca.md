# 让我们建造查克·诺里斯！-第 4 部分:Python 和 ctypes

> 原文：<https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-4-python-and-ctypes-3fca>

*最初发表于[我的博客](https://dmerej.info/blog/post/chuck-norris-part-4-python-ctypes/)。*

*注意:这是[的第 4 部分，让我们打造 Chuck Norris！](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)系列。*

# 静态和共享库

C 和 C++库有两种形式:静态的和共享的。

在这两种情况下，库都是名称(符号)和可执行代码的集合。静态库和共享库的区别在于它们是如何被使用的，例如被一个可执行程序使用。

静态库中的代码被直接集成到程序中:编译器将从静态库中复制程序使用的代码，并使其成为程序的一部分。

另一方面，共享库中的代码只被程序引用。当程序启动时，操作系统将尝试在共享库文件中找到要运行的代码。

我们说静态库中的代码在编译时使用*，而共享库的代码在运行时使用*

 **为此，静态库也被称为*档案库*，共享库也被称为*动态库*。

根据平台的不同，静态库和共享库通常有不同的扩展:

| 平台 | 静电 | 共享的 |
| --- | --- | --- |
| Linux 操作系统 | 。a | 。因此 |
| 马科斯 | 。a | . dylib |
| Windows 操作系统 | 。解放运动 | 。dll<sup id="fnref1">T1】1T3】</sup> |

那么，用 Python 写绑定需要哪种类型的库呢？

*注意:*从现在开始，一切都在 **Linux** 机器上进行。使用 macOS 或 Windows 时会有一些差异，但为了简单起见，我在这里只处理一个平台。我们也将只使用 **Python3** 。同样，在 Python2 中，事情有点不同，但是让我们保持简单。

由于我们使用 Python 作为一个*程序*(`/usr/bin/python3`二进制)，在编译时做任何事情显然都为时已晚。所以让我们建立一个共享库，看看我们在运行时能做什么，好吗？

# 构建共享库

当我们在前面的 CMakeList.txt 中描述 chucknorris 库时，我们没有指定它的类型。

本例中使用的库的类型由一个名为`BUILD_SHARED_LIBS`的变量控制，默认情况下这个变量是`OFF`。所以，让我们重新运行 CMake，将这个变量设置为`ON`。

```
$ cd build/default 
$ cmake -GNinja -DBUILD\_SHARED\_LIBS=ON ../.. 
$ ninja 
... 
-- Library sqlite3 found /.conan/data/sqlite3/3.21.0/bincrafters/...libsqlite3.a 
... 
[1/7] Building C object CMakeFiles/c\_demo.dir/src/main.c.o 
[2/7] Building CXX object CMakeFiles/chucknorris.dir/src/c\_wrapper.cpp.o 
[3/7] Building CXX object CMakeFiles/cpp\_demo.dir/src/main.cpp.o 
[4/7] Building CXX object CMakeFiles/chucknorris.dir/src/ChuckNorris.cpp.o 
[5/7] Linking CXX shared library lib/libchucknorris.so 
FAILED: lib/libchucknorris.so 
: && /bin/c++ ... 
  -o lib/libchucknorris.so 
  ChuckNorris.cpp.o 
  c_wrapper.cpp.o 
  ... 
  libsqlite3.a 
  ... 
/bin/ld: libsqlite3.a(sqlite3.o): relocation R_X86_64_PC32 against symbol
`sqlite3_version` can not be used when making a shared object; 
recompile with -fPIC

```

链接失败。

这里发生的事情是，我们试图将一个静态库(`libsqlite3.a`)合并到一个共享库中。大多数时候这很好，但是在 Linux 上不行。

编译器告诉我们需要做什么:我们必须用`-fPIC`重新编译`libsqlite3.a`。

以下是`man gcc`对`fpic`的评价:

```
-fpic 
  Generate position-independent code (PIC) suitable for use in a shared library. 
```

Enter fullscreen mode Exit fullscreen mode

很公平，让我们通过生成独立于位置的代码来尝试重建`sqlite3`。

# 修补一个柯南配方

第一步是看我们自己能否重建 sqlite3。

我们将使用不同的用户名和频道。( *@dmerej/test* 而不是 *@bincrafters/stable* )。正如我们之前解释的，柯南是去中心化的，所以为了满足你的要求而复制和修改别人的食谱是可能的，甚至是被鼓励的。

让我们从遥控器上拿食谱:

```
$ conan copy sqlite3/3.21.0@bincrafters/stable dmerej/test 
Downloading conan_sources.tgz [=====================================] 706B/706B 
Copied sqlite3/3.21.0@bincrafters/stable to sqlite3/3.21.0@dmerej/test 
Copied sources sqlite3/3.21.0@bincrafters/stable to sqlite3/3.21.0@dmerej/test

```

在这里，柯南在遥控器中寻找配方，并创建了一个不同名称的副本，但仍在柯南缓存中。

然后，我们从缓存中复制源代码，并将它们放在 C++代码旁边的`conan/sqlite3`文件夹中:

```
$ cd ChuckNorris/cpp 
$ mkdir -p conan/sqlite3 $ cd conan/sqlite3 
$ cp -rv ~/.conan/data/sqlite3/3.21.0/bincrafters/stable/export/* .
'../conanfile.py' -> './conanfile.py' 
'../conanmanifest.txt' -> './conanmanifest.txt' 
'../LICENSE.md' -> './LICENSE.md'

```

让我们试着自己构建这个包:

```
$ conan create . dmerej/test 
sqlite3/3.21.0@dmerej/test: Exporting package recipe 
sqlite3/3.21.0@dmerej/test: A new conanfile.py version was exported
...
sqlite3/3.21.0@dmerej/test: Installing package .. 
sqlite3/3.21.0@dmerej/test: Attempting download of sources from:
https://www.sqlite.org/2017/sqlite-amalgamation-3210000.zip ...
sqlite3/3.21.0@dmerej/test: Calling build() 
CMake Error: The source directory "..." does not appear to contain CMakeLists.txt.

```

原来我们还需要从`export_source`文件夹中复制一些文件:

```
$ cp -rv ~/.conan/data/sqlite3/3.21.0/bincrafters/stable/export_source/\* . 
'../CMakeLists.txt -> './CMakeLists.txt' 
'../FindSQLite3.cmake' -> './FindSQLite3.cmake'

```

现在我们可以建造:

```
$ conan create . dmerej/test 
sqlite3/3.21.0@dmerej/test: Exporting package recipe 
sqlite3/3.21.0@dmerej/test: A new conanfile.py version was exported
... 
sqlite3/3.21.0@dmerej/test: Installing package .. 
sqlite3/3.21.0@dmerej/test: Attempting download of sources from:
https://www.sqlite.org/2017/sqlite-amalgamation-3210000.zip ...
sqlite3/3.21.0@dmerej/test: Calling build() ... 
[1/2] Building C object CMakeFiles/sqlite3.dir/sources/sqlite3.o 
[2/2] Linking C static library lib/libsqlite3.a ... 
sqlite3/3.21.0@dmerej/test: Package '6ae331b72e7e265ca2a3d1d8246faf73aa030238' built
... 
sqlite3/3.21.0@dmerej/test: Calling package() 
sqlite3/3.21.0@dmerej/test package(): Copied 1 '.cmake' files: FindSQLite3.cmake 
sqlite3/3.21.0@dmerej/test package(): Copied 2 '.h' files: sqlite3.h, sqlite3ext.h 
sqlite3/3.21.0@dmerej/test package(): Copied 1 '.a' files: libsqlite3.a ... 
sqlite3/3.21.0@dmerej/test: Package '6ae331b72e7e265ca2a3d1d8246faf73aa030238' created

```

让我们总结一下发生的事情:

*   柯南从`sqlite.org`获取资料
*   它使用我们从`export_source`复制的`CMakeLists.txt`调用`CMake`
*   它使用名为`build()`的函数构建了 CMake 项目
*   它使用一个名为`package()`的函数复制了一些文件。

这大致就是我们之前手工构建 sqlite 时所做的。

让我们仔细看看柯南源文件:

*CMakeList.txt* :

```
project(cmake_wrapper)

include(conanbuildinfo.cmake)
conan_basic_setup()

add_library(sqlite3 sources/sqlite3.c) 
```

Enter fullscreen mode Exit fullscreen mode

*conanfile.py* :

```
class ConanSqlite3(ConanFile):
    name = "sqlite3"
    version = "3.21.0"
    settings = "os", "compiler", "arch", "build_type"
    options = {"shared": [True, False]}
    ...

    def source(self):
        base_url = "https://www.sqlite.org/" + self.year
        ...
        download_url = "{0}/{1}.{2}".format(base_url, archive_name, archive_ext)
        tools.get(download_url)

    def build(self):
        cmake = CMake(self)
        ...
        cmake.configure()
        cmake.build()

    def package(self):
        self.copy(...) 
```

Enter fullscreen mode Exit fullscreen mode

柯南食谱只是一个从`ConanFile`类派生的 Python 类。

它包含`source()`、`build()`和`package()`方法，用于获取源代码、构建包和复制我们在前面的控制台输出中看到的相关文件。

它使用一个知道如何运行`cmake`的`CMake`类。

最后，该类包含一些属性。`name`和`version`不言而喻，但是我们不得不说一下`settings`和`options`。

在这两种情况下，设置和选项都是包的使用者可以设置的变量。设置不能有默认值，如果设置改变，必须生成不同的包。这就是为什么编译器(想想`gcc`对`Visual Studio`)是一个设置。设置是全局设置的，通常在一个配置文件中，适用于所有的配方。选项是不同的:它们可以有默认值，可以逐个包地设置，并且它们有一个预定义的可能值列表。

我们可以看到`conanfile.py`已经定义了一个`shared`选项，该选项可以是真或假。我们真的不想要一个共享的`sqlite3`库，我们想要一个静态的`sqlite3`库，但是用独立于位置的代码构建。

有几种方法可以做到这一点。其中一个是引入一个叫做`pic`的新选项。

CMake 知道如何将“位置无关代码”的抽象概念转换成具体的编译器标志，例如 gcc 的`-fPIC`，所以我们只需设置正确的 CMake 变量:

```
class ConanSqlite3(ConanFile):

    settings = "os", "compiler", "arch", "build_type"
    options = {
      "shared": [True, False],
      "pic": [True, False],
    }

    def build(self):
        cmake = CMake(self)
        if self.options.shared:
            cmake.definitions["BUILD_SHARED_LIBS"] = "ON"
        if self.options.pic:
            cmake.definitions["CMAKE_POSITION_INDEPENDENT_CODE"] = "ON"
        ... 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们可以重新创建`sqlite3`包:

```
$ conan create --option 'pic=True' . dmerej/test

```

最后，我们可以更改`cpp/ChuckNorris`中的`conanfile.txt`来引用我们新构建的包:

*conanfile.txt* :

```
[requires]
sqlite3/3.21.0@dmerej/test

```

# 使用 chucknorris 共享库

让我们重新运行`conan install`，再次使用`--option`命令行标志，看看我们是否能设法按照我们想要的方式构建 chucknorris。

请注意，我们在`pic=True`选项前面加上了我们想要应用该选项的包的名称。如果我们不这样做，`conan`会尝试在每个包的*上设置选项。*

```
$ cd build/default 
$ conan install ../.. --option 'sqlite3:pic=True' 
$ cmake -GNinja -DBUILD_SHARED_LIBS=ON ../.. 
$ ninja 
[1/7] Building C object CMakeFiles/c_demo.dir/src/main.c.o 
[2/7] Building CXX object CMakeFiles/chucknorris.dir/src/c_wrapper.cpp.o 
[3/7] Building CXX object CMakeFiles/cpp_demo.dir/src/main.cpp.o 
[4/7] Building CXX object CMakeFiles/chucknorris.dir/src/ChuckNorris.cpp.o 
[5/7] Linking CXX shared library lib/libchucknorris.so 
[6/7] Linking C executable bin/c_demo 
[7/7] Linking CXX executable bin/cpp_demo

```

成功！

我们之前说过，是操作系统负责在运行时从共享库中加载代码。在 Linux 上，这是由一个叫做`ld-linux.so`的特殊共享库来完成的。

因此，当我们运行`cpp_demo`可执行文件时，我们可以检查`libchucknorris.so`文件是否被加载，方法是让`ld.so`输出关于它加载的文件的调试信息 <sup id="fnref2">[2](#fn2)</sup> :

```
$ cd build/default 
$ export LD_TRACE_LOADED_OBJECTS=1 
$ ./bin/cpp_demo 
./bin/cpp_demo 
linux-vdso.so.1 (0x00007ffe07395000)
libchucknorris.so => .../build/default/lib/libchucknorris.so (0x00007f5f4c43e000)
... 
libpthread.so.0 => /usr/lib/libpthread.so.0 (0x00007f5f4c220000) 
libdl.so.2 => /usr/lib/libdl.so.2 (0x00007f5f4c01c000) 
... 
Chuck Norris knows Victoria's secret

```

当我们手动链接到`sqlite3`时，我们的朋友`libpthread.so`和`libdl.so`也参与其中，我们可以在我们的构建文件夹中看到 ChuckNorris 库的完整路径。

# 使用 ctypes

Python 标准库包含一个名为`ctypes`的模块，它允许做`ld.so`做的事情，但是使用 Python 代码。

文档说我们可以使用`ctypes.cdll.LoadLibrary`从`.so`获得一个“句柄”,然后简单地通过调用句柄上正确名称的方法来使用共享库中的符号。

让我们试试:

```
handle = ctypes.cdll.LoadLibrary("build/default/lib/libchucknorris.so")
ck = handle.chuck_norris_init()
fact = handle.chuck_norris_get_fact(ck)
print(fact) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ python ck.py 
zsh: segmentation fault (core dumped) python ck.py

```

呜呜:/

实际上，要做到这一点，我们必须为我们调用的每个方法指定参数和返回值的类型，就像这样:

```
handle = ctypes.cdll.LoadLibrary("build/default/lib/libchucknorris.so")
handle.chuck_norris_init.restype = ctypes.c_void_p
handle.chuck_norris_get_fact.restype = ctypes.c_char_p
handle.chuck_norris_get_fact.argtypes = [ctypes.c_void_p]
ck = handle.chuck_norris_init()
fact = handle.chuck_norris_get_fact(ck)
print(fact) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ python ck.py 
b'When Chuck Norris enters a rodeo the bull has to try and last 8 seconds.'

```

差不多了:我们仍然需要去掉前缀。

`ctypes`无法真正假设 C 代码中的一个`char *`包含文本，所以`c_char_p`类型被翻译成了一个`bytes`对象，适合表示二进制数据。

假设我们很小心，只在 sqlite3 数据库中插入了有效的 UTF-8 编码的文本，我们可以在 Python 代码中调用`.decode(UTF-8)`，尽管:

```
fact_as_bytes = handle.chuck_norris_get_fact(ck)
fact_text = fact_as_bytes.decode("UTF-8")
print(fact_text) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ python ck.py 
When Chuck Norris enters a rodeo the bull has to try and last 8 seconds.

```

我们完成了:)

这一切都很好，但是有一个更健壮的方法来编写我们的 Python 绑定。你可以在[第五部分](https://dmerej.info/blog/post/chuck-norris-part-5-python-cffi/)中了解更多。

* * *

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  实际上，在 Windows 上，当你想链接一个共享库时，你既可以在链接时使用一个`.lib`，也可以在运行时使用一个`.dll`。我知道这很令人困惑。 [↩](#fnref1)

2.  这是`ldd`顺便用的手法。 [↩](#fnref2)**