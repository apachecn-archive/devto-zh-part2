# 没有全局或非局部的 Python 函数闭包作用域

> 原文：<https://dev.to/msoedov/-python-functional-closure-scopes-without-global-or-nonlocal-pg2>

python 或任何嵌套封闭函数中的典型装饰器通常指的是上面一个或多个变量作用域中的状态。

```
 def wrapper():

    counter = 0

    def incr():
        counter += 1
        return counter
    return incr

fn = wrapper()
print(fn())

# Traceback (most recent call last):
#   File "sample.py", line 51, in <module>
#     print(fn())
#   File "sample.py", line 46, in incr
#     return counter
# NameError: name 'counter' is not defined 
```

Enter fullscreen mode Exit fullscreen mode

就像这个例子中的计数器变量。代码将无法执行，因为`counter`在`incr`函数范围内不可见。

典型解决方法通常是将计数器声明为非局部的，甚至是全局的。

```
 def wrapper():

    counter = 0

    def incr():
        nonlocal counter
        counter += 1
        return counter
    return incr

fn = wrapper()
print(fn())

# >> 1 
```

Enter fullscreen mode Exit fullscreen mode

这和预期的一样有效，但是另一种方法能达到同样的效果吗？

```
 def wrapper():
    v = dict(counter=0)

    def incr():
        v['counter'] += 1
        return v['counter']
    return incr

fn = wrapper()
print(fn())

# > 1 
```

Enter fullscreen mode Exit fullscreen mode

作为第一个技巧，我们可以使用像 dict 这样的可变对象来声明和修改状态，而不使用非本地关键字。为什么有效？与前面的例子`counter += 1`相比，这实际上是针对`counter = counter + 1`的语法糖，python 解释器通常不知道如何解析将这个新的计数器变量重新分配给哪个作用域。然而，如果只是通过对象引用进行变异，则不存在这种模糊性

另一个技巧来自前面的例子，但我发现它更易读、更简洁。

```
 def wrapper():
    class Vars:
        counter = 0

    def incr():
        Vars.counter += 1
        return Vars.counter
    return incr

fn = wrapper()
print(fn())
# >> 1 
```

Enter fullscreen mode Exit fullscreen mode

[原帖](https://medium.com/@msoedov/python-3-functional-closure-scopes-without-global-nonlocal-5630b172f764)