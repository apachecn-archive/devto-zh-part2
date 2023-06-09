# Python 脚本工具箱:sys 和 fileinput

> 原文：<https://dev.to/rpalo/python-scripting-toolbox-sys-and-fileinput-3aob>

*Unsplash 上 Fleur Treurniet 拍摄的封面照片*

Python 是一种非常灵活的语言，可用于大量不同的应用和领域:web 应用、自动化、科学和数据分析等等。然而，我最喜欢用它来写脚本。脚本可以简单到编写一个快速的一次性小脚本，以比您想要手动完成的更快的速度完成某件事情，也可以复杂到一个您可以润色的重复任务——带有选项、标志和一点用户界面的东西。

幸运的是，Python 已经在其标准库中内置了许多脚本编写的基本要素！我将向您展示几个我最喜欢的功能以及它们的使用方法。对于所有这些例子，我将使用 Python 3.6。大多数东西应该在任何 Python 3 中都能工作。如果您使用传统 Python (Python 2)，您的收获可能会有所不同。

> 我知道有一些库和框架可以用来创建命令行界面。他们太棒了。在本文中，我将重点介绍 Python 的“附带电池”:标准库。我将向您展示在不安装任何外部依赖项的情况下，您可以完成多少工作。

## 设置

这将是一个由三部分组成的关于 Python 脚本工具的调查。我将通过创建三个展示标准库不同部分的脚本来展示这个功能。

1.  一个脚本，它把你传递给它的所有东西都喊出来。
2.  `make_script.py`:一个从模板生成启动脚本的脚本，用于像[项目欧拉](https://projecteuler.net/)或[罗莎琳德](http://rosalind.info/problems/locations/)这样的事情。
3.  `project_setup.py`:创建基本项目框架的脚本

这是第一部分。现在，让我们开始吧。

## 剧本 1:喊

你有没有希望你的电脑对你大喊大叫？或者你的电脑更粗糙？你很幸运，因为我们会解决所有的问题。创建一个名为`shout.py`的新文件。我们将首先填充基本功能，然后在此基础上构建界面。

```
#!/usr/bin/env python """Takes in input and returns that same input, but uppercase."""

def shout(text):
    return text.upper() 
```

Enter fullscreen mode Exit fullscreen mode

目前为止还不错！我想从简单开始，这样我们就可以主要关注我们的工具箱。现在，如果你运行`python shout.py`，它什么也不做。让我们解决这个问题。但是我们如何访问提供给脚本的参数呢？我们的第一个工具:`sys`。`sys`是一个模块，处理许多不同的系统级、特定于系统或特定于 python 安装的选项和值。您可以使用它来检查某人使用的 Python 版本，脚本运行在什么操作系统上，或者(我们使用它的目的)获得对`stdin`、`stdout`和命令行参数的访问。

### 使用`sys.argv`

让我们来看看使用这个脚本的一种方式，如果我们有一组或特定数量的参数，这可能是一个好的选择。

```
#!/usr/bin/env python3 """Takes in input and returns that same input, but uppercase."""

import sys

def shout(text):
    return text.upper()

if __name__ == "__main__":
    text = sys.argv[1]
    print(shout(text)) 
```

Enter fullscreen mode Exit fullscreen mode

`argv`是传递给我们脚本的参数数组(用空格分隔)。注意`argv[0]`是运行的脚本的名称。运行这个，结果会是:

```
$ python3 shout.py banana
BANANA 
```

Enter fullscreen mode Exit fullscreen mode

对某些事情来说，这是可行的。如果我们有一个基于边数和边长(`e.g. python polygonArea.py 5 20`)来计算多边形面积的脚本，`argv`将正好是医生所要求的。但是，在这种情况下，像任何其他 shell 命令一样，通过`stdin`将东西传递到我们的脚本中可能是有用的。幸运的是，`sys`又一次支持了我们！

### [T3 使用`sys.stdin`和`sys.stdout`](#using-raw-sysstdin-endraw-and-raw-sysstdout-endraw-)

```
#!/usr/bin/env python3 """Takes in input and returns that same input, but uppercase."""

import sys

def shout(text):
    return text.upper()

if __name__ == "__main__":
    text = sys.stdin.read()
    sys.stdout.write(shout(text)) 
```

Enter fullscreen mode Exit fullscreen mode

`sys`模块提供了`stdin`、`stdout`和`stderr`的句柄，您可以分别读取和写入这些句柄，就像任何其他文件类型的对象一样。现在找到一个文本文件或者创建一个多行文本文件，然后像这样运行你的代码:

```
$ cat test.txt | python3 shout.py
BANANA OONANA
I GAVE MY HEART TO A BANANA
MY HEART TO A BANANA.
BANANA OONANA! 
```

Enter fullscreen mode Exit fullscreen mode

我很抱歉所有的“香蕉”的东西。T4 那首歌一直萦绕在我的脑海里。但是 Python 的东西很酷，对吧？这里我们还可以做一个改进。如果我们想要的文本真的很长呢？逐行处理会更好，对吗？这样，我们可以在处理过程中看到部分输出，而不必担心将整个文件读入内存。这就是`stdin`和`stdout`成为“类文件对象”的伟大之处他们有和普通文件一样的方法。这意味着我们可以在一个`for`循环中迭代`stdin`的行！

```
# ... if __name__ == "__main__":
    for line in sys.stdin:
        sys.stdout.write(shout(line)) 
```

Enter fullscreen mode Exit fullscreen mode

这一次，当您运行它时，您不会注意到任何不同。让我们在每行之间加一个延迟。

```
#!/usr/bin/env python3 """Takes in input and returns that same input, but uppercase."""

import sys
from time import sleep

def shout(text):
    return text.upper()

if __name__ == "__main__":
    for line in sys.stdin:
        sys.stdout.write(shout(line))
        sleep(3) 
```

Enter fullscreen mode Exit fullscreen mode

当您这次运行它时，这些行应该会慢慢出现，而旧版本无论如何都会同时出现！

### 使用`fileinput`

如果你真的想用这种大喊大叫的工具来达到高潮呢？你希望能够一次喊出一大堆文件，伴随着来自`stdin`的管道声？你会想要`fileinput`。看看这个。

```
#!/usr/bin/env python3 """Takes many inputs and returns that same input, but uppercase."""

import fileinput
import sys

def shout(text):
    return text.upper()

if __name__ == "__main__":
    for line in fileinput.input():
        sys.stdout.write(f"({ fileinput.filename() }) - { shout(line) }") 
```

Enter fullscreen mode Exit fullscreen mode

现在，创建第二个文件(`test2.txt`)。像这样运行脚本:

```
$ python3 shout.py test.txt test2.txt
(test.txt) - BANANA OONANA
(test.txt) - I LEFT MY HEART INSIDE BANANAS
(test.txt) - INSIDE BANANAS.
(test.txt) - BANANAS OONANANA!
(test2.txt) - THIS IS THE SECOND FILE.
(test2.txt) - IT IS LAMER.
(test2.txt) - NOT AS CATCHY OF TEXT.
(test2.txt) - HELLO. 
```

Enter fullscreen mode Exit fullscreen mode

[`fileinput`](https://docs.python.org/3/library/fileinput.html) 使用`input`函数组合所有的文件名参数，并将其读入一个单独的逐行流。它还有一些额外的有用功能，你可以在阅读时调用这些功能来添加额外的信息。我们用它来提供正在处理的文件的文件名，但是还有更多，比如`lineno`、`filelineno`、`isfirstline`和`isstdin`。您甚至可以添加额外的选项来就地更改输入文件，创建就地修改文件的备份，解压缩压缩文件，等等！

*但是等一下。这真的很酷，但是你保证我们仍然可以通过这种方法使用`stdin`。这是怎么回事？*

您可以使用一个破折号(`-`)将`stdin`包含在您的文件列表中。

```
echo "whaddup from stdin" | python3 shout.py test.txt - test2.txt
(test.txt) - BANANA OONANA
(test.txt) - I LEFT MY HEART INSIDE BANANAS
(test.txt) - INSIDE BANANAS.
(test.txt) - BANANAS OONANANA!
(<stdin>) - WHADDUP FROM STDIN
(test2.txt) - THIS IS THE SECOND FILE.
(test2.txt) - IT IS LAMER.
(test2.txt) - NOT AS CATCHY OF TEXT.
(test2.txt) - HELLO. 
```

Enter fullscreen mode Exit fullscreen mode

## 全部喊完

以上就是`sys`、`fileinput`，以及我们的`shout.py`剧本。请继续关注下一部分，我们将构建`make_script.py`——我们将编写一个脚本来编写脚本，这样我们就可以在脚本编写的同时编写脚本。下次见！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/06/05/python-toolbox-shout/)*