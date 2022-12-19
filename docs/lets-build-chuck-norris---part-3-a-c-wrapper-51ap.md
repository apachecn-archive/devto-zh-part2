# 让我们建造查克·诺里斯！-第 3 部分:交流包装

> 原文：<https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-3-a-c-wrapper-51ap>

*最初发表于[我的博客](https://dmerej.info/blog/post/chuck-norris-part-3-a-c-wrapper/)。*

*注意:这是[的第 3 部分，让我们打造 Chuck Norris！](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)系列。*

# 简介:当语言在一起交谈

c 有点像编程语言的通用语。很多语言实现本身就是用 C 写的*，几乎都知道如何调用 C 代码。通常这被称为使用*外来函数接口*，简称 FFI。*

 *我们 chucknorris 项目的目标是在各种情况下使用我们的库(比如在 iOS 或 Android 应用程序中)，那么我们为什么不用 C 编写 Chuck Norris 库呢？

与 C 相比，C++有很多优势:

*   字符串更容易处理
*   内存管理更简单
*   你可以使用好的工具，比如类和模板
*   ...还有更多！

C 和 C++并没有太大的区别:例如，从 C++中调用 C 可以直接使用。`sqlite3`是用 C 写的，在我们的代码中，我们只需要包含`<sqlite3.h>`和一切“正常工作”的东西。 <sup id="fnref1">[1](#fn1)</sup>

但是当我们试着反过来时，事情变得更有趣了。

# 从 C 中调用 C++代码

在我们的库中，我们公开了一个类，但是 C 不知道类。所以我们要声明一个 C API，然后用 C++代码实现 C API。

我们可以这样做，因为 C++是 C 的“超集”。

不过还有一些细节需要弄清楚，所以让我们一步一步来。

## 声明一个 C API

c 不知道类，所以我们不能在任何地方使用`ChuckNorris`符号。

我们可以这样做:

*include/chuck Norris . h:*

```
typedef struct chuck_norris chuck_norris_t;
chuck_norris_t* chuck_norris_init(void);
const char* chuck_norris_get_fact(chuck_norris_t*);
void chuck_norris_deinit(chuck_norris_t*); 
```

Enter fullscreen mode Exit fullscreen mode

*   每个函数都有前缀`chuck_norris_`(因为 C 中没有名称空间)
*   我们声明了一个`chuck_norris_t` struct 类型，但是没有描述里面的内容。这样做是因为其他函数要么返回要么接受一个`chuck_norris_t` **指针**类型的参数，所以编译器不需要知道结构体内部有什么。这就是所谓的不透明指针。在 C++实现中，我们必须在“真实的”`ChuckNorris*`指针和不透明的`chuck_norris*`指针之间进行转换。
*   我们有明确的函数:`chuck_norris_init()`和`chuck_norris_deinit()`，而不是让编译器处理 C++类的创建和销毁。
*   代替类中的`getFact()`方法，我们有一个`chuck_norris_get_fact()`函数，它将不透明的`chuck_norris`指针作为第一个参数。

## 实现 C API

这是我们第一次尝试的样子:

*src/c _ wrapper . CPP:*T2】

```
#include <chucknorris.hpp> 
chuck_norris_t* chuck_norris_init()
{
  auto ck = new ChuckNorris();
  return reinterpret_cast<chuck_norris*>(ck);
}

const char* chuck_norris_get_fact(chuck_norris_t* chuck_norris)
{
  auto ck = reinterpret_cast<ChuckNorris*>(chuck_norris);
  std::string fact = ck->getFact();
  const char* result = fact.c_str();
  return result;
}

void chuck_norris_deinit(chuck_norris_t* chuck_norris)
{
  auto ck = reinterpret_cast<ChuckNorris*>(chuck_norris);
  delete ck;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们唯一可以使用的类型转换是`reinterpret_cast`，它基本上告诉编译器“相信我们，指针中的内容是正确的类型！”。这意味着如果 C API 的调用者不小心，事情会变得非常糟糕，但是不用担心，他们已经习惯了:P

为了检查它的工作情况，让我们添加另一个测试可执行文件，这次用 C 语言编写:

*src/main.c* :

```
#include <chucknorris.h>
#include <stdlib.h>
#include <stdio.h> 
int main()
{
  chuck_norris_t* ck = chuck_norris_init();
  const char* fact = chuck_norris_get_fact(ck);
  printf("%s\n", fact);
  chuck_norris_deinit(ck);
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们修改 CMake 代码:

*   将`c_wrapper.cpp`文件添加到`chucknorris`库的源代码列表中。
*   添加从`main.c`文件构建的`c_demo`可执行文件:

```
 add_library(chucknorris
    include/ChuckNorris.hpp
    include/chucknorris.h
    src/ChuckNorris.cpp
+   src/c_wrapper.cpp
  )

+ add_executable(c_demo
+   src/main.c
+ )
+
+ target_link_libraries(c_demo chucknorris) 
```

Enter fullscreen mode Exit fullscreen mode

让我们试着编译:

```
$ cd build/default
$ ninja
[1/7] Building C object CMakeFiles/c_demo.dir/src/main.c.o
[2/7] Building CXX object CMakeFiles/chucknorris.dir/src/c_wrapper.cpp.o
[3/7] Building CXX object CMakeFiles/cpp_demo.dir/src/main.cpp.o
[4/7] Building CXX object CMakeFiles/chucknorris.dir/src/ChuckNorris.cpp.o
[5/7] Linking CXX static library lib/libchucknorris.a
[6/7] Linking CXX executable bin/c_demo
FAILED: bin/c_demo
: && /bin/c++ main.c.o -o bin/c_demo lib/libchucknorris.a ...
CMakeFiles/c_demo.dir/src/main.c.o: In function `main':
main.c:(.text+0x9): undefined reference to `chuck_norris_init'
main.c:(.text+0x19): undefined reference to `chuck_norris_get_fact'
main.c:(.text+0x35): undefined reference to `chuck_norris_deinit'

```

我们可以看到`libchucknorris.a`库被传递给了链接器，那么为什么没有找到符号呢？

## 残缺不全的符号

为了理解，让我们使用一个叫做`nm`的工具来看看`libchucknorris.a`库中的符号名称:

```
$ nm --defined-only libchucknorris.a

ChuckNorris.cpp.o:
0000000000000000 V DW.ref.__gxx_personality_v0
...
000000000000020c T _ZN11ChuckNorris7getFactB5cxx11Ev

c_wrapper.o
...
0000000000000000 T _Z17chuck_norris_initv
00000000000000ac T _Z19chuck_norris_deinitP11ChuckNorris
000000000000003c T _Z21chuck_norris_get_factP11ChuckNorris

```

哼。符号的名称与我们在头文件中声明的名称不匹配。

这是因为它们被 C++编译器破坏了。我不会在这里详述为什么这些符号必须首先被破坏的原因。这么说吧，这和函数重载之类的东西有关。

我们可以检查一下`cpp_demo`二进制文件是否包含对奇怪的`getFact`符号的引用:

```
$ nm --defined-only cpp_demo
...
000000000000cefe T _ZN11ChuckNorris7getFactB5cxx11Ev

```

我们也可以在调用`nm`时使用`--demangle`选项，并查看原始名称:

(注意，C 符号也被破坏了)

```
$ nm --demangle --defined-only libchucknorris.a
...
00000000000001b0 T ChuckNorris::getFact[abi:cxx11]()
...
0000000000000000 T chuck_norris_init()
00000000000000ac T chuck_norris_deinit(ChuckNorris*)
000000000000003c T chuck_norris_get_fact(ChuckNorris*)

$ nm --demangle --defined-only cpp_demo
...
000000000000cefe T ChuckNorris::getFact[abi:cxx11]()

```

当我们编译`c_demo.o`的时候，我们使用了一个 *C* 编译器。(CMake 在源文件上看到了一个`.c`扩展名，因此告诉 ninja 用 C 编译器构建`main.c.o`)

由于 C 编译器没有*而不是*处理符号，所以`c_demo.o`和`libchucknorris.a`之间的最后一个链接失败了。

解决方案是告诉 C++编译器使用`extern`语法
来*而不是*破坏`chucknorris.h`头文件中定义的符号

```
 extern "C" {

  chuck_norris_t* chuck_norris_init(void);
  char* chuck_norris_get_fact(chuck_norris_t*);
  void chuck_norris_deinit(chuck_norris_t*);

} 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们这样做，我们现在会得到一个编译失败，因为 C 编译器不理解`extern`语法:

```
$ ninja
/bin/cc  -o main.c.o -c main.c
In file included from ../../src/main.c:1:0:
chucknorris.h:3:8: error: expected identifier or ‘(’ before string constant
extern "C" {

```

幸运的是，C++编译器为我们设置了一个`__cplusplus`定义:

```
 #ifdef __cplusplus extern "C" {
#endif 
  chuck_norris_t* chuck_norris_init(void);
  const char* chuck_norris_get_fact(chuck_norris_t*);
  void chuck_norris_deinit(chuck_norris_t*);

#ifdef __cplusplus }
#endif 
```

Enter fullscreen mode Exit fullscreen mode

现在构建通过了 <sup id="fnref2">[2](#fn2)</sup> ，我们可以仔细检查归档中的符号名称:

```
$ ninja
[1/7] Building C object CMakeFiles/c_demo.dir/src/main.c.o
[2/7] Building CXX object CMakeFiles/chucknorris.dir/src/c_wrapper.cpp.o
[3/7] Building CXX object CMakeFiles/cpp_demo.dir/src/main.cpp.o
[4/7] Building CXX object CMakeFiles/chucknorris.dir/src/ChuckNorris.cpp.o
[5/7] Linking CXX static library lib/libchucknorris.a
[6/7] Linking CXX executable bin/cpp_demo
[7/7] Linking CXX executable bin/c_demo

$ nm --defined-only libchucknorris.a
ChuckNorris.cpp.o:
0000000000000000 V DW.ref.__gxx_personality_v0
...
0000000000000160 T _ZN11ChuckNorrisC1Ev
...
000000000000020c T _ZN11ChuckNorris7getFactB5cxx11Ev

c_wrapper.o
...
0000000000000000 T chuck_norris_init
00000000000000ac T chuck_norris_deinit
000000000000003c T chuck_norris_get_fact

```

## 弦虫

万岁，我们成功构建了我们的 C 代码！让我们运行它:

```
$ ./bin/c_demo
���rU
 ./bin/c_demo
����BV
./bin/c_demo
'15R�U

```

哼。有些不对劲。

让我们再来看看`chuck_norris_get_fact`的实现:

```
const char* chuck_norris_get_fact(chuck_norris_t* chuck_norris)
{
  auto ck = reinterpret_cast<ChuckNorris*>(chuck_norris);
  std::string fact = ck->getFact();
  const char* result = fact.c_str();
  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

事情是这样的:

*   首先我们调用`ck->getFact()`并创建一个名为`fact`的局部变量
*   然后我们用`c_str()`得到一个指向`std::string`内容的`char*`指针
*   我们返回`char*`指针
*   但是随后`fact`变量超出了作用域，并且`std::string`的内容被释放。注意这就是`std::string`被*设计*去做的事情:它们为我们处理内存管理。我们遇到这个问题只是因为我们使用的是原始的 C 指针！
*   现在我们的`char*`指针指向未初始化的东西，我们得到垃圾。

解决方案是调用`strdup`来获得我们现在*拥有的*的内容的副本，并在以后明确地释放它。

(注意返回的指针不再是`const`。)

*src/c _ wrapper . CPP:*T2】

```
char* chuck_norris_get_fact(chuck_norris_t* chuck_norris)
{
  auto ck = reinterpret_cast<ChuckNorris*>(chuck_norris);
  std::string fact = ck->getFact();
  char* result = strdup(fact.c_str());
  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

*src/main . c:*T2】

```
int main()
{
  chuck_norris_t* ck = chuck_norris_init();
  char* fact = chuck_norris_get_fact(ck);
  printf("%s\n", fact);
  free(fact);
  chuck_norris_deinit(ck);
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在二进制开始工作了:

```
$ ninja
$ ./bin/c_demo
Chuck Norris doesn't dial the wrong number.
You answered the wrong phone.

```

今天到此为止。C 库是我们用来编写 Python 绑定和手机应用程序的基础。接下来的故事敬请期待！

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  那是骗人的。在`sqlite3.h`文件中有一些特殊的东西使这个工作。但是我们以后再谈这个。 [↩](#fnref1)

2.  这个关于`#ifdef __cplusplus`和`extern "C"`的小技巧在野外经常使用，你可以在`sqlite3.h`头中找到它。抱歉，我不得不撒谎来保持文章的流畅。 [↩](#fnref2)*