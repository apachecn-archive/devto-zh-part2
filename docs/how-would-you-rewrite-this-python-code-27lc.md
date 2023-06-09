# 你将如何重写这段 Python 代码？

> 原文：<https://dev.to/cathodion/how-would-you-rewrite-this-python-code-27lc>

我在[为挑战](https://dev.to/r0f1/write-a-simple-but-impactful-script-7ba)写一些代码的时候遇到了这个问题。

我有一些[代码](https://gist.github.com/dmoney/1de8f451f58b899ce5650d37c22da2be)，如果提供了文件名，我想把它们写到一个特定的文件中，如果没有，我想把它们写到标准输出中:

```
with (open(filename, 'w') if filename else sys.stdout) as file:
    do_something(file) 
```

Enter fullscreen mode Exit fullscreen mode

那一行有点长，一眼看上去不太可读。我可以把`open`放在`with`之外:

```
f = open(filename, 'w') if filename else sys.stdout
with f as file:
    do_something(file) 
```

Enter fullscreen mode Exit fullscreen mode

当退出`with`时，文件应该关闭。在这种情况下，我已经完成了对`stdout`的处理，但是我可能只是想在程序的后面将它用于其他事情。但是这种方式最糟糕的地方似乎是养成了在`with`表达式之外使用`open`的习惯，这可能会导致忘记关闭文件。

我可以走尝试/最终路线:

```
try:
    f = open(filename, 'w') if filename else sys.stdout
    do_something(f)
finally:
    if f is not sys.stdout:
        f.close() 
```

Enter fullscreen mode Exit fullscreen mode

但是这似乎有点冗长，让我想起了太多的 Java，根据经验，这可能意味着它不是[python 式的](http://docs.python-guide.org/en/latest/writing/style/)。

我可以写一个[上下文管理器](https://docs.python.org/3/library/contextlib.html)来隐藏“检查它是否是 stdout 并关闭它”的逻辑:

```
from contextlib import contextmanager

@contextmanager
def file_or_stdout(fname):
    if fname:
        f = open(fname, 'w')
        yield f
        f.close()
    else:
        yield sys.stdout

with file_or_stdout(filename) as file:
    do_something(file) 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很清楚，但也可能是矫枉过正。不过，我倾向于这样做，因为它给`do_something`块留下了足够的清晰空间，并且我可以将`file_or_stdout`函数提取到一个实用程序库中，这样就不会弄乱文件。

有什么想法吗？