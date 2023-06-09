# Python 有启动文件！

> 原文：<https://dev.to/rpalo/python-has-a-startup-file-b7c>

*乌列尔·索伯兰斯在 Unsplash 上拍摄的封面照片*

所以，我想说清楚。在我使用 Python 的这段时间里，我知道 Python 有一个启动定制文件。*我不是这个星期才知道的。我的意思是，当然*Python 有一个启动文件。一切都有一个启动文件！我只是想确定你知道这件事。开个玩笑，我不知道这是个东西。)**

> 在你提到它之前，我已经知道了 [bPython](https://bpython-interpreter.org/screenshots.html) ，它是取代常规 Python 解释器的令人敬畏的、语法突出的、制表符完整的、自动缩进的、出色的工具。我一直在用它。但这不是这篇博文的内容。P.S .如果你不知道 bPython，我强烈推荐它(Windows 用户的里程可能会有所不同)。

## $PYTHONSTARTUP

如果您将环境变量`$PYTHONSTARTUP`设置为有效的 Python 文件，那么该文件将在启动 Python 解释器时运行。

```
$ export PYTHONSTARTUP="~/.config/pythonrc.py" 
```

Enter fullscreen mode Exit fullscreen mode

不要担心文件名。随便你怎么命名！`python_startup.py`，或者只是`pythonrc`。你也可以把它放在任何你想要的目录里。只要确保您的`$PYTHONSTARTUP`环境变量匹配。然后，您可以将任何内容放入该文件。

```
# ~/.config/pythonrc.py a = "Wahoo!"
print("Soup")
try:
    import numpy as np
except ImportError:
    print("Could not import numpy.") 
```

Enter fullscreen mode Exit fullscreen mode

尝试运行您的 Python 解释器。

```
$ python  # or python3 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似下面的内容:

```
Python 3.7.0 (default, Jun 29 2018, 20:14:27)
[Clang 9.0.0 (clang-900.0.39.2)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
Soup
>>> np
<module 'numpy' from '/usr/local/lib/python3.7/site-packages/numpy/__init__.py'>
>>> np.zeros((3, 2))
array([[0., 0.],
       [0., 0.],
       [0., 0.]])
>>> a
'Wahoo!' 
```

Enter fullscreen mode Exit fullscreen mode

您可以导入常用的库，为自己创建变量，等等。

## sys.ps1 和 sys.ps2

一件简单的事情是设置`sys.ps1`和`sys.ps2`变量，它们控制您的 Python 提示。

```
# ~/.config/pythonrc.py 
import sys

sys.ps1 = "🌮"
sys.ps2 = "💩"

# ... 
```

Enter fullscreen mode Exit fullscreen mode

还有，回到互动 REPL:

```
🌮 for i in range(10):
💩     print("I am a mature adult.")
💩
I am a mature adult.
I am a mature adult.
... 
```

Enter fullscreen mode Exit fullscreen mode

事实上，您甚至可以将`sys.ps1`和`sys.ps2`设置为甚至不是字符串的对象！如果它们不是字符串，Python 会对它们调用`str(obj)`。

```
# ~/.config/pythonrc.py 
import sys
from datetime import datetime

class CustomPS1:
    def __init__(self):
        self.count = 0

    def __str__(self):
        self.count += 1
        return f"({self.count}) {datetime.now().strftime('%H:%m %p')} > "

sys.ps1 = CustomPS1() 
```

Enter fullscreen mode Exit fullscreen mode

并且在解释器中:

```
(1) 10:06 AM > for i in range(10):
...     print("Am I cool now?")
...
Am I cool now?
Am I cool now?
# ... (2) 11:06 AM > 
```

Enter fullscreen mode Exit fullscreen mode

## -I 标志

除了这些新的超能力之外，你可以暂时将任何一个 Python 脚本作为你的启动脚本。这对于一些交互式调试来说非常方便。假设你正在做一个项目，你有一个定义了一些函数的脚本:

```
# cool_script.py 
def what_time_is_it():
    return "Party Time" 
```

Enter fullscreen mode Exit fullscreen mode

当您运行 Python 解释器时，您可以使用`-i`标志来使用`cool_script.py`作为您的启动文件，而不是您通常使用的文件。

```
$ python -i cool_script.py
>>> what_time_is_it()
'Party Time' 
```

Enter fullscreen mode Exit fullscreen mode

如果你用你的启动文件做了一些很酷的事情，分享给我吧！我想了解一下！编码快乐！

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/06/30/python-startup-file/)*