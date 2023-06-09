# Python 和神奇的 Singleton

> 原文：<https://dev.to/asaf_g6/python-and-the-magic-singleton-4345>

## 单例设计模式

如果你现在已经写了一段时间的代码，你一定遇到过设计模式。其中最简单的是独生子女。

来自[维基百科](https://en.wikipedia.org/wiki/Singleton_pattern):

> singleton 模式是一种软件设计模式
> ，它将一个类的实例化限制在一个对象上。

当你的应用程序需要一个全局可访问的、只有一个实例的惰性加载对象时，你应该使用单例模式。

有些人可能会告诉你，在 python 中使用 singleton 是完全没有必要的。那是不对的。解决方案的必要性总是取决于问题。

如果只需要全局可访问对象，可以使用 globals。如果你不在乎延迟加载，你可以使用一个模块或者一个“静态”类。有很多可能性。

在我看来——如果它合适，并且能让你的代码更好，那就去做吧。
中的
出自[禅宗中的巨蟒](https://www.python.org/dev/peps/pep-0020/):

> “漂亮总比难看好。”
> 
> "如果实现容易解释，这可能是一个好主意."

## 实现

如果你在谷歌上搜索“python singleton”，你会发现很多不同的 singleton 模式的实现。
这个是我最喜欢的:

```
 class Singleton(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls] 
```

Enter fullscreen mode Exit fullscreen mode

一个元类可以定制另一个类的创建过程。这可能有点令人困惑，因为这是 python 具有的神奇特性之一，而大多数编程语言都没有。基本上就是班里的班。迷茫？让我们做一个小实验，试着理解发生了什么。

## 真神奇！

让我们试试下面的代码:

```
 class MyMeta(type):

    def __call__(cls, *args, **kwargs):
        print('meta!')

class MyClass(metaclass=MyMeta):

    def __init__(self):
        print('class')

if __name__ == '__main__':
    c = MyClass()
    print(c) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们定义了一个输出“元类”的元类和一个输出 class 的类。
注意，我们覆盖了神奇的函数 **__call__** 。每当你调用对象时， **__call__** 函数就会执行。在类方面，这意味着 **MyClass()** 等价于 **MyClass。**调用** ()** 。

上面的代码输出如下:

```
 meta!
None 
```

Enter fullscreen mode Exit fullscreen mode

因此..为什么变量 c 实际上是零...？

发生这种情况是因为我们的 **__call__** 函数不返回任何东西。
很酷吧？你可以恶作剧你的朋友，让他们的代码无法工作，直到他们找到元类定义(请不要！:) )

让我们继续修复那个 **__call__** 函数:

```
 class MyMeta(type):

    def __call__(cls, *args, **kwargs):
        print('meta!')
        return super(MyMeta, cls).__call__(*args, **kwargs)  # call the class's __call__ manually 

class MyClass(metaclass=MyMeta):

    def __init__(self):
        print('class')

if __name__ == '__main__':
    c = MyClass()
    print(c) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码输出:

```
meta!
class
<__main__.MyClass object at 0x7f43fa218898> 
```

Enter fullscreen mode Exit fullscreen mode

成功！我们定制了类实例化过程。现在，我们可以更好地理解我们的单例元类。元类创建一个实例，并在您调用该类时返回该实例。

你可以用 **__call__** 函数做任何想到的事情，Python 限制不是很大。然而，你应该非常小心。众所周知，代码读起来比写起来要多，直观性很重要。

## 当魔法变成巫毒的时候

元类单例实现有一个缺点，它是隐式的。
考虑下面的例子(MyClass 是我们之前的单例):

```
 # lots of code here 
c = MyClass()

def some_function():
    # some code
    a = MyClass()
    # some more code 
# lots of code 
class AnotherClass(object):

    def __init__(self, *args):
        self._d = MyClass()
        # more code 
# you got it.. more code 
```

Enter fullscreen mode Exit fullscreen mode

我们正在一个大型项目中使用我们新的单例实现，甚至可能是数万行代码。现在，一个新的编码员加入了团队并读取代码。你认为他/她能轻易分辨出我的同学是独生子吗？

99%的情况下答案是否定的。

那么，我们该怎么做才能让我们的单例更显式呢？

这里有一个解决方案:

```
 class MyClass(metaclass=MyMeta):

    def __init__(self):
        print('class')

    @classmethod
    def get_instance(cls):
        return MyClass()

# instead of calling MyClass() we'll call MyClass.get_instance() 
```

Enter fullscreen mode Exit fullscreen mode

**get_instance** 类方法非常清楚地表明这实际上是一个 singleton。
当然，你可以调用 **MyClass()** ，它会给你同样的结果。然而，使你的代码更清晰通常会使它更容易理解。

## 总结

我希望你能分享我对单身族、魔法和巫毒教的看法。当然，如果你没有，我会很乐意听到。

我希望你喜欢，并一如既往，随时评论或分享。

感谢阅读。