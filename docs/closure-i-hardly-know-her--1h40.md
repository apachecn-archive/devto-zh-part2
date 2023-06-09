# 结束？我几乎不认识她！

> 原文：<https://dev.to/rpalo/closure-i-hardly-know-her--1h40>

*封面图片鸣谢:[这个惊人的 StackOverflow 答案](https://stackoverflow.com/a/29558498/4100442)* 。

我已经学习了几次闭包，每一次，我都觉得我明白了，但是我不一定理解为什么人们把它们看得这么重要。耶，万岁，你得到了可以保存数据的函数！我见过有人发布这样的帖子，“如果你不使用闭包，你真的错过了。”我想我终于明白了为什么人们会如此兴奋，为什么我会感到困惑。这篇文章将解释什么是闭包，什么时候你可能想使用它们，以及为什么我花了这么长时间才明白它们的特别之处。

## 什么是闭包

闭包(也称为*函数闭包*或*词法闭包*)是指你找到一种方法，将一个函数以它被定义的状态包装成一个连接的、持久的包。如果不理解的话，我会给你看一些例子。创建闭包有很多种方法，但是规范的方法是从一个函数中定义并返回另一个函数。我的意思是。

```
def build_zoo():
    animals = []
    def add_animal(animal):
        animals.append(animal)
        return animals
    return add_animal

zoo_a = build_zoo()
zoo_b = build_zoo()

zoo_a("zebra")
# => ["zebra"] zoo_a("monkey")
# => ["zebra", "monkey"] zoo_b("snek")
# => ["snek"] zoo_a("panda")
# => ["zebra", "monkey", "panda"] 
```

Enter fullscreen mode Exit fullscreen mode

*感谢 [@Doshirae](https://twitter.com/Doshirae) 和[李炎辉](https://dev.to/nixlend)指出`return`声明中的一个错别字！*

`build_zoo`函数是一种“工厂”,它创建一个*范围*,并在该范围内定义一个函数。然后，它将仍然可以访问该作用域(以及其中的变量)的函数*交给您。在`build_zoo`函数结束后，它保持堆栈框架和定义的变量(如`animals`)可供返回的`add_animal`函数使用，以备后用。每次你调用这个`build_zoo`函数，它都会创建一个全新的作用域，与其他任何作用域都没有联系。这也是为什么`zoo_a`和`zoo_b`被调用时无法相互影响的原因！*

### 边注:Python 和 Scopes

在 Python 中，如果不做额外的工作，就无法修改作用域之外的变量。所以，如果你试着这样做:

```
def build_incrementer():
    current_value = 0
    def increment():
        current_value += 1
        return current_value
    return increment

incrementer = build_incrementer()
incrementer()
# => UnboundLocalError: local variable 'current_value' referenced before assignment 
```

Enter fullscreen mode Exit fullscreen mode

你得到一个错误！在许多语言中并非如此。在许多语言中，在父作用域中访问变量是可以的。在 Python 中，你必须这样做:

```
def build_incrementer():
    current_value = 0
    def increment():
        nonlocal current_value # <==
        current_value += 1
        return current_value
    return increment 
```

Enter fullscreen mode Exit fullscreen mode

这使您可以修改这个值。你也可以使用 global，但是我们不是动物，所以我们不会。

## 好吧，但那又怎样？

"你可以用十亿种不同的方式来记录状态！"你夸张地说。“闭包有什么特别的？它们看起来不必要的复杂。”这有一点是真的。一般来说，如果我想用一个函数来跟踪我的状态，我会用几种不同的方法之一。

### 发电机功能

```
def build_incrementer():
    current_value = 0
    while True:
        current_value += 1
        yield current_value

inc_a = build_incrementer()
inc_b = build_incrementer()

next(inc_a)
# => 1 next(inc_a)
# => 2 next(inc_a)
# => 3 next(inc_b)
# => 1 
```

Enter fullscreen mode Exit fullscreen mode

这个方法很“Pythonic”。它没有内部函数(据你所知)，有一个相当容易识别的流程，并且(如果你理解生成器)，完成工作。

### 建立一个对象

```
class Incrementer:
    def __init__(self):
        self.value = 0

    def increment(self):
        self.value += 1
        return self.value

    # Or, just so we can match the section above:
    def __next__(self):
        return self.increment()

inc_a = Incrementer()
inc_b = Incrementer()

next(inc_a)
# => 1 next(inc_a)
# => 2 next(inc_b)
# => 1 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个很好的选择，对我来说也很有意义，因为我已经学习了大量的 Ruby 和 Python。

### 全局变量

```
current_value = 0
def increment():
    global current_value
    current_value += 1
    return current_value

increment()
# => 1 increment()
# => 2 increment()
# => 3 
```

Enter fullscreen mode Exit fullscreen mode

号码

*但是，我——*

号码

*等等！就让我-*

没有。不要这样做。

[![Do you want bugs?  Because that's how you get bugs.](img/e73be82adcde24da14cd9bdceecbcee5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wz3qUE3---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/closure-bugs.jpg)

全局变量将在非常简单的情况下工作，但当事情变得更加复杂时，这是一种非常快速和简单的搬起石头砸自己的脚的方法。你会有 17 个不同的不相关的函数，它们都会影响这个变量。而且，如果这个变量命名得不好，它很快就会变得混乱和无意义。如果你做了一个，你可能做了二十个，现在除了你没有人知道你的代码做了什么。

[![Well, at least it doesn't need obfuscating.](img/da5bdb600be8eaa99114e321c4e4f0fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6nOeGolK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/closure-obfuscating.jpg)

## 为什么闭包很酷

闭包令人兴奋有三个原因:它们非常小，非常快，而且非常容易获得。

### 他们很小

我们来看看上面每种方法(全局变量除外)的大致内存使用情况:

```
import sys

def build_function_incrementer():
    # ... 
funky = build_function_incrementer()
def build_generator_incrementer():
    # ... jenny = build_generator_incrementer()
class Incrementer:
    # ... classy = Incrementer()

### Functional Closure sys.getsizeof(build_function_incrementer) # The factory
# => 136 sys.getsizeof(funky) # The individual closure
# => 136 
### Generator Function sys.getsizeof(build_generator_incrementer) # The factory
# => 136 sys.getsizeof(jenny) # The individual generator
# => 88 
### Class sys.getsizeof(Incrementer) # The factory (class)
# => 1056 sys.getsizeof(classy) # The instance
# => 56 
```

Enter fullscreen mode Exit fullscreen mode

令人惊讶的是，生成器函数的输出实际上是最小的。但是无论是生成器函数，还是传统的闭包，都比创建一个类要小得多。

### 他们速度很快

让我们看看他们在时间上的表现。请记住，我将使用`timeit`因为它很简单，但不会很完美。此外，我正在用我的 slowish 小笔记本电脑做这件事。

```
import timeit

### Functional Closure timeit.timeit("""
def build_function_incrementer():
    # ...
funky = build_function_incrementer()
for _ in range(1000):
    funky()
""", number=1)
# => 0.0003780449624173343 
### Generator Function timeit.timeit("""
def build_generator_incrementer():
    # ...
jenny = build_generator_incrementer()
for _ in range(1000):
    next(jenny)
""", number=1)
# => 0.0004897500039078295 
### Class timeit.timeit("""
class Incrementer:
    def __init__(self):
        self.value = 0

    def increment(self):
        self.value += 1
        return self.value

    def __next__(self):
        return self.increment()

classy = Incrementer()
for _ in range(1000):
    next(classy)
""", number=1)
# => 0.001482799998484552 
```

Enter fullscreen mode Exit fullscreen mode

类方法再次出现在底部，但是这一次我们看到了函数闭包的边缘减速带。然而，请记住，闭包的最后一个论点是最有力的。

### 他们有货

这是我花了最长时间才发现的。不是所有的语言都像 Python 一样幸运。(请原谅，我正在准备我的收件箱以接收大量的仇恨邮件。)在 Python 中，我们很幸运地拥有了生成器以及许多创建它们的方法，比如生成器函数。老实说，如果我必须从上面的方法中选择，并且我正在编写 Python，我会推荐 Generator Function 方法，因为它更容易阅读和推理。

然而，有很多语言并不“包含电池”如果您想要一个小的应用程序，或者如果您受到某种限制，这实际上是一个好处。在这些情况下，只要您的语言支持创建函数，您就应该能够获得生成器的所有好处(惰性求值、记忆化、遍历可能无限的级数的能力……)，而没有任何花哨的功能。

在 JavaScript 中，你现在可以使用一个版本的生成器[，但是这是 ES6 并不总是有的功能。据我所知，这也不是 Go 的内置功能(尽管一些研究表明，](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)[使用通道而不是](https://stackoverflow.com/questions/11385556/python-style-generators-in-go)可能更习惯)。我确信还有许多其他低级语言，其中简单的函数闭包比试图编写自己的生成器更容易。

## 分享你的智慧！

因为我对低级语言没有太多的经验，所以闭包的优点和缺点对我来说都是新的。如果你有更好的解释或例子，说明什么时候结束是工作的完美工具，请[让我知道](https://twitter.com/paytastic)或在下面评论，我会尽我所能传播你的智慧。

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2018/02/10/closure-i-hardly-know-her/)*