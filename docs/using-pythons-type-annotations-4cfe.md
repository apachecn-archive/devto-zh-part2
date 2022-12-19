# 使用 Python 的类型注释

> 原文：<https://dev.to/dan_starner/using-pythons-type-annotations-4cfe>

Python 是众所周知的狂野西部语言，无所不在。除了缩进之外，代码风格和文档大部分是由开发人员根据自己的想法编写的，这可能会导致一些混乱、不可读的代码。

这种模糊的样式结构部分源于 Python 是一种动态类型化的语言，这意味着类型与变量在某个时间点的值相关联，而不是变量本身。这种语言属性意味着变量可以在任何点取任何值，并且只有在访问属性或方法时才进行[类型检查](https://en.wikipedia.org/wiki/Type_system#Dynamic_type_checking_and_runtime_type_information)。

考虑下面的代码。在 Python 中，这是可以接受的。

```
age = 21
print(age)  # 21 age = 'Twenty One'
print(age)  # Twenty One 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，`age`的值首先是一个`int`(整数)，但随后我们将其更改为一个`str`(字符串)。每个变量都可以代表程序中任意点的任意值。这就是动态打字的力量！

让我们用一种静态类型的语言做同样的事情，比如 Java。

```
int age = 21;
System.out.print(age);
age = "Twenty One";
System.out.print(age); 
```

Enter fullscreen mode Exit fullscreen mode

我们以下面的错误结束，因为我们试图将`"Twenty One"`(一个`String`)赋给被声明为`int`的变量`age`。

```
Error: incompatible types: String cannot be converted to int 
```

Enter fullscreen mode Exit fullscreen mode

为了在静态类型语言中工作，我们必须使用两个独立的变量，并使用一些辅助的类型转换方法，比如标准的`toString()`方法。

```
int ageNum = 21;
System.out.print(ageNum);
String ageStr = ageNum.toString();
System.out.print(ageStr); 
```

Enter fullscreen mode Exit fullscreen mode

这种转换是可行的，但是我真的很喜欢 Python 的灵活性，我不想因为类型在大多数情况下很难推理而牺牲它作为一种动态的、可读的、初学者友好的语言的积极属性。也就是说，我也喜欢静态类型语言的可读性，让其他程序员知道一个特定变量应该是什么类型！所以，为了两全其美，Python 3.5 引入了**类型注释**。

## 什么是类型标注？

类型注释是在 [PEP 484](https://www.python.org/dev/peps/pep-0484/) 中添加的一个新特性，允许给变量添加*类型提示*。它们用来通知阅读代码的人什么类型的变量*应该是*预期的。这种暗示给动态类型的 Python 带来了一种静态类型控制的感觉。这是通过在初始化/声明变量或方法后添加给定的类型声明来实现的。

## 为什么&如何使用类型标注

静态类型语言的一个有用的特性是变量的值在特定的域内总是已知的。例如，我们知道字符串变量只能是字符串，整型变量只能是整型，等等。对于动态类型语言，基本上任何人都可以猜测变量的值是什么或者应该是什么。

### 标注变量

注释变量时，可以用
的形式定义

```
my_var: <type> = <value> 
```

Enter fullscreen mode Exit fullscreen mode

用给定的值创建给定类型`<type>`的名为`my_var`的变量。

下面显示了一个例子，当我们声明变量时添加了`: int`来表明年龄应该是类型 *int* 。

```
age: int = 5
print(age)
# 5 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，**类型注释不会以任何方式影响程序的运行时间**。这些提示会被解释器忽略，只是用来增加其他程序员和您自己的可读性。但是同样，这些类型提示在运行时是不强制的，所以仍然由调用者方法/函数/块来确保使用正确的类型。

## 标注函数&方法

我们可以在编写和调用函数时使用预期变量的类型，以确保正确传递和使用参数。如果我们在函数期望一个`int`时传递一个`str`，那么它很可能不会以我们期望的方式工作。

考虑下面的代码:

```
def mystery_combine(a, b, times):
    return (a + b) * times 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到*在做什么*，但是我们知道`a`、`b`或`times`应该是什么吗？看看下面的代码，特别是我们用不同类型的参数调用`mystery_combine`的两行代码。观察每个版本的输出，显示在每个块下面的注释中。

```
# Our original function def mystery_combine(a, b, times):
    return (a + b) * times

print(mystery_combine(2, 3, 4))
# 20 
print(mystery_combine('Hello ', 'World! ', 4))
# Hello World! Hello World! Hello World! Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

嗯，基于我们传递的函数，我们得到两个*完全*不同的结果。对于整数，我们得到了一些不错的 [PEMDAS](https://xkcd.com/992/) 数学，但是当我们将字符串传递给函数时，我们可以看到前两个参数被连接起来，得到的字符串被乘以`times`倍。

原来写函数的开发者其实预料到了第二个版本是`mystery_combine`的用例！使用类型注释，我们可以消除这种混乱。

```
def mystery_combine(a: str, b: str, times: int) -> str:
    return (a + b) * times 
```

Enter fullscreen mode Exit fullscreen mode

我们已经将`: str`、`: str`和`: int`添加到函数的参数中，以显示它们应该是什么类型。这将有望使代码阅读起来更清晰，并更多地揭示它的用途。

我们还添加了`-> str`来表明这个函数将返回一个`str`。使用`-> <type>`，我们可以更容易地显示任何函数或方法的返回值类型，以避免未来开发人员的困惑！

同样，我们仍然可以以第一种不正确的方式调用我们的代码，但是希望有一个好的回顾，程序员将会看到他们正在以一种不期望的方式使用函数。类型注释和提示对于团队和多开发者 Python 应用程序非常有用。它消除了阅读代码的大部分猜测！

我们可以进一步扩展这一步来处理默认参数值。我们修改了下面的`mystery_combine`,使用`2`作为`times`参数的默认参数值。该默认值放在类型提示之后。

```
def mystery_combine(a: str, b: str, times: int = 2) -> str:
    return (a + b) * times 
```

Enter fullscreen mode Exit fullscreen mode

### 键入提示与方法

类型提示的工作方式与方法非常相似，尽管在我的经验中，省略对`self`的类型提示是很常见的，因为这意味着它是包含类本身的一个实例。

```
class WordBuilder:

    suffix = 'World'

    def mystery_combine(self, a: str, times: int) -> str:
        return (a, self.suffix) * times 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到上面的代码与前面的基于函数的例子非常相似，除了我们为`WordBuilder`类上的`suffix`属性去掉了`b`参数。注意，我们不需要显式地将`: str`添加到`suffix`定义中，因为大多数代码编辑器会查看预期类型的默认值。

* * *

## 可用类型

上一节处理了类型注释的许多基本用例，但是没有什么是*仅仅是*基本的，所以让我们分解一些更复杂的用例并展示常见的类型。

### 基本类型

注释对象最基本的方法是使用类类型本身。在 Python 中你可以提供任何满足一个`type`的东西。

```
# Built-in class examples an_int: int = 3
a_float: float = 1.23
a_str: str = 'Hello'
a_bool: bool = False
a_list: list = [1, 2, 3]
a_set: set = set([1, 2, 3])  # or {1, 2, 3} a_dict: dict = {'a': 1, 'b': 2}

# Works with defined classes as well class SomeClass:
    pass

instance: SomeClass = SomeClass() 
```

Enter fullscreen mode Exit fullscreen mode

### 复杂类型

使用 [`typing`](https://docs.python.org/3/library/typing.html) 模块来处理 Python 中除原语之外的任何东西。它描述类型来提示 *any* 变量对 *any* 类型更详细。它预装了类型注释，如`Dict`、`Tuple`、`List`、`Set`等等！在上面的例子中，我们有一个`list`提示的变量，但是没有定义列表中的*应该是什么。由`typing`模块提供的类型容器允许我们更正确地指定想要的类型。*

然后，您可以将类型提示扩展到用例中，如下例所示。

```
from typing import Sequence

def print_names(names: Sequence[str]) -> None:
    for student in names:
        print(student) 
```

Enter fullscreen mode Exit fullscreen mode

这会告诉读者`names`应该是`str` s 的一个 [`Sequence`](https://techvidvan.com/tutorials/python-sequences/) ，比如琴弦的一个`list`、`set`或者`tuple`。

字典以类似的方式工作。

```
from typing import Dict

def print_name_and_grade(grades: Dict[str, float]) -> None:
    for student, grade in grades.items():
        print(student, grade) 
```

Enter fullscreen mode Exit fullscreen mode

`Dict[str, float]`类型提示告诉我们`grades`应该是一个字典，其中键是`str`ing，值是`float` s

### 键入别名

如果你想使用自定义类型名，你可以使用*类型别名*。例如，假设你正在用一组`\[x, y\]`点作为`Tuple`点，那么我们可以使用一个别名将`Tuple`类型映射到`Point`类型。

```
from typing import List, Tuple

# Declare a point type annotation using a tuple of ints of [x, y] Point = Tuple[int, int]

# Create a function designed to take in a list of Points def print_points(points: List[Point]):
    for point in points:
        print("X:", point[0], "  Y:", point[1]) 
```

Enter fullscreen mode Exit fullscreen mode

### 多个返回值

如果您的函数以元组的形式返回多个值，则将预期输出包装为`typing.Tuple[<type 1>, <type 2>, ...]`

```
from typing import Tuple

def get_api_response() -> Tuple[int, int]:
    successes, errors = ... # Some API call
    return successes, errors 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码从 API 调用返回一个成功和错误数量的元组，其中两个值都是整数。通过使用`Tuple[int, int]`，我们向阅读本文的开发人员表明，该函数确实返回了多个`int`值。

### 多种可能的退货类型

如果你的函数有一个可以采用不同形式的值，你可以使用`typing.Optional`或`typing.Union`类型。

当值为给定类型*或* `None`时，使用`Optional`。

```
from typing import Optional

def try_to_print(some_num: Optional[int]):
    if some_num:
        print(some_num)
    else:
        print('Value was None!') 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码表明`some_num`可以是类型`int`或`None`。

当值可以采用更具体的类型时，使用`Union`。

```
from typing import Union

def print_grade(grade: Union[int, str]):
    if isinstance(grade, str):
        print(grade + ' percent')
    else:
        print(str(grade) + '%') 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码表明`grade`可以是类型`int`或`str`。这在我们打印分数的例子中很有帮助，这样我们就可以打印 98%的或 98%的而不会产生意外的结果。

## 使用数据类

[数据类](https://docs.python.org/3/library/dataclasses.html)是一个方便的类，它为适当的类提供自动生成的`__init__`和`__repr__`方法。它减少了创建新类所需的[样板代码](https://en.wikipedia.org/wiki/Boilerplate_code)的数量，这些新类在其构造函数中接受多个关键字参数。这些数据类使用类型提示和类级属性定义来确定哪些关键字参数和相关值可以传递给`__init__`并由`__repr__`打印。

以下代码直接来自 [`dataclasses`文档](https://docs.python.org/3/library/dataclasses.html)。它定义了一个`InventoryItem`，上面定义了三个属性，都使用了类型提示；一个`name`、`unit_price`和`quantity_on_hand`。

```
from dataclasses import dataclass

@dataclass
class InventoryItem:
    """Class for keeping track of an item in inventory."""
    name: str
    unit_price: float
    quantity_on_hand: int = 0

    def total_cost(self) -> float:
        return self.unit_price * self.quantity_on_hand 
```

Enter fullscreen mode Exit fullscreen mode

使用类型提示和`@dataclass`装饰器，可以用下面的代码创建新的【the dataclass 将负责将关键字参数映射到属性。

```
common_item = InventoryItem(name='My Item', unit_price=2.99, quantity_on_hand=60)
other_item = InventoryItem(name='My Item', unit_price=2.99)  # uses default value of 10 quantity 
```

Enter fullscreen mode Exit fullscreen mode

关于`@dataclass` es 的一个重要注意事项是*任何用默认值**定义的***类属性必须在任何没有默认值的属性之后声明。这意味着`quantity_on_hand`必须在`name`和`unit_price`之后声明。当处理从父数据类扩展的数据类时，这会变得很有趣，所以要小心，但是 Python 解释器应该会为您捕捉这些问题。

### 更多例子

要获得更多的例子，请查看`typing`模块的官方 Python 文档。他们有大量不同种类的例子供你参考。这里我只是触及了冰山一角，但是希望我已经激起了您对使用 Python 中的类型注释来编写更干净、更易读的代码的兴趣。

一如既往，如果你有任何意见或问题，请伸出手，喜欢，评论或分享！