# 推出“让我们打造查克·诺里斯！”项目

> 原文：<https://dev.to/dmerejkowsky/introducing-the-lets-build-chuck-norris-project--3d0g>

*最初发表于[我的博客](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)。*

# 查克·诺里斯图书馆

Chuck Norris 库是用 C++编写的，它包含一个类，当调用`getFact()`方法时，该类能够给你随机的 Chuck Norris 事实:

下面是使用方法:

```
#include <ChuckNorris.hpp> 
ChuckNorris chuckNorris;
std::string fact = chuckNorris.getFact(); 
```

Enter fullscreen mode Exit fullscreen mode

你会在 [GitHub](https://github.com/dmerejkowsky/chucknorris) 上找到所有的源代码。

# 我们来打造查克·诺里斯！

“让我们建造查克·诺里斯！”project 是一系列博客文章，旨在探索关于 C++和构建系统的各种主题:

这些都是按时间顺序排列的。尽情享受吧！

1.  [使用 CMake 和 Ninja](https://dmerej.info/blog/post/chuck-norris-part-1-cmake-ninja/)
2.  [管理与柯南的第三方依赖关系](https://dmerej.info/blog/post/chuck-norris-part-2-sqlite-conan/)
3.  [在 C++代码上公开 C API](https://dmerej.info/blog/post/chuck-norris-part-3-a-c-wrapper/)
4.  [通过 ctypes 使用 Python](https://dmerej.info/blog/post/chuck-norris-part-4-python-ctypes/)
5.  [使用 cffi 构建 Python 扩展](https://dmerej.info/blog/post/chuck-norris-part-5-python-cffi/)
6.  【Android 交叉编译
7.  [安卓和 JNA](https://dmerej.info/blog/post/chuck-norris-part-7-android-jna/)
8.  [在 iOS 应用程序中使用 c++](https://dmerej.info/blog/post/chuck-norris-part-8-ios/)