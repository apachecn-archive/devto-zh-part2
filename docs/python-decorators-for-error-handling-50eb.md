# 用于错误处理的 Python 装饰器

> 原文：<https://dev.to/booterror/python-decorators-for-error-handling-50eb>

所以我有一个小 python 脚本，它从某个网站上截取产品数据。几个获取数据的函数，分析一种类型的内容，另一种类型的数据。

现在我遇到了这个问题，css 选择器返回 None，这破坏了我所有偷偷摸摸的列表索引。

目前我的程序是这样的。

```
 def get_x_info(url):

# piece of code that works all the time 
# piece of code that crashes 
   return result 
```

将`try and except`应用于崩溃的代码片段并不十分令人信服，因为对所有可能的易变代码做这样的事情是一项乏味的任务。我不确定哪一部分会崩溃。

于是， **Python Decorators** 。

用可重用代码包装函数的语法糖。

了解一下[这里](https://realpython.com/primer-on-python-decorators/)

下面是我如何使用 decorators 错误处理大多数时候抛出错误的函数。

```
def safe_run(func):

    def func_wrapper(*args, **kwargs):

        try:
           return func(*args, **kwargs)

        except Exception as e:

            print(e)
            return None

    return func_wrapper 
```

现在将`safe_run`作为装饰器应用于主代码
中的函数声明

```
 @safe_run
def get_x_info(url):
.... 
```

*快速有效的可怕修复:)*