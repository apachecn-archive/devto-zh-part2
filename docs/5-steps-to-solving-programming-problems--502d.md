# 解决编程问题的 5 个步骤

> 原文：<https://dev.to/aprietof/5-steps-to-solving-programming-problems--502d>

解决问题是程序员的饭碗，每个人都有自己的方法，我个人发现 5 个步骤很可能会帮助你，不仅解决问题，而且更快更有效地解决问题。

### 1。把问题多看几遍，直到你能向别人解释为止

[![Read](img/810cbf89cf19b8f925b9c2a785bf5c5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NNdNHOyF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.adrianprieto.com/wp-content/uploads/2016/04/giphy-4.gif)

这是到目前为止，**最重要的**步骤，把问题看几遍，直到你完全理解为止，不理解就解不出来。知道你是否理解问题的最好方法是向别人解释。

### 2。手动解决问题

> 没有什么是不能手动完成的，可以自动完成的！

我们写的任何代码都有一个基础，就是手工过程。也就是说，首先手动解决问题，这样你就知道你想要自动化什么，如果你开始像疯子一样写代码，这会节省你很多时间。

用一个以上的输入和一些边角案例来测试你的过程以验证它，密切注意你在头脑中采取的每一步，把它写下来，因为每一步都很重要。

### 3。让您的手动解决方案更好

看看你是否能使你的过程更好，是否有更简单的方法，或者是否有一些步骤你可以削减来简化它(比如循环)。这一步非常重要，记住在你的头脑中重建你的过程比在你的代码中要容易得多。

在这一点上，你可能会想写一些代码，但不要这么做，我们还有一步要走，我向你保证这会让你的最终代码更容易写。

### 4。写伪代码

伪代码是对一个程序必须做的事情的详细描述，这将帮助你写出解决你的问题所需的每一行代码。

有经验的程序员有时会省略这一步，但我可以向您保证，无论您有多有经验，如果您编写一些伪代码，编写最终代码的过程会容易得多，因为您只需将每一行伪代码翻译成实际代码。

让我们考虑一下编写一个返回数字平方值的函数所需的步骤。

```
// Initialize a variable with a 'n' value

// Multiply variable by it self

// Return the result of that multiplication 
```

Enter fullscreen mode Exit fullscreen mode

现在我们确切地知道了代码应该做什么，我们还有一个步骤。

### 5。用真实代码替换伪代码

这是有趣的部分，现在你已经确切地知道你的程序应该做什么，只需写一些代码并测试它。请记住，在这个过程中，你总能让你的代码变得更好。

让我们以正方形为例(非常简单，便于演示):

```
function square(n) { 
    // Initialize a variable with a 'n' value
    const initialValue = n
    // Multiply variable by it self
    const squaredValue = initialValue * initialValue
    // Return the result of that multiplication
    return squaredValue
} 
```

Enter fullscreen mode Exit fullscreen mode

优化您的代码:

```
function square(n) { 
    return n * n
} 
```

Enter fullscreen mode Exit fullscreen mode

这就对了。

这看起来是一个显而易见的过程，但是大多数人忽略了一半并开始编码，这导致了宝贵时间的浪费。

不管你的问题有多复杂，我向你保证，这 5 个步骤将帮助你在更短的时间内解决问题，减少麻烦。

*注意:如果你的问题太复杂，把它分成小问题，这是一种叫做[“分而治之”](https://en.wikipedia.org/wiki/Divide_and_conquer_algorithms)的技巧。*

*最初发布在我的[网站](https://www.adrianprieto.com/5-steps-to-solving-programming-problems/)-2016 年 5 月 26 日*