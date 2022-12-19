# 我为什么要关心 python 中的不变量？

> 原文：<https://dev.to/himankbhalla/why-should-i-care-about-immutables-in-python-4ofn>

[![](../Images/9d12e41f3f97510ca283837ee525ae0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JhrhcjkW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fef4t2pzo1sty270jg76.jpg) 
为什么我会关心 Python 中的不变？

我们经常读到可变数据类型和不可变数据类型。例如，字符串在 Java 中是不可变的。有很多资源都在讨论这个概念，但是我找不到关于 Python 的完整讨论。在本文中，我将讨论这个概念。它的用例以及为什么我们需要 Python 的不变性？

**不可变对象**
不可变对象是不能被修改的对象。这意味着一旦创建，其状态就不能改变。一旦创建，它将代表相同的值。例如—字符串在 python 中是不可变的。一个字符串对象将总是代表相同的值，它不能被修改。例如—字符串对象“h”将始终表示 python 值 h。无论发生什么情况！

另一个例子是，如果有一个类似“hello”的字符串对象，我想将第二个位置的字符“e”更改为“o ”,以使字符串对象成为“hollo”。没有办法，我可以在 python 中做到这一点，因为字符串在 Python 中是不可变的。唯一的解决方法是创建另一个新的字符串对象“hollo”。

让我们用 python 来验证这一点。

```
name = 'python'
name[0] = 'z'    #This won't change the original string to zython.
                 #This will result in an error as item assignment
                 #is not supported for strings. Even if we implement
                 #item assignment, we will have to create a new
                 #string. 
```

再比如:

```
x = 'hello'

print(id(x))      #This will print some id like 8879437848.

x = 'python'

print(id(x))      #This will print some id like 4387438948 different
                  # from the previous one. 
```

因为字符串对象“hello”不同于字符串对象“python”。函数 id 返回不同的唯一 id。这意味着存储这些对象的内存位置是不同的，这是两个不同的对象。

[![](../Images/d880e112d65d3215719e2c9b37a013e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0VtTmeyD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ae1DF3ithSOP3bP_ZCef78A.png) 
**可变对象**
可变对象是在创建后状态可以修改的对象。在程序执行期间，它可能表示相同的值，也可能不表示相同的值。例如，列表是一个可变对象。

```
myList = [1 , 2 , 3]

print(id(myList))      #This will print id like 8594375837.

myList.append(5)       #This appends 5 to the list resulting in
                       # myList to be[1, 2, 3, 5]

print(id(myList))      #This will print id with the same value as
                       # before 8594375837. 
```

这里，我们发现两个 id 是相等的。因为当我们在列表中添加 100 或者试图修改原始列表时。没有创建新的列表对象，而是修改了原始列表，因为它是可变的数据类型。

事实上，整数、浮点和元组在 python 中也是不可变的数据类型。列表、集合、字典都是可变数据类型。另外，python 中没有原语。一切都是物体。我真的需要永恒吗？

为什么我们需要不变性？

*   不可变对象是线程安全的。对象的状态在构造后不能更改。这意味着只读数据在线程间共享，这提供了线程安全性。涉及像(在特定索引处改变值)这样的突变的操作可以通过创建新对象而不是修改现有对象的方式来实现。
*   不可变对象提高了正确性和清晰性。它提供了一种理性思考代码的方式。因为对象的值在程序执行过程中不会改变。我们可以自由地传递对象，而不用担心被修改。
*   可变对象更难推理。具有指向同一对象的多个引用的可变对象也称为别名，会导致不一致并威胁代码的正确性。一部分代码改变了值，另一部分代码由于这种改变而受到影响。这就是它导致不一致的原因。

Python 也围绕这种不变性进行了一些优化。尤其是对于字符串对象。如果我们用相同的值创建 10 个字符串对象，那么它不会为 10 个字符串分配内存，而是每个标识符都指向同一个字符串。因此，节省了内存。虽然，这是依赖于实现的，对于其他不可变的数据类型不会完全这样。

示例:

```
myList = []                 #Create an empty list.

for i in range(10):
    myList.append('hello')   #Append 'hello' 10 times.

for i in range(10):
    print(id(myList[i]))     #id of each item will be same because
                             # they all refer to the same object. 
```