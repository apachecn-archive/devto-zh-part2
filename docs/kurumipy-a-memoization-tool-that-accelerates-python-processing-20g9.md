# KurumiPy:一个加速 Python 处理的记忆工具

> 原文：<https://dev.to/yoheiseki/kurumipy-a-memoization-tool-that-accelerates-python-processing-20g9>

我们开发了一个工具“KurumiPy ”,可以很容易地适应 Python 中的内存化，并作为 OSS 发布给 GitHub。

当您再次运行程序时，Kurumipy 也可以使用缓存。

[https://github.com/FujitsuLaboratories/kurumipy](https://github.com/FujitsuLaboratories/kurumipy)

# 什么是记忆化？

在维基百科中是这样写的(2018 年 10 月 7 日)。

> 在计算中，记忆化是一种优化技术，主要用于通过存储昂贵的函数调用的结果并在相同的输入再次出现时返回缓存的结果来加速计算机程序。

在一个函数中，对相同的输入数据(参数值)多次重复具有相同返回值的过程是低效的。

因此，缓存(保存)第一次函数处理的结果。对于第二次和随后的时间，程序不在该函数中进行处理，而是按原样返回缓存的数据。

这使得省略多次重复相同的过程成为可能，因此处理速度相应地加快。

# 为什么做 KurumiPy？

如果您正在开发一个处理量不大的系统，那么内存化的需求就很低。

不过最近经常说 AI 和数据分析。按照目前的趋势，使用 Python 处理大量数据的情况越来越多。

例如，如下图所示，有一个系统以大量传感器数据作为输入，处理大量数据。

[![KurumiPy Description<br>
](img/0e693f7fe16ff912b4dc4b1244886066.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zwtcpoKf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fdehqf9f6fekm0duj5mt.png)

在这种情况下，如果您稍微更改程序，尝试通过执行程序来查看输出结果，将再次执行未重写程序的具有数据转换处理的功能。

比如改变上图“数据转换处理 C”的函数中的程序，重新执行时，“数据预处理”、“数据转换处理 A”、“数据转换处理 B”的函数部分，即使重复使用之前运行的结果，结果也是一样的。

在这种情况下，通过重新使用由存储器化缓存的数据，可以省略“数据转换处理 C”之前的处理，并且可以快速输出输出结果。

在数据量很小的情况下，可能不需要这样做，但如果输入数据变得超过几兆字节，这是值得的。

我正在开发一个特定的系统，但是在那个系统中，处理数据和输出结果需要 40 多秒。

但是，通过在 KurumiPy 中应用 memoization，输出结果用了不到 1 秒钟。

在开发一个通过试错法进行数据分析处理的系统时，有时有时会想稍微改变一下变量值，重新执行一下看看结果。

在这样的情况下，如果每次输出结果都要花很多秒，那压力就大了。

那时，KurumiPy 正在扮演一个积极的角色。

## 如果使用数据库缓存...

如果您很好地使用 DB(数据库)进行优化，您就不必多次执行相同的过程。

然后，你就不需要记忆工具了。

但是，在准备假设验证阶段或原型时，准备 DB 是很麻烦的。

就算用 DB，可能也很难优化好。

那时，我想要一个记忆。

# 如何使用 KurumiPy

支持的 Python 版本是 3.x。

例如，请预先安装依赖包[“紧固件 0 . 14 . 1”](https://pypi.org/project/fasteners/)，如下所示。

```
pip install fasteners==0.14.1 
```

将“memoization”文件夹复制到您的项目中，然后导入模块。给函数写一个装饰来实现记忆。

```
from memoization.memo_decorator import memo

@memo
def your_function(n):
    # ... 
```

你可以单独应用记忆。

缓存文件将存储在下面的文件夹中。

**【的。/memoization/memocache]**

*   你的功能的变化
    *   当您更改目标函数的实现时，KurumiPy 会自动使缓存失效。它对于测试驱动的开发很有用。
*   因变量的变化
    *   当目标函数的任何因变量发生变化时，KurumiPy 会自动使缓存失效。如果您修改参数并重新执行您的程序，这将非常有用。

否则，随着上述文件夹中积累的缓存越来越多，必要时请删除上述文件夹的缓存文件。

## 限制

函数应满足以下限制。

*   纯函数
*   参数有字符串类型、数值类型等。(不支持:列表类型、字典类型、集合类型、文件对象等。)
*   不支持:多线程中的相互递归函数(用餐哲学家问题)。

## 其他设置

Python 3.3 默认情况下在字符串、字节和日期时间对象的散列上启用“salt”。

Salt 防止进程间的哈希值一致。

您需要在运行 Python 程序之前禁用它。

```
# bash
export PYTHONHASHSEED=0

# Command prompt
set PYTHONHASHSEED=0 
```

# 为什么叫“KurumiPy”？

Kurumi 在日语中代表核桃。

他们说核桃能提高记忆力。

为此，我们命名为“Kurumi ”,因为它符合记忆化。

让我们用 KurumiPy 智能高效地开发吧！