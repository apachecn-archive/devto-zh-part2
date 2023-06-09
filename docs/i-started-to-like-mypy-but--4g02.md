# mypy:快速了解 Python 中的静态类型检查

> 原文：<https://dev.to/k4ml/i-started-to-like-mypy-but--4g02>

我厌倦了人们错误地实现一些类，不根据基类接口，就像添加新的没有在基类方法中定义的位置参数。这通常发生在插件模式中，对于给定的功能，存在多个后端或实现。每个后端由独立的团队/开发人员开发。

或者不得不编写测试来确保将正确的参数传递给函数。应该编写测试来验证逻辑，而不是语言语法或语义。

我想要的第一件事是可以验证方法签名是否符合基类规范的东西。

[https://medium . com/@ ageitgey/learn-how-to-use-static-type-checking-in-python-3-6-in-10-minutes-12 c86d 72677 b](https://medium.com/@ageitgey/learn-how-to-use-static-type-checking-in-python-3-6-in-10-minutes-12c86d72677b)

让我们看下面这个简单的例子:-

```
class A():
    def __init__(self, x):
        self.x = x

    def send(self, msg):
        pass

class B(A):
    def send(self, msg):
        return True 
```

Enter fullscreen mode Exit fullscreen mode

上述代码的问题在于，其他开发人员(或团队)可能会以与其他子类不兼容的方式在`B`类中实现`send()`方法。例如通过添加额外的参数:-

```
class A():
    def __init__(self, x):
        self.x = x

    def send(self, msg):
        pass

class B(A):
    def send(self, msg, extra):
        return True 
```

Enter fullscreen mode Exit fullscreen mode

这可能被忽视，直到你需要插入另一个`A`子类来替换`B`，但是这个实现看起来不兼容，因为它不接受`extra`参数。如果我们有办法对方法签名进行类型检查以确保它符合基类规范，这是可以避免的。

```
import abc

class A(abc.ABC):
    def __init__(self, x: int) -> None:
        self.x = x     # Attribute x of type int 
    @abc.abstractmethod
    def send(self, msg: str) -> bool:
        pass

class B(A):
    def send(self, msg: str) -> bool:
        return True

b = B(1)
b.x = 2       # OK b.send('hello') 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果有人试图通过给`send()`添加额外参数来实现`B`，比如:-

```
import abc

class A(abc.ABC):
    def __init__(self, x: int) -> None:
        self.x = x     # Attribute x of type int 
    @abc.abstractmethod
    def send(self, msg: str) -> bool:
        pass

class B(A):
    def send(self, msg: str, extra: str) -> bool:
        return True

b = B(1)
b.x = 2       # OK b.send('hello', 'extra') 
```

Enter fullscreen mode Exit fullscreen mode

通过 mypy 运行它会给我们错误:-

```
base.py:12: error: Signature of "send" incompatible with supertype "A" 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在我们解决了本文开头提到的问题。

另一个常见问题是返回值不匹配。例如:-

```
import abc

class A(abc.ABC):
    def __init__(self, x: int) -> None:
        self.x = x     # Attribute x of type int 
    @abc.abstractmethod
    def send(self, msg: str) -> bool:
        pass

class B(A):
    def send(self, msg: str) -> bool:
        return 'hello'

b = B(1)
b.x = 2       # OK b.send('hello') 
```

Enter fullscreen mode Exit fullscreen mode

上面，类 A 中的方法`send()`被注释为返回一个布尔值，但是由于`B.send()`返回一个字符串，mypy 会抱怨:-

```
base.py:13: error: Incompatible return value type (got "str", expected "bool") 
```

Enter fullscreen mode Exit fullscreen mode

我在 mypy 身上看到了一些潜力。这对我们的测试套件是一个很好的补充，这样我们就不必为一些实际上可以被编译器捕获的情况编写测试了(如果我们有编译器的话)。但不幸的是，mypy】还不支持命名空间包,我们确实经常使用命名空间包。

我们面临的另一个问题是扩建。为了给标准库(大多数还没有注释)提供类型提示，mypy 提供了一个名为 typeshed 的隐藏库。Buildout 脚本定义了它自己的`sys.path`，但是 mypy 忽略了`sys.path`，因为它需要先查找类型，这意味着在我们的脚本中 buildout 设置的所有路径都不可用。我们必须将它指定为`MYPYPATH`,但这仍然不起作用，所以我放弃了。