# Python 中的 Dict 移动

> 原文：<https://dev.to/rpalo/dict-moves-in-python-5b6i>

快速提示时间！

今天，我又开始了#100DaysOfCode 挑战(第一百万次)。我决心在这次挑战中取得成功，我拒绝放弃。这一次，我使用的是 [Python Bytes Code Challenges 网站](https://codechalleng.es/)和他们的 100 天项目建议。在今天的挑战中，我学到了一个使用字典的小技巧，我想和大家分享一下。

## 挑战

挑战是这样的:浏览一个[单词字典](https://raw.githubusercontent.com/rpalo/100DaysOfCode/master/001/dictionary.txt)，它实际上只是一个`/usr/share/dict/words`的副本。使用这些字母分数，找出拼字游戏中得分最高的单词:

```
SCRABBLE_SCORES = [
  (1, "E A O I N R T L S U"),
  (2, "D G"),
  (3, "B C M P"),
  (4, "F H V W Y"), 
  (5, "K"), 
  (8, "J X"), 
  (10, "Q Z"),
]
LETTER_SCORES = {
    letter: score for score, letters in scrabble_scores
    for letter in letters.split()
}
# {"A": 1, "B": 3, "C": 3, "D": 2, ...} 
```

Enter fullscreen mode Exit fullscreen mode

## 这个问题

问题是我不想担心输入中是否有任何无效字符(至少现在是这样)。所以如果我查找单词“snoot！43@@@”，现在，我更希望看到 SNOOT 的分数，然后是其余角色的 0 分。我知道有很多方法可以做到这一点，但我想到的第一个方法是使用默认为 0 的*(也就是说，如果你试图查找一个不在`LETTER_SCORES`中的字符，它会返回 0，而不是产生一个`KeyError`。)*

 *## 输入默认字典

幸运的是，Python 提供了我们需要的东西:一个标准库的`collections`模块提供的`defaultdict`。它的用法相当简单:如果找不到输入，就给`defaultdict`提供一个构造默认值的类或函数。让我展示给你看。

```
from collections import defaultdict

zeros = defaultdict(int)
zeros["a"] = 1
zeros["b"] = zeros["definitely not in there"] + 4
print(zeros)
# => defaultdict(<int>, {"a": 1, "b": 4, "definitely not in there": 0}) 
```

Enter fullscreen mode Exit fullscreen mode

由于`zeros`字典找不到`"definitely not in there"`键，它调用它的默认生成器函数`int`。继续打开你的 Python REPL，尝试不带参数地调用`int`函数。

```
>>> int()
0 
```

Enter fullscreen mode Exit fullscreen mode

不带参数调用的`int`函数每次都返回 0。

你甚至可以创建自己的默认 maker 函数(类也可以)！

```
from random import choice

def confusing_default():
    possibles = ["1", 1, True, "banana"]
    return choice(possibles)

tricky_dict = defaultdict(confusing_default)
tricky_dict["Ryan"]
# => "banana" tricky_dict["Python"]
# => True tricky_dict["Why would you do this?"]
# => 1 tricky_dict
# => defaultdict(<confusing_default>, {"Ryan": "banana", "Python": True, "Why would you do this?": 1}) 
```

Enter fullscreen mode Exit fullscreen mode

很多时候，你可以用`lambdas`更快地做事情。

```
from random import randint

SCREAMING = defaultdict(lambda: "A")
for i in range(20):
    key = randint(0, 3)
    SCREAMING[key] += "A"
SCREAMING
# => defaultdict(<function <lambda> at 0x108707f28>, {0: 'AAAAAAAA', 1: 'AAAAAAA', 3: 'AAAAA', 2: 'AAAA'}) 
```

Enter fullscreen mode Exit fullscreen mode

事实上，我真的认为使用`defaultdict(lambda: 0)`比使用`defaultdict(int)`更明确，更少混乱，只要你不是用这种方式创建大量的这些`defaultdicts`。

## 升级到默认字典

现在，我们终于准备好快速提示了。在上面，我将`LETTER_SCORES`定义为一条普通的旧 Python `dict`。如何快速获得我想要的默认行为？一种方法是使用内置的`dict.update()`函数，合并两个字典。

```
FORGIVING_SCORES = defaultdict(lambda: 0)
FORGIVING_SCORES.update(LETTER_SCORES)

FORGIVING_SCORES["Q"]
# => 10 
FORGIVING_SCORES["@"]
# => 0 
```

Enter fullscreen mode Exit fullscreen mode

万岁！

当然，这不是一个完美的解决方案，因为`FORGIVING_SCORES` defaultdict 存储了每个无效的请求。如果你不希望看到大量的无效查询，这可能是没问题的。如果你*是*担心保持空间效率，那么这样做可能更好:

```
score = LETTER_SCORES.get("@") or 0 
```

Enter fullscreen mode Exit fullscreen mode

如果出现`KeyError`，函数`get`返回`None`，如果查找失败，函数`or`允许我们提供一个合理的默认值。每个人都很开心！

*编辑 2018 年 4 月 9 日:【T2:】正如[列图公爵](https://dev.to/lietux)指出的，你可以更简单地通过为`get`提供一个缺省值:
来做到这一点*

```
score = LETTER_SCORES.get("@", 0) 
```

Enter fullscreen mode Exit fullscreen mode

## 总结起来

因此，事实证明，这篇博文的全部原因并不是最初问题的最简单解决方案。也就是说，希望你能对`defaultdict`的工作方式和`dict.update`的方法有更多的了解。

感谢阅读！

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/04/08/convert-to-default-dict/)**