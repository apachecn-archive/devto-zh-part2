# Cpp 的另一个构建系统

> 原文：<https://dev.to/dannypsnl/another-build-system-for-cpp-974>

我认为像 CMake 这样的脚本工具对于大多数 C++项目来说太复杂了，所以我创建了一个名为:Cdog 的项目。

这里是链接 [cdog](https://github.com/dannypsnl/cdog) 。

我想知道它有用吗？

如何使用:

1.  创建项目目录。`mkdir hello && cd hello`
2.  创建子目录`src` `mkdir src`
3.  创建一个名为`cdog.toml` &的配置文件，在其中键入以下消息

```
 [project]
  src = "src" # source
  bin = "build" # output dir 
```

Enter fullscreen mode Exit fullscreen mode

1.  在方向`hello`执行`cdog build`

仅此而已。

选项`src`将变成包含目录。所以如果你的源目录下有一个`add.h`，你就可以`#include "add.h"`。如果同一路径下有`add.cc`，Cdog 会尝试编译它&将目标文件添加到最终二进制文件中。

编译结果将出现在选项框的设置中。