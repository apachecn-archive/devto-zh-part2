# Python 中的数据结构:字典

> 原文：<https://dev.to/georgeoffley/data-structures-in-python-dictionaries-3i1h>

*这篇文章也可以在我的博客上找到[这里](https://www.georgeoffley.com/new_dev/2018/07/15/data-structures-python-dictionaries/)T3】*

**简介**

我不会用一个花哨的复仇者名单来说明我的观点。我还没从结局中恢复过来。另外，他们杀死的大多数复仇者都是有色人种。当我摇头时，我唯一的想法是，显然灭霸也是种族主义者。该死的！我的数据类型系列的下一部分是处理 Python 中的字典。

字典在很多情况下都很有用。它在功能上类似于一个微型数据库，在运行时创建您的值之间的小型关系模型。大多数多语言开发人员将它们称为关联数组。我们不用数字来索引我们的值，而是用键。这些键可以是任何不可变的对象类型、字符串、数字或元组。键值对是考虑字典的一种简单方式。我们可以用字典做很多事情。当我们需要对列表进行排序时，可以使用键直接访问值。

**创建字典**

创建字典就像创建列表一样简单:

```
a = {}
print (a) 
```

在这里，我们指定字典的名称，并使它等于右括号。这为我们提供了以下信息:

```
{} 
```

**向词典添加条目**

创建列表很容易。向列表中添加条目也同样简单。我们需要为计算机节点名创建一个字典。我们需要能够创建计算机对象(我们的密钥)，并为它们分配将在数据库中使用的工作站名称(我们的值)。

```
computer_list = {}

computer_list["Comp1"] = "Station1"
computer_list["Comp2"] = "Station2"
computer_list["Comp3"] = "Station3"
computer_list["Comp4"] = "Station4"
computer_list["Comp5"] = "Station5"

print (computer_list) 
```

这里我们使用我们的左括号和右括号来创建字典。然后，我们引用字典，并用方括号添加我们的关键字，用双引号括起来。这里有引号，因为我们使用字符串作为键。然后我们把它设为等于我们的值。最后，我们打印我们的列表，以确保我们的工作被保存。

```
{'Comp1': 'Station1', 'Comp2': 'Station2', 'Comp3': 'Station3', 'Comp4': 'Station4', 'Comp5': 'Station5'} 
```

**长度**

现在我们有了列表，我们可以使用我们为字典列表使用的相同函数来查找条目的数量。通常对于循环目的，具有列表或字典的长度是有用的。

```
print (len(computer_list)) 
```

我们使用 len()函数来计算计算机列表中的条目总数。

```
5 
```

**删除条目**

所以我们需要删除列表中的一个条目。原来其中一台电脑被移动了，他们不需要它。

```
del computer_list['Comp5']

print (computer_list) 
```

要删除条目，我们只需使用 del()函数，然后引用字典名和条目的键来删除它。

```
{'Comp1': 'Station1', 'Comp2': 'Station2', 'Comp3': 'Station3', 'Comp4': 'Station4'} 
```

**基本循环**

使用这种类型的数据类型的内在价值是能够遍历我们的字典来使用这些值执行各种任务。这里我将为我们的字典举例说明一个简单的循环。

现在我需要能够打印出我们的计算机及其工作站名称的列表。为此，我们构建了一个简单的循环:

```
for k, v in computer_list.items():
    print(k,v) 
```

让我们快速浏览一下。这里我们在 for 循环中命名了两个占位符来代替我们的键和值。我们还引入了一个名为 items()函数的新函数。items()函数用于遍历字典条目。把这些放在一起，我们就有了我们的清单。

```
Comp1 Station1
Comp2 Station2
Comp3 Station3
Comp4 Station4 
```

**枚举**

*更新*
居然有更新。澄清一下，没有数字索引，只有枚举函数给我们一个数字列表中的值列表。它们实际上不能用来获取键/值。试了几次以确保。感谢[安德鲁](https://dev.to/gbm001)纠正我。我很高兴我们可以利用这些帖子来进行真正的讨论。

```
for a, b in enumerate(computer_list):
    print (a, b) 
```

所以我们做了和上面一样的事情，得到了下面的结果。每个值向我们显示字典条目的编号索引。

```
0 Comp1
1 Comp2
2 Comp3
3 Comp4 
```

**真实世界示例**

因此，创建计算机名称并分配不同的工作站并不是巧合。最近，我不得不在工作中创建一个项目，要求我拥有运行数据库条目应用程序的计算机的工作站名称。可追溯性要求我们知道我们的质量人员在哪里使用应用程序。所以我做的是用程序启动时加载的实际计算机名创建一个字典，并根据计算机名为条目分配站号。

为此，我决定稍微编辑一下。小程序的目的是输入节点名，并让程序根据节点名或键输出站名或值。

```
node_name = input("Enter computer name:\n")
node_name = node_name[0].upper() + node_name[1:]

if node_name in computer_list:
    print (computer_list.get(node_name))
else:
    print("Please use a designated computer for this program") 
```

因此，第一部分是创建输入，然后确保第一个字母是大写的，以符合我们预先确定的键。然后 get 创建 if 语句并使用 get()函数在我们的列表中查找节点名。get()函数将一个键作为参数，并在字典中搜索该键。然后它输出那个键的值。如果找不到密钥，我们会收到一条错误消息，提示我们的测试人员需要使用指定的测试站。这也使得只有某些工作站可以运行该应用程序。

```
Enter computer name:
comp2
Station2 
```

**结论**

正确使用字典是有效的。它们提供了在运行时跟踪数据或变量的简单方法。下次我们将深入研究另一种数据结构。可能是哈希表。

源代码可以在[这里](https://github.com/georGEO1989/Data_structures_Dictionaries/blob/master/source.py)找到

更多关于字典的信息可以在这里找到