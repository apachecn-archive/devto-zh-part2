# 让我们建造查克·诺里斯！-第 5 部分:Python 和 cffi

> 原文：<https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-5-python-and-cffi-54di>

*最初发表于[我的博客](https://dmerej.info/blog/post/chuck-norris-part-5-python-cffi/)。*

*注意:这是[让我们打造查克·诺里斯的第 5 部分！](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)系列。*

[上周](https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-4-python-and-ctypes-3fca)我们使用`ctypes`为 chucknorris 库编写了 Python 绑定。

我们设法从 Python 程序中获得了一些 Chuck Norris 事实。

从好的方面来说，我们不需要编译任何东西。一切都是直接用 Python 完成的。

不过，有几个问题:

*   我们必须将`libchucknorris.so`共享库的路径传递给`ctypes.cdll.LoadLibrary`。
*   我们必须在 Python 程序中复制 C 函数的参数类型信息，这很容易出错。

用 Python 包装 C 代码的另一种方法是使用一个 *C 扩展*:即一个用 C 编写的 Python 模块。在这种情况下，Python 模块实际上采用了共享库的形式，因此在运行时由`python`解释器加载。

创建 Python C 扩展有很多方法，从直接编写 C 代码(使用 [Python C API](https://docs.python.org/3/c-api/index.html) )，到生成 C 代码然后编译它。

在过去，我曾经使用过像 [boost::python](https://www.boost.org/doc/libs/1_66_0/libs/python/doc/html/index.html) 和 [swig](http://www.swig.org/) 这样的工具来完成这个任务。

我最近才开始使用`cffi`，但我发现它更容易使用，而且，与上面的工具相反，它与 [pypy](https://pypy.org/) 兼容，这有点太棒了。

# 先用 cffi 试试

如果你浏览[文档](https://cffi.readthedocs.io/en/latest/index.html)，你会看到`cffi`可以用于几种*模式*。有一个 *ABI 模式*和一个 *API 模式*。ABI 模式类似于我们对`ctypes`使用的技术，因为它涉及到将`chucknorris`作为共享库加载。

我们将改为使用 *API* 模式，其中所有代码都是使用 chucknorris.h 头文件生成的*，这将出错的可能性降至最低。*

这意味着我们可以重新构建静态库`chucknorris`。这样我们就不必关心库的位置，chucknorris 代码将在编译时使用。(抱歉走了点弯路)。

我们要做的就是重新运行 cmake 和 ninja:

```
$ cd cpp/ChuckNorris/build/default 
$ cmake -DBUILD_SHARED_LIBS=OFF ../.. 
$ ninja 
[1/7] Building C object CMakeFiles/c_demo.dir/src/main.c.o 
[2/7] Building CXX object CMakeFiles/chucknorris.dir/src/c_wrapper.cpp.o 
[3/7] Building CXX object CMakeFiles/cpp_demo.dir/src/main.cpp.o 
[4/7] Building CXX object CMakeFiles/chucknorris.dir/src/ChuckNorris.cpp.o 
[5/7] Linking CXX static library lib/libchucknorris.a 
[6/7] Linking CXX executable bin/c_demo 
[7/7] Linking CXX executable bin/cpp_demo

```

现在，让我们使用 cffi builder 为我们的 C 扩展编写一个 Python 构建脚本:

*build _ chuck Norris . py*:

```
from cffi import FFI
ffibuilder = FFI()

ffibuilder.set_source(
    "_chucknorris",
    """
    #include <chucknorris.h>
    """,
)

ffibuilder.cdef("""
typedef struct chuck_norris chuck_norris_t;
chuck_norris_t* chuck_norris_init(void);
const char* chuck_norris_get_fact(chuck_norris_t*);
void chuck_norris_deinit(chuck_norris_t*);
""") 
```

Enter fullscreen mode Exit fullscreen mode

*   我们实例化了一个名为`ffibuilder`的 FFI 对象。
*   在`ffibuilder.set_source()`中，我们给构建器起了 C 扩展的名字:`_chucknorris`。C 扩展名通常以下划线为前缀。
*   我们还给 FFI 构建器提供了编译它生成的代码所需的 C 代码。(这里我们只需要包含`<chucknorris.h>`头文件，但是在一个真实的项目中，你可能会添加宏或者额外的帮助代码)。
*   最后，我们列出了我们希望在 C 扩展中作为 C 声明公开的函数和类型——直接从`chucknorris.h`头复制/粘贴——并将它们作为字符串传递给`ffibuilder.cdef()`。

# 保持事物干燥

> “但是等一下！”，*我听到你说*。“您说 cffi 比 ctypes 好，因为我们不必复制有关类型的信息，但现在您告诉我们，我们仍然需要在对。cdef()！什么给了？”。

嗯，的确，当我们写代码时，我们通常试图保持事情*干燥*(干燥的意思是“不要重复自己”)。

然而，当使用 cffi 时，这并不重要。只有当复制的代码*不同时*改变并且不同步时，不遵循 DRY 才是危险的。

假设你破坏了你的库的 API(例如通过改变一个 C 函数的参数数量)。如果您没有反映出`ffibuilder.def()`中的变化，您将得到一个不错的编译错误，而不是像我们在`ctypes`中经历的崩溃或 segfault。

# 添加 setup.py

解决了这个问题之后，让我们添加一个在开发绑定、安装代码并重新分发给其他人时可以使用的`setup.py`文件:

*setup . py*:T2

```
from setuptools import setup, find_packages

setup(name="chucknorris",
      version="0.1",
      description="chucknorris python bindings",
      author="Dimitri Merejkowsky",
      py_modules=["chucknorris"],
      setup_requires=["cffi"],
      cffi_modules=["build_chucknorris.py:ffibuilder"],
      install_requires=["cffi"],
) 
```

Enter fullscreen mode Exit fullscreen mode

这些参数的作用如下:

*   `py_modules`:要安装的 Python 模块列表。我们只有一个名为`chucknorris`的，它将使用`_chucknorris` C 扩展并公开一个更“Pythonic 化”的 API。
*   `setup_requires`:为了构建扩展，`setup.py`脚本需要什么。
*   `cffi_modules`:构建扩展时要调用的 Python 对象列表。这里是在`build_chucknorris.py`文件中定义的`ffibuilder`对象。
*   `install_requires`:我们的模块*一旦构建并安装*后的依赖列表。我们*在运行时也*需要`cffi`，不仅仅是为了编译扩展。

最后，我们可以编写 chucknorris Python 模块的实现:

*chucknorris.py:*

```
from _chucknorris import lib, ffi

class ChuckNorris:
    def __init__ (self):
        self._ck = lib.chuck_norris_init()

    def get_fact(self):
        c_fact = lib.chuck_norris_get_fact(self._ck)
        fact_as_bytes = ffi.string(c_fact)
        return fact_as_bytes.decode("UTF-8")

    def __del__ (self):
        lib.chuck_norris_deinit(self.c_ck)

def main():
    chuck_norris = ChuckNorris()
    print(chuck_norris.get_fact())

if __name__ == " __main__":
    main() 
```

Enter fullscreen mode Exit fullscreen mode

*   我们从从`_chucknorris` C 扩展导入代码开始。`lib`包含已经包装的内容——用`ffibuilder.cdef()`声明——`ffi`包含各种 cffi 助手。
*   我们将`lib.chuck_norris_init()`和`lib.chuck_norris_deinit()`隐藏在`__init__`和`__del__`方法下。(正如我们从 C++构造函数和析构函数到 C 函数所做的一样，但是反过来)
*   在`get_fact()`方法中，我们称之为`lib.chuck_norris_get_fact()`。`chuck_norris_get_fact()`返回一个“C 字符串”，它只是一个以`\0`结尾的`char*`数组。我们将它传递给`ffi.string()`以获得一个`bytes`对象，适合保存这种数据。最后，我们使用`decode()`将字节列表转换成一个真正的字符串。
*   最后，当调用`chucknorris.py`脚本时，我们使用我们漂亮的 Python 类，就好像从来没有 C 代码一样:)

# 运行构建器

在安装了`cffi`包之后，我们终于可以试着编译代码了:

```
$ python setup.py build_ext 
running build_ext 
generating ./_chucknorris.c 
...
building '_chucknorris' extension 
gcc ... 
  -fPIC 
  ... 
  -I/usr/include/python3.6m 
  -c _chucknorris.c 
  -o ./_chucknorris.o 
_chucknorris.c:493:14: fatal error: chucknorris.h: No such file or directory 

```

发生了什么事？

*   `python setup.py build_ext`了解如何使用我们的`ffibuilder`对象。
*   它在一个`_chucknorris.c`文件中生成了一些 C 代码
*   它开始使用`_chucknorris.c`和我们在`ffibuilder.set_source()`和`ffibuilder.cdef()`中传递的代码构建`_chucknorris`扩展。
*   `ffibuilder.compile()`方法知道我们的老朋友`-fPIC`，以及 Python includes ( `-I/usr/include/python3.6m`)的路径，但是它找不到`chucknorris.h`头，编译失败。

# 调整 ffibuilder

显然,`ffibuilder`需要知道 chucknorris 库和 chucknorris 包含路径。

我们可以使用`extra_objects`和`include_dirs`参数 <sup id="fnref1">[1](#fn1)</sup> 将它们直接传递给`set_source()`方法。

*build _ chuck Norris . py*:

```
import path

cpp_path = path.Path("../cpp/ChuckNorris").abspath()
cpp_build_path = cpp_path.joinpath("build/default")
ck_lib_path = cpp_build_path.joinpath("lib/libchucknorris.a")
ck_include_path = cpp_path.joinpath("include")

ffibuilder.set_source(
    "_chucknorris",
    """
    #include <chucknorris.h>

    """,
    extra_objects=[ck_lib_path],
    include_dirs=[ck_include_path],
)
... 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们使用奇妙的 [path.py](https://github.com/jaraco/path.py) 库来处理路径操作，我们可以将它添加到我们的`setup.py`文件:

```
from setuptools import setup

setup(name="chucknorris",
      version="0.1",
      ...
      setup_requires=["cffi", "path.py"],
      ...
) 
```

Enter fullscreen mode Exit fullscreen mode

# 缺失的符号

让我们再次尝试构建我们的扩展:

```
$ python setup.py build_ext 
running build_ext 
generating ./_chucknorris.c 
... 
building '_chucknorris' extension 
gcc 
  ... 
  -fPIC 
  ... 
  -I/usr/include/python3.6m 
  -c _chucknorris.c 
  -o ./_chucknorris.o 
gcc 
  ... 
  -shared 
  ... 
  -o build/lib.linux-x86_64-3.6/_chucknorris.abi3.so

```

好了，这个管用。

现在让我们运行`python setup.py develop`，这样我们可以直接导入 C 扩展:

```
$ python setup.py develop 
... 
generating cffi module 'build/temp.linux-x86_64-3.6/_chucknorris.c' 
already up-to-date 
... 
copying build/lib.linux-x86_64-3.6/_chucknorris.abi3.so -> ...

```

请注意，`setup.py develop`负责为我们构建扩展，甚至能够在不需要重新构建时完全跳过编译。

现在让我们运行`chucknorris.py`文件:

```
$ python chucknorris.py 
Traceback (most recent call last): File "chucknorris.py", line 1, in 
    from _chucknorris import lib, ffi
ImportError: .../_chucknorris.abi3.so: undefined symbol: _ZNSt8ios_base4InitD1Ev

```

该死的！

这就是共享库的问题。很高兴让你建立一个共享库，即使有些符号没有在任何地方定义。它只是假设缺失的符号会在加载库之前的某个时候被提供。

因此，确保正确构建共享库的唯一方法是*从可执行文件*中加载它。 <sup id="fnref2">[2](#fn2)</sup>

我们再次面临从符号名中猜测库的任务。由于它看起来像一个损坏的 C++符号，我们可以使用`c++filt`来获得一个更容易阅读的名称:

```
$ c++filt _ZNSt8ios_base4InitD1Ev 
std::ios_base::Init::~Init

```

这里我碰巧知道这是一个来自 c++ *运行时库*的符号，这个库包含了类似于`std::string`的实现。

我们可以通过将`c++`库的名称直接作为`libraries`参数传递来解决这个问题:

```
ffibuilder.set_source(
    "_chucknorris",
    """
    #include <chucknorris.h>

    """,
    extra_objects=[ck_lib_path],
    include_dirs=[ck_include_path],
    libraries=["stdc++"], 
```

Enter fullscreen mode Exit fullscreen mode

注意:我们也可以将参数`language`设置为`c++`，并在链接`_chucknorris.so`时调用 C++链接器，因为 C++链接器知道 c++运行时库在哪里。 <sup id="fnref3">[3](#fn3)</sup>

让我们再试一次:

```
$ python setup.py develop 
$ python chucknorris.py 
ImportError: .../_chucknorris.abi3.so: undefined symbol: sqlite3_close

```

这个更简单:`chucknorris`依赖于`libsqlite3`，所以我们也要和`sqlite3`链接。

在 CMakeLists.txt 中，我们在[第二部分](https://dmerej.info/blog/post/chuck-norris-part-2-sqlite-conan/)中写道，当我们构建`cpp_demo`可执行文件时，我们只是调用了`target_link_libraries(cpp_demo chucknorris)`。CMake 知道从`chucknorris`目标到 sqlite3 库的依赖关系，一切都很好。

但是我们在这里没有使用 CMake / conan 构建系统，我们使用的是 Python 构建系统。怎样才能让他们合作？

# JSON 生成器

自从柯南 1.2.0 以来，有一个名为`json` <sup id="fnref4">[4](#fn4)</sup> 的生成器，我们可以使用它来获取关于依赖关系的机器可读信息。

下面是我们如何在 ffibuilder 中使用这个 json 文件。

首先，让我们将`json`添加到柯南生成器列表中:

*conanfile.txt* :

```
[requires]
sqlite3/3.21.0@dmerej/test

...

[generators]
cmake
json

```

然后，让我们重新运行`conan install`:

```
$ cd cpp/python/build/default 
$ conan install ../.. 
... 
PROJECT: Installing /home/dmerej/src/chucknorris/cpp/ChuckNorris/conanfile.txt 
... 
sqlite3/3.21.0@dmerej/test: Already installed! 
PROJECT: Generator cmake created conanbuildinfo.cmake 
PROJECT: Generator json created conanbuildinfo.json
...

```

这会生成如下所示的`conanbuildinfo.json`文件:

*build/default/conanbuildinfo . JSON*:

```
{  "dependencies":  [  {  "version":  "3.21.0",  "name":  "sqlite3",  "libs":  [  "sqlite3",  "pthread",  "dl"  ],  "include_paths":  [  "/.../.conan/data/sqlite3/.../<id>/include"  ],  "lib_paths":  [  "/.../.conan/data/sqlite3/.../<id>/lib"  ],  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以解析 json 文件，传递库并包含到`ffibuilder.set_source()`函数的路径:

*build _ chuck Norris . py*:

```
cpp_path = path.Path("../cpp/ChuckNorris").abspath()
cpp_build_path = cpp_path.joinpath("build/default")

extra_objects = []
libchucknorris_path = cpp_build_path.joinpath("lib/libchucknorris.a")
extra_objects.append(libchucknorris_path)

include_dirs = []
include_dirs.append(cpp_path.joinpath("include"))

libraries = ["stdc++"]

conan_info = json.loads(cpp_build_path.joinpath("conanbuildinfo.json").text())
for dep in conan_info["dependencies"]:
    for lib_name in dep["libs"]:
        lib_filename = "lib%s.a" % lib_name
        for lib_path in dep["lib_paths"]:
            candidate = path.Path(lib_path).joinpath(lib_filename)
            if candidate.exists():
                extra_objects.append(candidate)
            else:
                libraries.append(lib_name)
    for include_path in dep["include_paths"]:
        include_dirs.append(include_path)

ffibuilder.set_source(
    "_chucknorris",
    """
    #include <chucknorris.h>

    """,
    extra_objects=extra_objects,
    include_dirs=include_dirs,
    libraries=libraries,
) 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都按预期运行:

```
$ python3 setup.py clean develop
$ python chucknorris.py
There are no weapons of mass destruction in Iraq, Chuck Norris lives in Oklahoma.

```

我们甚至可以构建一个预编译的轮子，其他人可以使用它，而无需自己编译 chucknorris 项目:

*在开发机上:*

```
$ python setup.py bdist_wheel 
… 
running build_ext 
… 
building ‘_chucknorris’ extension 
… 
creating ‘dist/chucknorris-0.1-cp36-cp36m-linux_x86_64.whl’ and adding ‘.’ to it …

```

*在另一台机器上*:

```
$ pip install chucknorris-0.1-cp36-cp36m-linux_x86\_64.whl 
$ python -c ‘import chucknorris; chucknorris.main()’

```

要做到这一点，其他用户需要在 Linux 上，有一个兼容的 C++库和相同版本的 Python，但是就 Linux 上二进制文件的发布而言，这不是很好吗？

关于预编译 Python 二进制模块的发布，有一整篇博客文章要写，但是现在关于 Python 已经足够了:)

下次再见，届时我们将使用我们在那里学到的一切，并开始将 Chuck Norris 移植到 Android 上。

* * *

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  `ffibuilder.set_source()`使用与 [distutils 扩展类](https://docs.python.org/3.6/distutils/apiref.html#distutils.core.Extension)相同的 API。 [↩](#fnref1)

2.  这也意味着您应该至少有一个可执行文件来测试您编写的每个共享库，但是您已经知道了，对吗？ [↩](#fnref2)

3.  不知道什么是最好的行动。如果你对此有意见，请告诉我。PS:我知道我们也可以使用静态链接，但是我把它留到我们在 Android 上构建 ChuckNorris 的部分。<sup>【返回】</sup> [↩](#fnref3)

4.  声明:json 生成器特性是由[your truly](https://github.com/conan-io/conan/pull/2515)添加的。 [↩](#fnref4)