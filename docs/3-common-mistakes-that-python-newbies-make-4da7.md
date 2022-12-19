# Python 新手常犯的 3 个错误

> 原文：<https://dev.to/rpalo/3-common-mistakes-that-python-newbies-make-4da7>

上周末，我开始在 Python 赛道上指导人们进行[exercisem . io](https://exercism.io)训练。我不确定会发生什么，但在过去的一周里，我已经指导了大约 50 个人，帮助他们从“通过测试”到“通过测试，可读，*和*python”得到了他们的解决方案我被迷住了。完全是一场爆炸。我打算专门写一篇关于那次经历的帖子。那不是这个帖子。这篇文章将讨论我在上周看到的三个最常见的错误，以及一些可能更好的替代方法！所以让我们开始倒计时吧！

## 1。If 语句或循环的深层嵌套

```
# Calculating whether or not 'year' is a leap year 
if year % 4 == 0:
    if year % 100 == 0:
        if year % 400 == 0:
            return True
        else:
            return False
    else:
        return True
else:
    return False 
```

Enter fullscreen mode Exit fullscreen mode

很多时候，我会借鉴 Python 的[禅，将我的反馈引向“被辅导者”(不要与被辅导者混淆)。当我看到这个问题时，我总是以](https://www.python.org/dev/peps/pep-0020/#id3)

> 扁平的比嵌套的好。

如果你看代码时眼睛不集中，只看图形而不看文字，你会看到一串箭头出去又回来:

```
\
 \
  \
   \
    \
    /
   /
  /
  \
   \
    \
     \
     /
    /
   /
  /
 /
/ 
```

Enter fullscreen mode Exit fullscreen mode

这绝对不是一件坏事，但这是一种“代码味道”，或者说是一种蜘蛛的感觉，即某些东西可能会被重构。

那么，除了窝，还能做什么呢？有一些事情可以尝试。第一种是颠倒你的逻辑，用“早期回报”一次一个地剥离解决方案空间的小部分。

```
if year % 400 == 0:
    return True
if year % 100 == 0:
    return False
if year % 4 == 0:
    return True
return False 
```

Enter fullscreen mode Exit fullscreen mode

如果数字能被 400 整除，那么我们立即返回 true。否则，对于我们代码的其余部分，我们可以知道年份是*绝对不是*能被 400 整除。因此，在这一点上，任何能被 100 整除的年份都不是闰年。所以我们通过返回 False 来去掉这层洋葱皮。

在此之后，我们可以知道`year`绝对不是 400 *或* 100 的倍数，代码的剩余部分也遵循同样的模式。

另一种避免嵌套的方法是使用“布尔运算符”:`and, or, and not`。我们可以组合`if`语句，这样我们就省去了一层嵌套！

```
if year % 4 == 0 and (year % 100 != 0 or year % 400 == 0):
    return True
else:
    return False 
```

Enter fullscreen mode Exit fullscreen mode

当然，这就引出了我们的第二个项目...

## 2。从 If 语句返回布尔值

我们从上面的最后一个例子开始:

```
if year % 4 == 0 and (year % 100 != 0 or year % 400 == 0):
    return True
else:
    return False 
```

Enter fullscreen mode Exit fullscreen mode

任何时候你发现自己在写作:

```
if something:
    return True
else:
    return False 
```

Enter fullscreen mode Exit fullscreen mode

你应该记住一个`if`语句的子句本身就是一个布尔值！

```
>>> year = 2000
>>> year % 4 == 0 and (year % 100 != 0 or year % 400 == 0)
True 
```

Enter fullscreen mode Exit fullscreen mode

那么，为什么不少打一点，直接返回布尔运算的结果呢？

```
return (year % 4 == 0 and (year % 100 != 0 or year % 400 == 0)) 
```

Enter fullscreen mode Exit fullscreen mode

当然，在这一点上，这一行可能变得有点长，但是代码现在不那么冗余了！

## 3。清单就像锤子——不是所有的东西都是钉子

以下是这种情况出现的两种可能方式:

```
some_numbers = [1, 2, 5, 7, 8, ...]
other_numbers = [1, 3, 6, 7, 9, ...]
# Let's try to combine these two without duplicates for number in other_numbers:
    if number not in some_numbers:
        some_numbers.append(number) 
```

Enter fullscreen mode Exit fullscreen mode

或者:

```
data = [["apple", 4], ["banana", 2], ["grape", 14]]
# What fruits do we have? for item in data:
    print(item[0])
# => "apple" "banana" "grape"
# How many grapes do we have? for item in data:
    if item[0] == "grape":
        print(item[1])
# => 14 
```

Enter fullscreen mode Exit fullscreen mode

在第一种情况下，您试图跟踪一些项目组，并希望将它们组合在一起而不重复。这是一个*理想的*候选人一个 [`set`](https://www.geeksforgeeks.org/sets-in-python/) 。集合本身会跟踪它们的项目(尽管不是顺序，所以如果顺序很重要，就不要使用集合)。您可以用内置的`set()`函数或者用波形括号(`{}`)来声明它们。

```
some_numbers = {1, 2, 5, 7, 8}
other_numbers = {1, 3, 6, 7, 9}
# Sets use the 'binary or' operator to do "unions"
# which is where they take all of the unique elements some_numbers | other_numbers
# => {1, 2, 3, 5, 6, 7, 8, 9} 
# You can even add single items in! some_numbers.add(10)
# => {1, 2, 5, 7, 8, 10} 
# But adding a duplicate doesn't change anything some_numbers.add(1)
# => {1, 2, 5, 7, 8, 10} 
```

Enter fullscreen mode Exit fullscreen mode

在第二种情况下，顺序可能并不重要。您希望通过“标签”之类的东西来跟踪一些数据，但是能够将它们放在一起，并在必要时列出来。这一次，你可能在寻找一个`dict`。你可以用`dict()`内置函数创建它们，也可以用曲线括号(`{}`)创建它们。但是，这一次，您用冒号分隔标签(键)和值。

```
fruits = {
    "apples": 4,
    "bananas": 2,
    "grapes": 14,
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以列出所有的键(或值！).

```
list(fruits.keys())
# => ["apples", "bananas", "grapes"] list(fruits.values())
# => [4, 2, 14] 
# Or both! list(fruits.items())
# => [("apples", 4), ("bananas", 2), ("grapes", 14)] 
```

Enter fullscreen mode Exit fullscreen mode

您可以向它询问特定的键(或者给它一个新的值)。

```
# How many grapes are there? fruits["grapes"]
# => 14 
# Not anymore.  I ate some. fruits["grapes"] = 0

fruits["grapes"]
# => 0 
```

Enter fullscreen mode Exit fullscreen mode

使用一个列表，你的算法循环通过每一个项目，找到正确的。`dict`有非常快的查找速度，所以，即使你的`dict`有几十亿个水果长，找到`grapes`仍然非常快——而且很容易输入！没有循环！

## 行动号召

Exercism 需要导师！如果你认为你会是一个好的导师(或者甚至是一个体面的导师，只是在简单的练习中)，在他们的导师注册页面注册。现在，铁锈、戈朗和仙丹特别忙，需要*你的*帮助！