# Python 中实数常量 1 分钟指南

> 原文：<https://dev.to/wemake-services/1-minute-guide-to-real-constants-in-python-2bpk>

像`java`和`php`这样的许多语言共享一个`final`实体的概念。
`final`实体是不可改变的东西。

我们在`python`中没有这个功能。直到最近发生了两件事:

1.  我已经发布了 [`final-class`包](https://github.com/moscow-python-beer/final-class)
2.  `python`核心团队[已在`typing`模块发布](https://github.com/python/mypy/pull/5522)官方`final`支持

现在我们真的有了一个新的*闪亮的*语言特性！下面就来深挖一下它的工作原理，以及它为什么这么牛逼。

## 声明常数

首先，你需要安装`mypy`和`type_extensions` :

```
» pip install mypy typing_extensions 
```

Enter fullscreen mode Exit fullscreen mode

然后我们就可以开始使用了:

```
from typing_extensions import Final

DAYS_IN_A_WEEK: Final = 7 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！但是，如果我们试图修改这个常数，会发生什么呢？

```
from typing_extensions import Final

DAYS_IN_A_WEEK: Final = 7
DAYS_IN_A_WEEK = 8  # I really want more days in a week! 
```

Enter fullscreen mode Exit fullscreen mode

真的，没什么。这是一个很好的传统，你可以做一些奇怪的事情而没有回报。它只是不关心类型注释。

只有当我们运行`mypy`类型检查器:
时，所有的神奇才会发生

```
» mypy --python-version=3.6 --strict week.py
week.py:4: error: Cannot assign to final name "DAYS_IN_A_WEEK" 
```

Enter fullscreen mode Exit fullscreen mode

嘣！我们这里有一个常数！

看看`Final`类型如何处理底层类型。您不必手动告诉类型检查器实际上是什么类型。它会[自己解决](https://en.wikipedia.org/wiki/Type_inference)。换句话说，类型检查器将知道`DAYS_IN_A_WEEK`是`int`。

## 界面

它不仅仅是声明常量。你可以声明你的接口部分，比如属性和方法，它们不应该被改变:

```
from typing_extensions import Final, final

class BaseAPIDeclaration(object):
     namespace: Final = 'api'

     @final
     def resolve(self) -> dict:
         return {'namespace': self.namespace, 'base': True} 
```

Enter fullscreen mode Exit fullscreen mode

现在这个虚拟类的所有子类都不能重定义`namespace`和`resolve()`。但是，让我们尝试破解它们，看看会发生什么:

```
class ConcreteAPI(BaseAPIDeclaration):
    namespace = 'custom-api'

    def resolve(self) -> dict:
        return {'hacking': True} 
```

Enter fullscreen mode Exit fullscreen mode

会支持我们的。下面是输出的样子:

```
» mypy --python-version=3.6 --strict api.py
api.py:12: error: Cannot assign to final name "namespace"
api.py:14: error: Cannot override final attribute "resolve" (previously declared in base class "BaseAPIDeclaration") 
```

Enter fullscreen mode Exit fullscreen mode

## 类

而且连课都可以`final`。这样我们可以明确禁止子类化没有被设计成子类的类:

```
from typing_extensions import final

@final
class HRBusinessUnit(AbstractBusinessUnit):
    def grant_permissions(self) -> None:
        self.api.do_some_hr_stuff() 
```

Enter fullscreen mode Exit fullscreen mode

`@final` decorator 带给你什么？确信没有什么会破坏这个契约:

```
class SubHRBusinessUnit(HRBusinessUnit):
    def grant_permissions(self) -> None:
        self.api.do_some_it_stuff() 
```

Enter fullscreen mode Exit fullscreen mode

这段代码会让`mypy`相当不开心(拜托，不要虐待机器人！):

```
» mypy --python-version=3.6 --strict units.py
units.py:9: error: Cannot inherit from final class "HRBusinessUnit" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以推理为什么你应该在你的项目中使用它。

## 结论

创建新的限制对你有好处:它使你的代码更干净，更可读，并提高其质量。

优点:

1.  从定义中可以清楚地看出，什么是恒定的或具体的实现，什么不是
2.  我们的用户将有严格的 API 边界，不能违反
3.  我们可以建立封闭的系统，不能容忍破坏规则
4.  更容易理解应用程序内部发生了什么
5.  它在继承之上强制执行[合成，这是众所周知的最佳实践](https://en.wikipedia.org/wiki/Composition_over_inheritance)

缺点:无！如果你能发现任何缺点，写一个评论。

使用类型，创建漂亮的 API，继续黑客攻击！