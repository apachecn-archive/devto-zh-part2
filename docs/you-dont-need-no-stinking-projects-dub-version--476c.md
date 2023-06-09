# 不需要没有臭项目[配音版]

> 原文：<https://dev.to/btbytes/you-dont-need-no-stinking-projects-dub-version--476c>

假设您想要构建一个应用程序，您知道该应用程序可以放在一个文件中。许多脚本/自动化程序都属于这一类。但是，该程序依赖于第三方库。你认为你可能需要建立一个“项目”，你开始想“也许我可以用 Python 来做这个”。但是你仍然需要使用 pip 或者创建一个`virtualenv`(全局)安装包。如果你不得不“运输”这个程序，你别无选择，只能运输依赖管理工具(即，`requirements.txt`或者一个`Pipfile`。有了 D，(具体来说就是`dub`，你就可以鱼与熊掌兼得了。

给我看看代码！

```
#!/usr/bin/env dub
/+ dub.sdl:
    name "allthepythons"
    dependency "d-glob" version="~>0.3.0"
+/

import std.stdio : stdout;
import glob : glob;

void main () {
  foreach (entry ; glob("/usr/local/bin/python*")) {
    stdout.writefln("%s", entry);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的程序中，您使用了一个名为 [glob-d](https://code.dlang.org/packages/d-glob) 的第三方库(您可能会说，“使用 python，您不必为此使用第三方库，哈！”。请原谅，这毕竟是一个虚构的例子！).

`#!/usr/bin/env dub` hashbang 告诉 shell 使用 D 的`dub` [包管理器](https://code.dlang.org/getting_started)，来运行下面的代码。

包含在`/+ +/`中的注释部分是一个专用于`dub`的特殊部分，它告诉我们这个单文件包叫做什么(`allthepythons`)以及它有什么依赖关系(`d-glob`)。Dub 默认支持两种数据定义语言——[JSON](https://code.dlang.org/package-format?lang=json)和 [sdl](https://code.dlang.org/package-format?lang=sdl) 。我在这里用了`sdl`，因为它看起来很舒服。

代码的其余部分很容易理解，不需要特别的解释。

运行脚本非常简单:

```
$ ./allthepythons.d

/usr/local/bin/python2
/usr/local/bin/python3
... truncated ... 
```

Enter fullscreen mode Exit fullscreen mode

这对于开发、测试和编写脚本来说是极好的。但是，如果您需要将二进制文件部署到 a)没有安装 dub 的机器上，该怎么办呢？和/或 b)不具有从因特网自动下载包的因特网连接？(比如说，出于安全考虑)。将上面的文件转换成二进制很容易:

```
$ dub build --single allthepythons.d
Performing "debug" build using dmd for x86_64.
d-glob 0.3.0: target for configuration "default" is up to date.
allthepythons ~master: building configuration "application"...
Linking...
To force a rebuild of up-to-date targets, run again with --force. 
```

Enter fullscreen mode Exit fullscreen mode

您将最终得到一个名为`allthepythons`的二进制文件，然后可以很容易地进行部署。

注意:为了生成一个“发布”二进制文件，在`dub build`命令中添加`--build=release`。

```
dub build --build=release --single allthepythons.d 
```

Enter fullscreen mode Exit fullscreen mode

感谢 Reddit 上的 [Aravinda K](https://www.reddit.com/user/aravindavk) 指出来！

万岁！

[gist.github.com 上的源代码](https://gist.github.com/btbytes/608a52b705b403df73c13bfe2a8ca718)