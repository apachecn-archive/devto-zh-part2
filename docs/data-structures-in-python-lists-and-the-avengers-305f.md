# Python 中的数据结构:列表和复仇者

> 原文：<https://dev.to/georgeoffley/data-structures-in-python-lists-and-the-avengers-305f>

*也出现在我的个人博客上[这里](https://www.georgeoffley.com/new_dev/2018/05/01/data-structures-python-lists-avengers/)T3】*

**简介**

好吧，我看了无限战争，太棒了。太震撼了，太可笑了，让我哭了。放松，有一些剧透，但有一个警告。尽管如此，我还是要为我的 python 数据结构的例子偷一些复仇者。我不太熟悉 python 拥有的所有数据结构。作为一个挑战，我想出了一个深入研究 Python 中的数据结构的计划。本系列的第一篇文章是 python 列表。

数据结构是存储和处理数据的方法。因为 python 是比 C 语言更高级的语言，所以我们不受内存管理和垃圾收集的影响。尽管如此，通过索引过程创建一个类似列表的东西确实可以分配内存地址。不过我们会详细讨论这一点。

**原始与非原始数据结构**

让我们来看看 python 是如何对数据进行分类的。原始数据结构是在编程语言中处理的结构。它们是 python 中的 int、floats 和 strings。它们也被称为数据类型。python 中的非原始数据类型指的是我们的数据结构。列表、元组、集合和字典。它们是程序员创造的结构。因此，当我们索引这些条目并为列表中的条目分配内存位置时。python 很难公开内存位置。对于高级语言来说，这真的不是你需要考虑的事情。然而，在我看来，了解编程语言的底层机制是很重要的。

**列表介绍**

列表是我们在列表中存储值的方式，通过索引每个值来引用。下面是一个列表示例:

```
list = [1,2,3]

print (list) 
```

现在我们运行我们的列表

```
[1, 2, 3] 
```

我们的列表是一组数字，每个数字都有自己的索引。我们可以列出一组值，例如名称、日期、价格。我们的列表有许多应用程序，其中一个应用程序是一组字符串，用于跟踪加入复仇者联盟的英雄。这是第一部电影的第一轮复仇者联盟。

```
avengers = ['Iron Man','Captain America','Black Widow','The Hulk','Hawkeye','Thor', 'Falcon']

print ("Hero List example: ")
print (avengers) 
```

然后我们运行代码，得到:

```
Hero List example:
['Iron Man', 'Captain America', 'Black Widow', 'The Hulk', 'Hawkeye', 'Thor', 'Falcon'] 
```

如上所示，我们可以将我们的列表打印成一个单一的列表。我们还可以利用 for 循环来逐个遍历列表。通常，如果我们对这个列表做任何事情(在屏幕上打印，添加到数据库)，我们会使用 for 循环来遍历这个列表

```
print ("For Loop Example: ")
for x in avengers:
    print (x) 
```

然后我们运行代码:

```
For Loop Example:
Iron Man
Captain America
Black Widow
The Hulk
Hawkeye
Thor
Falcon 
```

**堆叠**

我们可以用许多不同的方式使用列表。我们也可以用一些方法来“堆叠”我们的列表。遵循一个后进先出的方法，我们可以为我们的列表使用 append 和 pop 函数来添加和删除人员。看过《美国队长:内战》的人看到阵容中又多了两个英雄。

```
avengers.append('Black Panther')

print ("Append Example: ")
for x in avengers:
    print (x) 
```

这给了我们

```
Append Example:
Iron Man
Captain America
Black Widow
The Hulk
Hawkeye
Thor
Falcon
Black Panther 
```

现在，我们都知道谁是黑豹。他要管理一个国家。他没有时间坐在复仇者联盟周围，等待可怕的事情发生。一旦 T'challa 加入，他必须后退，在他父亲死后填补瓦坎达国王的角色。

```
avengers.pop()

print ("Pop Example: ")
for x in avengers:
    print (x) 
```

这给了我们:

```
Pop Example:
Iron Man
Captain America
Black Widow
The Hulk
Hawkeye
Thor
Falcon 
```

堆叠就是这样工作的。append 方法将在列表末尾添加一个条目。带有的 pop 方法从列表中移除一个条目。pop 方法的不同之处在于，您可以指定要删除的值的索引(位置),它将删除该值。如果留空，弹出功能将删除列表中的最后一个条目。

**索引**

索引是您跟踪值及其在列表中的顺序的方式。我们从零开始向上计数，这将给出我们入口的位置。让我们以黑寡妇为例。除了期待已久的单人原创电影，我们还需要找到她在名单上的位置。

```
print (avengers.index('Black Widow')) 
```

将该命令放入 python 解释器，并获取 2 或列表中的第三个位置。

```
2 
```

**更好的价值管理**

所以第一个开始意识到他们不只是那么喜欢复仇者联盟的是美国队长。假设我们不知道 pop 函数是如何工作的，也不知道船长在我们的列表中的位置。我们可以使用 remove 函数来删除它。

```
avengers.remove('Captain America')

print ("Remove Example: ")
for x in avengers:
    print (x) 
```

该函数查找并删除给定的条目。如果该值不存在，我们将返回一个错误。

```
Remove Example:
Iron Man
Black Widow
The Hulk
Hawkeye
Thor
Falcon
Black Panther 
```

随着盖帽和钢铁侠的出局，我们让黑豹加入了阵容。然而，钢铁侠也需要另一只手把罗杰斯队长和他的同伴们带回来。最佳电影版蜘蛛侠来了。让我们把他加到名单上。比如我们想让蜘蛛侠成为复仇者联盟的新面孔，所以我们想让他把他放在列表的第一位。我们可以使用 insert 函数来实现这一点。这里，我们传入希望值出现的索引位置和值。

```
avengers.insert(0, 'Spider Man')

print ("Insert Example: ")
for x in avengers:
    print (x) 
```

如果我们再打印一次我们的列表，我们会看到蜘蛛侠出现在最上面。对此我没意见。汤姆·赫兰德是一个出色的蜘蛛侠/彼得·帕克。

```
Insert Example:
Spider Man
Iron Man
Captain America
Black Widow
The Hulk
Hawkeye
Thor
Falcon
Black Panther 
```

有多少蜘蛛侠？

所以漫画世界有十几个不同的蜘蛛侠变种。有彼得·帕克蜘蛛侠，我最喜欢的迈尔斯·莫拉莱斯蜘蛛侠，和可怕的蜘蛛格温。MCU(漫威电影宇宙)目前只有一个蜘蛛侠。然而，让我们数数我们的名单上有多少是安全的。

我们可以为我们的列表使用 count 函数来计算一个值在列表中出现的次数。

```
how_many_spider_mans = avengers.count('Spider Man')

print ("Count Example Example: How Manny Spider Men are there?")
print (how_many_spider_mans) 
```

MCU 只有一个，所以我们的列表只有一个蜘蛛侠值。所以我们得到了一个返回给我们的。

```
Count Example Example: How Manny Spider Men are there?
1 
```

**嵌套**

我们都喜欢第一个银河护卫队。出色的视觉效果，幽默的对话，当然还有 70 年代音乐和太空探险的恰当结合。他们在漫威宇宙中除了地球上的复仇者之外，都是单独存在的。然而，在《无限战争》中，它们都融合成了 MCU 中最好的对话。所以我们有了地球上的英雄名单。现在又来了一个很酷的团队加入他们。让我们创建新的列表。

```
gotg = ['Star Lord', 'Rocket', 'Groot', 'Drax', 'Gamora'] 
```

现在我们有了新名单，我们需要想办法把他们加入复仇者联盟。Python 嵌套列表拯救了。使用 python 列表，可以在列表中嵌套列表。这个是有应用的。例如，将相关信息放在一个列表中会非常有用。然而，在这个特殊的例子中，使用类可能更好。稍后会详细介绍。所以让我们把守卫者加入复仇者的名单。

```
avengers.append(gotg)

print ("Append Nested List Example: ")
for x in avengers:
    print (x) 
```

在打印出我们的名单后，我们看到我们有了守护者的名单以及其他复仇者的名单。干得好，凯文·费奇！

```
Append Nested List Example:
Iron Man
Captain America
Black Widow
The Hulk
Hawkeye
Thor
Falcon
Black Panther
['Star Lord', 'Rocket', 'Groot', 'Drax', 'Gamora'] 
```

**警告前方剧透！！**

这是一个警告。如果你没有看过复仇者联盟 3：无限战争，请跳过这一步。这是一个巨大的结局，如果你真的关心这部电影，请随意跳到结尾。已经警告过你了。

**说真的，前面有剧透。太棒了。**

好吧，我警告过你了。对于我们这些看过无限战争的人来说，我们都知道它的结局。令人心碎。我看的完全惊呆了。我还在处理。在结局中，复仇者大多被杀光。不是所有的人，但有相当一部分。所以现在我们需要更新我们的列表。在这里，我们可以使用清除功能来清除我们的复仇者名单，因为他们中的大多数已经不存在了。列表中的一些仍然存在于 MCU 中，但是，使用电影的结尾更适合这种方法。

```
avengers.clear()

print ("Clear Example: ")
print (avengers) 
```

哪张照片

```
Clear Example:
[] 
```

现在当我们打印我们的列表时，我们得到一个空列表。我还是没缓过来。通常，这些电影远没有情感上的冲击力。自从《黑豹》这样的电影问世以来，这种情况发生了变化，它以一种微妙而有趣的方式处理了巨大复杂的主题。我很高兴看到电影继续提高标准。

**结论**

我希望与《复仇者联盟》的合作有助于分解如何使用列表以及如何在 python 程序中使用列表。这种数据结构有许多不同的应用。然而，在有些应用中，列表可能是有意义的，但是不同的数据结构可能更合适。在本系列接下来的部分中，我们将探索其他数据结构。我很期待看到我们能做些什么。

完整的源代码可以在这里找到:[https://github.com/georGEO1989/Data-Structures-Lists](https://github.com/georGEO1989/Data-Structures-Lists)