# 使用正则表达式和 Python 将蛇的大小写转换成骆驼的大小写

> 原文：<https://dev.to/rrampage/snake-case-to-camel-case-and-back-using-regular-expressions-and-python-m9j>

[蛇案](https://en.wikipedia.org/wiki/Snake_case)和[骆驼案](https://en.wikipedia.org/wiki/Camel_case)是命名变量、函数和类的约定。大多数团队和项目在他们的风格指南中规定了一个特殊的案例。

骆驼案的例子:

```
MyClass
MyClassFactory
MyClassFactoryBuilder
MyClassFactoryBuilderImpl
myInstance
myInstance2
abc
patternMatcher 
```

Enter fullscreen mode Exit fullscreen mode

蛇案的例子:

```
add
matrix_add
diagonal_matrix_add
pseudo_inverse 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想要在这些情况之间来回转换，我们必须寻找感兴趣的点——单词边界。骆驼大小写界限的第一个字母大写，而蛇大小写界限有一个 **`_`** 。

### 蛇案到骆驼案

下面是一个正则表达式，用于找出 **`_`** 和下一个单词的第一个字母:

```
(.*?)_([a-zA-Z]) 
```

Enter fullscreen mode Exit fullscreen mode

这个正则表达式有两个部分:

1.  **`(.*?)`** 查找到 **`_`** 的所有内容。

    *   “.”表示任何字符。
    *   * '代表匹配 0 个或多个实例
    *   '?'代表非贪婪匹配。我们必须用“？”因为默认情况下，正则表达式引擎会尝试尽可能多的匹配。因此，如果我们只使用 **`(.*)`** ，整个单词将被消耗掉，而不会为模式的其余部分留下任何内容。
    *   ()‘代表一个群体。分组是将比赛的一部分留到以后进行的一种方式。
    *   总的来说，它们意味着查找第一个' _ '之前的所有字符，并将它们捕获到一个组中。
2.  **`([a-zA-Z])`** 找出 **`_`** 后的第一个字母。我们需要这个来转换为骆驼案件的大写。

下面的 Python 代码将蛇的大小写转换成骆驼的大小写:

```
import re

REG = r"(.*?)_([a-zA-Z])"

def camel(match):
    return match.group(1) + match.group(2).upper()

def camel_upper(match):
    return match.group(1)[0].upper() + match.group(1)[1:] + match.group(2).upper()

words = """add
matrix_add
diagonal_matrix_add
pseudo_inverse""".splitlines()

results = [re.sub(REG, camel, w, 0) for w in words]
print(results)
# Output:
# ['add', 'matrixAdd', 'diagonalMatrixAdd', 'pseudoInverse'] 
```

Enter fullscreen mode Exit fullscreen mode

我们使用前面构建的正则表达式和 [**`re.sub`**](https://docs.python.org/3/library/re.html#re.sub) 方法来替换匹配的单词。我们传递一个名为`camel`的方法作为参数。这个方法允许我们改变第二组中字母的大小写，而保持第一组不变。注意，第一个字母可以是小写的，也可以是大写的，这取决于它是变量、方法(小写)还是类(大写)。`camel_upper`方法可以用于类名。

### 骆驼案到蛇案

类似地，对于从 camel 到 snake 的转换，正则表达式是:

```
(.+?)([A-Z]) 
```

Enter fullscreen mode Exit fullscreen mode

还有 Python 代码:

```
import re

REG = r"(.+?)([A-Z])"

def snake(match):
    return match.group(1).lower() + "_" + match.group(2).lower()

words = """MyClass
MyClassFactory
MyClassFactoryBuilder
MyClassFactoryBuilderImpl
myInstance
myInstance2
abc
patternMatcher""".splitlines()

results = [re.sub(REG, snake, w, 0) for w in words]

print(results)
# Output
# ['my_class', 'my_class_factory', 'my_class_factory_builder', 'my_class_factory_builder_impl', 'my_instance', 'my_instance2', 'abc', 'pattern_matcher'] 
```

Enter fullscreen mode Exit fullscreen mode