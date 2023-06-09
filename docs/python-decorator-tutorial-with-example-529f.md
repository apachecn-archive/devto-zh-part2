# 带参数的 Python 装饰器带示例的 Python 装饰器教程

> 原文：<https://dev.to/apcelent/python-decorator-tutorial-with-example-529f>

如果你想真正深入研究，你应该读读这些由[格雷厄姆·邓普顿](https://twitter.com/GrahamDumpleton)写的[详尽的文章](https://github.com/GrahamDumpleton/wrapt/tree/develop/blog)。然而，如果您打算开始并更好地阅读/编写 python decorators，这篇文章应该足够了。

在 python 中，一切都是对象，甚至函数也是。一个函数可以赋给一个变量，传递给另一个函数，也可以从另一个函数返回。看看下面的例子:

```
[1]: def outer_function():
   ...:     print "1\. This is outer function!"
   ...:     def inner_function():
   ...:         print "2\. This is inner function, inside outer function!"
   ...:     print "3\. This is outside inner function, inside outer function!"
   ...:     return inner_function()
   ...:

[2]: func_assign = outer_function()
1. This is outer function!
3. This is outside inner function, inside outer function!
2. This is inner function, inside outer function! 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在上面的执行中，inner 函数中的语句是如何在 outer_function 的末尾打印出来的，结果是 inner_function 被返回，并且在赋值期间可以看到执行。

Python decorator 是接收一个函数作为参数并返回另一个函数作为返回值的函数。对于装饰器的假设是，我们将传递一个函数作为参数，并且装饰器中内部函数的签名必须与要装饰的函数相匹配。

## 功能装饰器

现在，让我们为自己写一个简单的函数装饰器。我们将编写一个装饰器来测量传递给它的函数的执行时间。

```
import time

def timetest(input_func):

    def timed(*args, **kwargs):

    start_time = time.time()
    result = input_func(*args, **kwargs)
    end_time = time.time()
    print "Method Name - {0}, Args - {1}, Kwargs - {2}, Execution Time - {3}".format(
        input_func.__name__,
        args,
        kwargs,
        end_time - start_time
    )
    return result
    return timed

@timetest
def foobar(*args, **kwargs):
    time.sleep(0.3)
    print "inside foobar"
    print args, kwargs

foobar(["hello, world"], foo=2, bar=5)

inside foobar
(['hello, world'],) {'foo': 2, 'bar': 5}
Method Name - foobar, Args - (['hello, world'],), Kwargs - {'foo': 2, 'bar': 5}, Execution Time - 0.30296087265 
```

Enter fullscreen mode Exit fullscreen mode

我们将 foobar 函数传递给名为 timetest 的装饰器。在 decorator 内部，函数 foobar 被引用为变量 input_func。input_func 执行后的结果称为 result。

在装饰者的名字前面加上@,并在一个函数上面写同样的内容来调用装饰者，并将函数传递给装饰者(decorates)。

## 方法装饰器

方法装饰器允许通过装饰来覆盖类属性，而不必找到调用函数。

```
def method_decorator(method):

    def inner(city_instance):
    if city_instance.name == "SFO":
        print "Its a cool place to live in."
    else:
        method(city_instance)
    return inner

class City(object):

    def __init__(self, name):
    self.name = name

    @method_decorator
    def print_test(self):
    print self.name

p1 = City("SFO")

p1.print_test()

Its a cool place to live in. 
```

Enter fullscreen mode Exit fullscreen mode

在上面显示的代码片段中，我们修饰了类方法 print_test。如果 city 实例的名称不是 SFO，method_decorator 打印城市的名称。

## 类装修工

如果您想创建一个返回另一个可调用对象的可调用对象，函数装饰器方法更容易。如果你希望返回的是一个函数，函数装饰器应该是首选，但是如果你希望装饰器返回一个自定义对象，做一些与函数不同的事情，在这种情况下应该使用类装饰器。

使用类，可以向修饰的可调用对象添加方法和属性，或者对它们实现操作。您可以创建描述符，这些描述符在放入类中时以特殊的方式起作用(例如 classmethod，property)

```
class decoclass(object):

    def __init__(self, f):
    self.f = f

    def __call__(self, *args, **kwargs):
    # before f actions
    print 'decorator initialised'
    self.f(*args, **kwargs)
    print 'decorator terminated'
    # after f actions 
@decoclass
def klass():
    print 'class'

klass() 
```

Enter fullscreen mode Exit fullscreen mode

## 连锁装饰者

decorator 的链接类似于如何使用多重继承来构造类，我们可以编写任意多的 decorator，并在定义要修饰的函数之前，用修饰语法将它们一个接一个地包含在修饰行中。

```
def makebold(f):
    return lambda: "<b>" + f() + "</b>"
def makeitalic(f):
    return lambda: "<i>" + f() + "</i>"

@makebold
@makeitalic
def say():
    return "Hello"

print say() 
```

Enter fullscreen mode Exit fullscreen mode

有一点应该记住，我们设置的装饰者的顺序很重要。当你链接装饰者时，他们的排列顺序是从下到上的。

## Functools 和 Wraps

当我们使用装饰器时，我们是在用一个功能替换另一个功能。

```
def decorator(func):
    """decorator docstring"""
    def inner_function(*args, **kwargs):
    """inner function docstring """
    print func.__name__ + "was called"
    return func(*args, **kwargs)
    return inner_function

@decorator
def foobar(x):
    """foobar docstring"""
    return x**2 
```

Enter fullscreen mode Exit fullscreen mode

如果我们尝试打印名称和文档字符串，我们会看到下面的

```
print foobar.__name__
print foobar.__doc__

inner_function
inner function docstring 
```

Enter fullscreen mode Exit fullscreen mode

上面的观察让我们得出结论，函数 foobar 正在被 inner_function 所取代。这意味着我们丢失了正在传递的函数的信息。functools.wraps 来拯救我们了。它采用装饰器中使用的函数，并添加了复制函数名、文档字符串、参数网络等功能。让我们装饰而不丢失信息:

```
from functools import wraps

def wrapped_decorator(func):
    """wrapped decorator docstring"""
    @wraps(func)
    def inner_function(*args, **kwargs):
    """inner function docstring """
    print func.__name__ + "was called"
    return func(*args, **kwargs)
    return inner_function

@wrapped_decorator
def foobar(x):
    """foobar docstring"""
    return x**2

print foobar.__name__
print foobar.__doc__

foobar
foobar docstring 
```

Enter fullscreen mode Exit fullscreen mode

上面的实现保存了传递给装饰器的函数的信息。

你如何在基于类的装饰器中缓存信息？

[这里列出了一种方法](https://twitter.com/GrahamDumpleton)，希望在评论中看到更多的实现。

## 装饰者用论据

带参数的函数装饰器

```
from functools import wraps

def decorator(arg1, arg2):

    def inner_function(function):
    @wraps(function)
    def wrapper(*args, **kwargs):
        print "Arguements passed to decorator %s and %s" % (arg1, arg2)
        function(*args, **kwargs)
    return wrapper
    return inner_function

@decorator("arg1", "arg2")
def print_args(*args):
    for arg in args:
    print arg

 print print_args(1, 2, 3)

 Arguements passed to decorator arg1 and arg2
 1
 2
 3 
```

Enter fullscreen mode Exit fullscreen mode

## 基于类的装饰器与参数

```
class ClassDecorator(object):

    def __init__(self, arg1, arg2):
    print "Arguements passed to decorator %s and %s" % (arg1, arg2)
    self.arg1 = arg1
    self.arg2 = arg2

    def __call__(self, foo, *args, **kwargs):

    def inner_func(*args, **kwargs):
        print "Args passed inside decorated function .%s and %s" % (self.arg1, self.arg2)
        return foo(*args, **kwargs)
    return inner_func

@ClassDecorator("arg1", "arg2")
def print_args(*args):
    for arg in args:
    print arg

 print_args(1, 2, 3)
 Arguements passed to decorator arg1 and arg2
 Args passed inside decorated function .arg1 and arg2
 1
 2
 3 
```

Enter fullscreen mode Exit fullscreen mode

你将如何实现带有可选参数的 decorator？

试着按照这个[去贴](https://stackoverflow.com/questions/3888158/making-decorators-with-optional-arguments)。

您可能想进一步探索一下 [Wrapt 库](http://wrapt.readthedocs.io/en/latest/decorators.html)。

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/python-decorator-tutorial-with-example.html)上。