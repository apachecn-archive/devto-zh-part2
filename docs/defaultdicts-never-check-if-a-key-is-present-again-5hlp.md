# 默认规则:永远不要再检查一个键是否存在！

> 原文：<https://dev.to/rpalo/defaultdicts-never-check-if-a-key-is-present-again-5hlp>

由 Romain Vignes 在 Unsplash 上拍摄的封面图片。

秋天到了，所以这似乎是写一些关于 PSL 的帖子的最佳时机。不，不是南瓜香料拿铁 Python 标准库！标准库是 Python 附带的一堆有用的模块(包括电池！)，所以当您将它们引入到代码中时，几乎没有安全/维护负担！

当我在 exercisem 上指导 Python 新手时，有几个练习很大程度上使用了`dicts`。在其他语言中，你可能知道他们是`hash`、`hashmap`或`dictionary`。不管你怎么称呼它们，我说的是将一个值，一个**键**映射到另一个值的数据类型，通常简称为**值**。

这种数据结构非常适合通过键来选择特定的项目进行快速读写。它通常用于标记和收集数据，用于学校花名册、单词计数器等用例，或者作为对象的替代品，带有命名字段(尽管在 Python 中，有一些比普通的 ole' dict 更好的选择。但那是另一篇文章了)。

由于这些用例，您经常会看到需要这样做的代码:

```
grades = ["A", "A", "B", "C", "D", "C", "B", "C", "A", "C", "B"]

counter = dict()
for grade in grades:
    if grade in counter:
        counter[grade] += 1
    else:
        counter[grade] = 1 
```

你需要检查那个特定的分数字母是否在`counter`字典中，然后再给它加 1。否则你会遇到错误！

这里*是*一个相当清晰易读的变通方法:我们的朋友`get`。

```
for grade in grades:
    counter[grade] = counter.get(grade, 0) + 1 
```

默认情况下，如果找不到键，`dict.get(key)`将返回`None`，而不是像平常一样引发`KeyError`、*，但是*如果您将第二个参数添加到`get`调用中，它将返回默认设置，允许您在一行中更新或创建键。

*但是瑞恩，*你说。*这看起来像是一个*可怕的*当一个键丢失时，仅仅让一个字典为我提供一个默认值就要做很多工作！你说得对！*

有一个更好的方法。

我给你介绍一下 [**集合模块**](https://docs.python.org/3/library/collections.html) 。查看文档。这里有更多好东西(尤其是真正的`Counter`类，它解决了我们这里的问题。但是，这是另一篇 PSL 的文章)。具体来说，我们正在研究`defaultdict`类。下面是它如何帮助我们解决上面的问题。

```
from collections import defaultdict

counter = defaultdict(int)

for grade in grades:
    counter[grade] += 1 
```

它是这样工作的。`defaultdict`构造函数接受一个参数:一个函数(或其他可调用函数)。注意我们没有*调用函数参数*(`int`)，所以`int`后面没有括号，我们传递函数`int`本身。

每当 defaultdict 遇到一个它以前没有见过的键时，它不是引发一个`KeyError`，而是调用你给它的那个函数，在它自己内部创建这个新的**键**，并将函数调用的结果作为这个键对的**值**。这里的神奇之处在于，你通常用来将字符串或浮点数转换成整数的`int`函数，实际上在你不向它传递任何东西时，它会返回`0`。

```
>>> int("5")
5
>>> int()
0 
```

你可能会问自己，其他的原始构造函数会做同样的事情吗？你打赌你的小家伙会的！

```
>>> str()
''
>>> list()
[]
>>> float()
0.0
# And EVEN >>> complex()
0j 
```

实际上，这个非常有用。假设你想把东西放进垃圾箱:

```
from collections import defaultdict

school_roster = defaultdict(list)

# Nico is in 2nd grade school_roster[2].append("Nico")

# So is Lester school_roster[2].append("Lester")

# Laura is in 5th grade school_roster[5].append("Laura")

school_roster
# => defaultdict(list, {2: ['Nico', 'Lester'], 5: ['Laura']}) 
```

但是如果你想做一个自定义的构造函数呢？如我所说，它适用于任何不带参数并返回某个值的可调用函数！

```
def happy_constructor():
    return ":)"

happy_dict = defaultdict(happy_constructor)

happy_dict["Ryan"]
# => ":)" 
```

不过，更常见的情况是，如果构造函数像这样简短，您会看到它以`lambda`的形式出现，这只是一种制作快速、简短、一次性匿名函数的奇特方式。

```
happy_dict = defaultdict(lambda: ":)")
happy_dict["You"]
# => ":)" 
```

如果您的自定义类在其`__init__`方法中没有任何参数，您也可以传入您自己的类。

```
class Dog:
    def __init__(self):
        self.treats = 0

    def woof(self):
        return "Woof!"

hotel_for_dogs = defaultdict(Dog)
hotel_for_dogs["Fifi"].woof()
# => "Woof!" 
```

这门课可以让你做很多很酷的事情。你有什么很酷的使用方法吗？我想了解他们！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/11/18/defaultdicts/)*