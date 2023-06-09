# 编码字节:第 2 部分—数据类型、运算符和语法

> 原文：<https://dev.to/waqardm/coding-bytes-part-2-data-types-operators--syntax-4m63>

*这是编码字节系列的第 2 部分，更早的部分列举如下:*

*   *[第一部](https://dev.to/waqardm/coding-bytes-part-1intro--data-types-2nbc)*

## 数据类型续...

在第一部分中，我们没有完成所有的数据类型。这是故意的，因为这篇文章比我想要的要长一点，其次，在这个系列的过程中，一些东西将被省略，以使事情尽可能简单。

**对象**
我们之前已经讲过`arrays`，类似数组的还有`objects`。一个`object`的例子:

```
var me = { 
  firstName:"Waqar", 
  lastName:"Mohammad", 
  age:33, 
  eyeColor:"brown"
}; 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`arrays`和`objects`是相似的。随着我们在这个系列中向前推进，我们将详细介绍`arrays`、`multi-dimensional arrays`和`objects`之间的区别。现在，我们只需要知道对象可以用来存储具有描述性属性的数据，比如在这个例子中，我就是讨论中的对象。请参见下表，了解“我”`object`的代表。

| 财产 | 属性值 |
| --- | --- |
| 西方人名的第一个字 | Waqar |
| 姓 | 穆罕默德 |
| 年龄 | Thirty-three |
| 眼睛颜色 | 棕色 |

**布尔(又)**
我们讲过，布尔是一个`true`或`false`值，也可以用‘1’或‘0’来表示。事实上，还有更多' *falsy* '值。现在，我们不会进入细节，但重要的是，我们浏览它们。

##### Falsy 值

*   `0`
*   `false`
*   `null`
*   `undefined`
*   `""`
*   `NaN`

## 运算符

**算术运算符**这些被称为`arithmetic operators`。下表显示了它们的工作原理。

*请注意:例中的`x = 10`和`y = 3`。*

| 操作员 | 标志 | 例子 |
| --- | --- | --- |
| 添加 | `+` | x `+` y = 13 |
| 减法 | `-` | x `-` y = 7 |
| 增加 | `*` | x `*` y = 30 |
| 分开 | `/` | x `/` y = 3.33 |
| 模(除法的余数) | `%` | x `%` 3 = 1 |
| 增量 | `++` | var z = x `++`。结果`z = 11` |
| 减量 | `--` | var z = x `--`。结果`z = 9` |

`modulas`运算符可能有点令人困惑，但我们在上面的例子中所做的就是将`x`除以 3，并要求计算机尽可能长时间地进行等分，然后给我们余数，这里是 1。因此，当我们对`10 / 3`进行标准除法运算时，我们得到的不是`3.33`，而是`1`的余数，因为`3`可以被分成 10，总共 3 次，但在第四次尝试时，只剩下`1`——这就是`modulas operator`给我们的。我知道一开始会很困惑😕！但你会习惯的，我保证。

另一件要注意的事情是，`++`和`--`操作符可以出现在所讨论的值之前或之后，例如`++y`或`y++`，定位是很重要的，我们将在后面的阶段回顾。

最后，随着本系列的继续，我们将回顾其他操作符。

## 语法

Javascript(和其他语言)中有一套规则，就像我们在英语等口语中所拥有的一样。

**关键词**
我们之前也遇到过`var`这个关键词。关键字是为某些动作保留的，其中`var`用于分配一个变量。必须注意的是`VAR`或`Var`不能代替`var`，因为 Javascript 是区分大小写的。

**分号**
分号或`;`用来结束一个语句，就像句号或句号`.`会结束一个句子。

**注释**
我们经常需要在代码中进行注释，所以以两个斜线`//`开始可以确保后面的任何文本/数据都将被忽略。下面是一个例子。还要注意多行注释语法。

```
// this is an example of a comment.
var test = "test";

/*
This is a
multiline comment
*/ 
```

Enter fullscreen mode Exit fullscreen mode

**标识符**
`Identifiers`本质上是名称。Javascript 中的规则是标识符的第一个字符必须以`letter`、`_`或`$`符号开头。它不能以`number`开头。一个有趣的学习/测试你的标识符是否有效的方法可以在这里看到。

**案件类型**

##### 骆驼案

在编程中，我们会遇到几种情况。我们在上面的`object`示例中遇到了`camel case`，其中`firstName`是`property`。在`camel case`中，单词之间没有空格，除了第一个单词，每个单词的第一个字母都是大写的。

##### 帕斯卡格

`Pascal case`和`camel case`一模一样，唯一的区别是第一个单词也是大写字母。因此，这里不是`camel case`中的`firstName`，而是`FirstName`。

##### 其他

其他的例子有`underscore`，其中每个单词都用下划线隔开，`first_name`。还有`hyphen`、`first-name`。

**空格和换行符**
Javascript 忽略你在代码之间放置的任何空格，除非它们在类似`string`的东西中被指定。

*感谢阅读。如果你想加入我的编码之旅，来打个招呼吧👋在推特上。可以找到我[@ lawyers code](https://twitter.com/lawyerscode)T3】*