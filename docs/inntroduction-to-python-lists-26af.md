# Python 列表简介

> 原文：<https://dev.to/achiengcindy/inntroduction-to-python-lists-26af>

[![](../Images/676a9312b56240cfcf2501de41565bb3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6p2dhskM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1r60jwep68i2y5c0vg16.jpg)

List 是最通用的 python 数据结构，存储有序的元素序列，就像购物清单或待办事项清单一样。在 Python 中，列表是`mutable`，这意味着元素可以被改变，不像`tuples`甚至`strings`。`list`的这些元素被称为`items`，可以是任何数据类型。

### 用 Python 创建列表

用 python 创建列表非常简单，使用方括号[]并用逗号分隔列表中的项目。一个列表可以是空的，也可以包含任意数量的不同数据类型(整数、浮点、字符串等)的条目。).

```
mylist = [ ]  #empty list
mylist2 = [1,2]  # list containing 2 items with integers
mylist3 = [1, 2, 'hello']  # list with 3 items,mixed data types,integers and a string 
```

注意:一个列表也可以保存另一个列表作为一个项目，这些被称为`nested lists.`，如下所示。

```
mylist4 = [['python','php'],[1,2,3]  # a nested list 
```

### 访问列表中的项目

我们可以通过以下方式访问列表中的项目

1.  **索引**。
    我们使用`index`运算符[ ]。列表中的每一项都有一个指定的索引值。需要**注意的是**在 python 中索引从 0 开始，并且**必须是整数**

    | Java 语言（一种计算机语言，尤用于创建网站） | 大蟒 | 实际抽取与汇报语言 | 红宝石 | c# |
    | --- | --- | --- | --- | --- |
    | Zero | one | Two | three | four |

    **注意**:任何非空列表的第一项都是[0]。

    ```
    Languages = ['java', 'python', 'perl' 'ruby', 'c#' ]   # define a list
    print(Languages[0]) # Access the first item of a list at index 0
    # Output:java which is the first item on the list. 
    ```

2.  **负索引** Python 也支持负索引。当你想得到一个列表中的最后一项时，负索引是有用的，因为它是从末尾开始访问列表的。

    | Java 语言（一种计算机语言，尤用于创建网站） | 大蟒 | 实际抽取与汇报语言 | 红宝石 | c# |
    | --- | --- | --- | --- | --- |
    | -5 | -4 | -3 | -2 | -1 |

    > **注**:任何非空列表的最后一项都是[-1]。

    ```
    # define a list 
    Languages = ['java', 'python', 'perl' 'ruby', 'c#' ] 
     print(Languages[-1) #Access the last item of a list
    # Output:  C# 
    ```

**列表长度**

Python 有一些有用的内置函数来处理 list。我们稍后会讨论它们，但是现在，`len()`函数帮助我们返回列表中元素的总数。

```
# define a list
languages = ['java', 'python' ,'perl', 'ruby', 'c#']
# print the length of the list
print(len(languages)) 
# output 5 
```

### 列表切片

切片有利于获取列表中项目的子集。它使用`slicing`操作符:(冒号)提取部分序列。

```
Languages = ['java', 'python', 'perl','ruby', 'c#' ] # define a list 
# [:3] slicing everything up to but not including index 3
print(languages[:3])  
#Outputs: ['java', 'python', 'perl' ]

# define a list 
Languages = ['java', 'python', 'perl','ruby', 'c#' ]
# [3:] slicing everything from index 3 to the last item
print(languages[3:])
#Output: ['ruby', 'c#' ]

Languages = ['java', 'python', 'perl','ruby', 'c#' ]
#elements from beginning to end
print(languages[:])
#Output: ['java', 'python', 'perl','ruby', 'c#' ]

# define a list 
Languages = ['java', 'python', 'perl','ruby', 'c#' ]
# elements from 1st to 3rd
print(languages[0:3])
Output # ['java', 'python', 'perl'] 
```

### 如何改变/添加列表中的元素

**list.append(item)**

方法`list.append(item)`将在列表末尾添加`item`。

```
 # define a list
   languages = ['java', 'python' ,'perl', 'ruby', 'c#']
   # append c
   languages.append('c') 
   print (languages)
  # Output : ['java', 'python', 'perl', 'ruby', 'c#', 'c'] 
```

**list.insert(i，item)**

这个方法将在列表的第 I 个位置插入一个项目，将元素向右移动。

```
# define a list
languages = ['java', 'python' ,'perl', 'ruby', 'c#']
# insert c
languages.insert( 0, 'php') 
print languages 
# Output :  ['php', 'java', 'python', 'perl', 'ruby', 'c#'] 
```

**list.extend(项目)**

extend 方法连接列表。请注意，不要使用多个参数调用 extend 它接受第二个列表作为它的参数。

```
# define a list
languages = ['java', 'python' ,'perl', 'ruby', 'c#']

Languages2=['c++', 'c'] # define a second list

languages.extend(Languages2)

print(languages)

# Output ['java', 'python', 'perl', 'ruby', 'c#', 'c++', 'c'] 
```

> extend 方法不返回任何值，而是通过添加第二个列表的内容来修改原始列表

### 删除列表元素

使用 **del** 关键字删除特定索引处的项目

*   单项

    ```
     languages = ['java', 'python' ,'perl', 'ruby', 'c#'] # define a list
     del languages[2] # use del keyword
     print(languages)
     # Output removes perl ['java', 'python', 'ruby', 'c#'] 
    ```

*   多个项目

    ```
     languages = ['java', 'python' ,'perl', 'ruby', 'c#'] # define a list
     del languages[1:3]  #delete Multiple items by slicing
     print(languages)

     # Output : ['java', 'ruby', 'c#'] 
    ```

**list.remove(item)**

它将搜索并删除第一个出现的项目

```
Languages = ['java', 'python', 'perl','python','ruby', 'c#' ]
Languages.remove('python')  # the first occurrence of python
print(languages) # outputs ['java', 'perl', 'python', 'ruby', 'c#'] 
```

它在 java '之后移除 python。请注意，perl 后面的“python”仍然存在

**list.pop()**

移除并返回列表的最后一项

```
languages = ['java', 'python' ,'perl', 'ruby', 'c#']

print(languages.pop()) # print c#

print(languages)
# output ['java', 'python', 'perl', 'ruby'] 
```

**list.pop(i)**

移除并返回列表中的第 I 项

```
Languages = ['java', 'python', 'perl','ruby', 'c#' ]
print(languages.pop(1)) # removes  index 1 item
#  Output returns Python 
```

**list.index(item)**

当列表开始变得很长时，我们就很难数出条目来确定某个值位于哪个索引位置。我们可以使用`list.index(item)`，返回列表中该项所在的索引。
如果有多个值为`item`的项目，该方法将返回第一个出现的项目。

### 列表方法汇总

|  | 方法名称 | 描述 |
| --- | --- | --- |
| one | list.append(项目) | 在列表末尾添加新项目 |
| Two | list.insert(i，item) | 在第 I 个位置插入项目，将其他项目向右移动。 |
| three | list.extend() | 将第二个列表的元素添加到原始列表中 |
| four | 名单[i] | 删除第 I 个位置的项，也可以通过使用切片来删除一系列项 |
| five | list.remove(项目) | 搜索并移除项目的第一个匹配项，不返回新列表，如果找不到则抛出错误 |
| six | 流行() | 移除并返回列表的最后一项 |
| seven | pop(i) | 移除并返回列表的第 I 项 |
| eight | 列表索引(项目) | 返回第一次出现的索引 |
| nine | list.sort() | 对列表中的项目排序 |
| Ten | list.reverse() | 反转列表 |
| Eleven | 清单.计数(项目) | 返回项目出现的次数 |

### 常见的列表操作

**串联**

串联使用了 `+` 运算符。它结合了列表。

```
# define a list
languages = ['java', 'python' ,'perl', 'ruby', 'c#']
print(languages +(['c++', 'c']))
# Output:  ['java', 'python' ,'perl', 'ruby', 'c#', c++, c] 
```

**重复**

使用 `*` 运算符。重复连接一个列表

```
 mylist = ['strings are  cool'] * 2
 print (mylist)
# output:  ['strings are  cool', 'strings are  cool'] 
```

**会员资格**

关键字`In`用于测试一个条目是否是列表的成员。

```
languages = ['java', 'python' ,'perl', 'ruby', 'c#']
if 'python' in languages:
    print('right') 
```

### 迭代

`For`循环用于使用关键字`in`遍历列表中的每个元素。for 循环允许您对列表中的每个元素执行一个操作。

**插图 1**

```
languages = ['java', 'python' ,'perl', 'ruby', 'c#']
for i in languages:
   print(i) 
```

这将打印列表中的所有项目，每行一个，如下所示:

```
java
python
perl
ruby
c# 
```

**插图 2**

```
random_sum = [2,7,8,9]
total = 0
for i in random_sum:
    total +=i
print total 
```

上面的代码将打印 26，列表中所有项目的总和。
for 循环需要一个变量来保存被迭代的项和源。

`While`循环将首先检查条件。如果条件是`true`，它将继续迭代，一旦条件变为`false`，就终止循环。

```
languages = ['java', 'python' ,'perl', 'ruby', 'c#']
i = 0
while i <len(languages):
    print(languages[i])
    i = i+3 
```

以上代码将打印出来

```
java

ruby 
```

### 
 [T3】
结论](#conclusion) 

现在你知道什么是列表以及如何操作它们了。要进一步了解列表，请阅读关于列表理解。