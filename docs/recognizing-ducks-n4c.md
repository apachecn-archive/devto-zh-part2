# 认识鸭子

> 原文：<https://dev.to/tmr232/recognizing-ducks-n4c>

在多次尝试寻找一个能贯穿整篇文章的有趣叙述，但都以惨败告终后，我决定只写技术部分。我的很多同事都觉得它很有趣，所以我想如果没有笑话，它也能撑得住。

Python 为我们提供了多种方法来检查我们传递给函数的对象是否是我们期望的类型。每种方法都有其优点和缺点。

## 只是不在乎

第一种选择自然是不关心类型——只写你的代码，并抱最好的希望。这是一种可行的方法，经常被采用。它尤其适用于您不希望维护太多的简短片段或脚本。它不需要任何开销就可以工作。

```
def poke(duck):
    duck.quack()
    duck.walk() 
```

## 遗产

另一种选择是使用继承，这在 OOP 语言中很常见。我们可以定义一个`Anas`类，并期望它的所有衍生物都足够像鸭子。

```
class Anas:
    def quack(self): pass
    def walk(self): pass

class Duck(Anas):
    def quack(self): print('Quack!')
    def walk(self): print('Walks like duck.')

class Mallard(Anas):
    def quack(self): print('Quack!')
    def walk(self): print('Walks like duck.')

def poke(duck):
    assert isinstance(duck, Anas) 
```

## 接口

虽然遗传有点像是完成了工作，但机器鸭肯定不属于 [Anas](https://en.wikipedia.org/wiki/Anas) 类，尽管它确实实现了我们关心的所有特征。所以我们可以用接口来代替层次继承。

```
from abc import ABC, abstractmethod

class IDuck(ABC):
    @abstractmethod
    def quack(self): pass

    @abstractmethod
    def walk(self): pass

class Duck(IDuck):
    def quack(self): print('Quack!')
    def walk(self): print('Walks like duck.')

class RoboticDuck(IDuck):
    def quack(self): print('Quack!')
    def walk(self): print('Walks like duck.')

def poke(duck):
    assert isinstance(duck, IDuck) 
```

太好了。如果我们不控制类型，我们总是可以编写适配器。

## 鸭子测试

但这是 Python。我们可以做得更好。

正如我们所知，Python 使用鸭子类型。所以我们应该能够使用[鸭子测试](https://en.wikipedia.org/wiki/Duck_test)来进行类型测试。在我们的例子中，每个实现`quack()`和`walk()`的对象都是一只鸭子。这很容易检查。

```
def is_a_duck(duck):
    for attr in ('quack', 'walk'):
        if not hasattr(duck, attr):
            return False
    return True

def poke(duck):
    assert is_a_duck(duck)
    duck.quack()
    duck.walk() 
```

这个管用。但是我们列出了`isinstance(...)`调用。我们肯定能做得更好。

## 元类和子类挂钩

元类是奇妙的构造。顾名思义，他们参与类的构造。他们甚至允许我们在基本的 Python 机制中设置钩子，像`isinstance(...)`，使用 [`__subclasshook__`](https://docs.python.org/3/library/abc.html#abc.ABCMeta.__subclasshook__) 。

```
from abc import ABC

def is_a_duck(duck):
    for attr in ('quack', 'walk'):
        if not hasattr(duck, attr):
            return False
    return True

class DuckChecker(ABC):
    @classmethod
    def __subclasshook__(cls, C):
        if cls is not DuckChecker:
            return NotImplemented
        return is_a_duck(C)

def poke(duck):
    assert isinstance(duck, DuckChecker)
    duck.quack()
    duck.walk() 
```

我们又重操旧业了。也就是说，`is_a_duck`仍然是一团乱麻，维护起来会非常痛苦。

如果我们可以使用我们的`IDuck`界面来检查 duck-ness 不是很好吗？

## 又是抽象方法！

幸运的是，我们可以！

除此之外，`ABC`父类枚举所有的`@abstractmethod`并将它们存储在`__abstractmethods__`成员变量中。这意味着我们可以很容易地在我们的子类 hook 中枚举它们并检查它们。

```
from abc import ABC, abstractmethod

class IDuck(ABC):
    @abstractmethod
    def quack(self): pass

    @abstractmethod
    def walk(self): pass

    @classmethod
    def __subclasshook__(cls, C):
        if cls is not IDuck:
            return NotImplemented
        for attr in cls.__abstractmethods__:
            if not hasattr(C, attr):
                return False
        return True

class Duck:
    def quack(self): print('Quack!')
    def walk(self): print('Walks like a duck.')

def poke(duck):
    assert isinstance(duck, IDuck)
    duck.quack()
    duck.walk()

poke(Duck())  # Quack!
              # Walks like a duck. 
```

太棒了。下一步——将接口与检查逻辑分离。

## 协议

通读 Python 文档和术语，您可能到处都能看到术语“协议”。这是 Python 调用鸭式接口的方式。所以你可以说我们刚刚创建了一个“协议检查器”。现在，我们可以把它分成一个基类。

```
from abc import ABC, abstractmethod

class Protocol(ABC):
    _is_protocol = True
    @classmethod
    def __subclasshook__(cls, C):
        if not cls._is_protocol:
            return NotImplemented
        for attr in cls.__abstractmethods__:
            if not hasattr(C, attr):
                return False
        return True

class IDuck(Protocol):
    @abstractmethod
    def quack(self): pass

    @abstractmethod
    def walk(self): pass 
```

仅此而已。那面小旗子的存在是有原因的。通常，我们会使用`isinstance(...)`来检查协议。然而，在这种情况下，我们将挂钩到该机制，这将导致无限递归。

我们现在可以自由地使用我们的`Protocol`基类来创建我们需要的新协议，使用友好的类似接口的语法。我们快完成了。

## 这只狗是一只鸭子

在某些情况下，协议检查可能不是我们想要的。我想到的明显原因是:

1.  我们无法使用协议技巧真正检查所需的语义。
2.  我们想要破坏浩劫。

对于那些情况(嗯，主要是第一种情况)，`ABC`基类提供了另一个技巧。我们可以简单地将类注册为有效的“虚拟子类”，而不是定义`__subclasshook__`来检查接口。

```
from abc import ABC

class IDuck(ABC): pass

class Duck:
    def quack(self): print('Quack!')
    def walk(self): print('Walk like a duck.')

IDuck.register(Duck)

def poke(duck):
    assert isinstance(duck, IDuck)
    duck.quack()
    duck.walk() 
```

记住，这个方法把所有的压力都放在了程序员身上。写`IDuck.register(Dog)`相当于说“我担保这只狗是只鸭子”。它可能通过检查，但不一定会产生预期的结果。

## 摘要

在本文中，我们介绍了多种检查对象“鸭”的方法。从属于阿纳斯属，到只是在他们的头上贴一张写着“鸭子！”。其中一些方法比其他方法更有用或更适用，但我仍然认为熟悉所有这些方法是值得的。此外，还有许多主题没有在这里讨论，比如静态类型检查。

### 进一步阅读

这里演示的元类技术是来自 [`abc`](https://docs.python.org/3/library/abc.html) 和 [`typing`](https://docs.python.org/3/library/typing.html) 模块的代码的简化版本。我强烈建议浏览这些模块和它们各自的文档，至少浏览一遍，以扩展你的知识并弥补我的手动解释留下的任何漏洞。