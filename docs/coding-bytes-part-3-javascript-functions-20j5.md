# 编码字节第 3 部分:Javascript 函数

> 原文：<https://dev.to/waqardm/coding-bytes-part-3-javascript-functions-20j5>

*这是编码字节系列的第 2 部分，更早的部分列举如下:*

*[第一部分](https://lawyerscode.co.uk/blog/coding-bytes-1)*
*[第二部分](https://lawyerscode.co.uk/blog/coding-bytes-2)*

### 什么是函数？

一个`function`实际上是一个`object`，被设计来执行一个特定的任务，通常是重复的。

### 定义一个函数

有几种定义`function`的方法，但是我们将关注最基本的，所以现在将忽略`arrow functions/ ES6 functions`。

```
 function nameOfFunction (parameters) {
        statement;
    } 
```

Enter fullscreen mode Exit fullscreen mode

如上例所示，`function`关键字用于定义一个`function`。关键字后面是你自己选择的名字，但是描述一下`function`是做什么的通常是很好的做法——下面会有更多的介绍。

在命名了`function`之后，我们有了`parentheses ( )`中的`parameters`，后面是`curly braces { }`中的语句。您可以定义多达 255 个用逗号分隔的`parameters`。`Parameters`类似于占位符，其中`function`知道寻找这些占位符来执行其预期用途。您可能会遇到术语`arguments`互换使用的情况，但是有一点细微的区别，这在一个例子中有更好的解释。

ℹ️ *如果你正在使用 Chrome，你可以试着在[控制台](https://www.youtube.com/watch?v=PIxpbreS3eU&feature=youtu.be)中跟随。*

##### 举例

在我们的例子中，我的朋友是一名地毯装配工，他需要计算出房间的`area`，这样他就知道需要多少地毯。我们知道`area = length x width`，那么我们如何把它放到`function`中呢？

```
function area (length, width) {
  return length * width;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们的`function`被命名为 area，`parameters`是`length, width`。您可以看到一个`return`语句，它停止了函数的执行，并告诉函数我们期望看到什么样的响应。在声明中，我们要求将`length`和`width`相乘。简而言之，我们函数的任务就是乘以`parameters`。

### 调用函数

`Invoking``function`只是一种对`function`的奇特称呼。要调用一个`function`，我们只需要通过它的名字后跟括号来引用它。我们可以参考上面的例子和`invoke`区域`function`。

```
area(10, 5); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们调用了`area`函数，但是你会注意到`()`中的`10,5`。这两个数字代表我们之前提到的`length, width`，也就是所谓的`parameters`。但是在这里，因为它们是提供给函数的数据——我们称它们为`arguments`。希望现在更容易看出两者之间的区别😃。

我们在上面的`invocation`中所说的就是，运行`area` `function`，使用`10,5`作为`arguments`。众所周知，我们的`function`被设置为将两个`arguments`相乘，得到 50 的输出。恭喜🎉我们创造了第一个`function`。

### 进一步学习

这只是一个非常基本的`function`，但是你可以做得更多！为了进一步练习，考虑一下`function`可以派上用场的地方，并尝试创建一个。下面还有一个例子，在把它复制到你的`console`之前，试着理解它可能会做什么。

```
function sayHello(name, age){
  console.log(name + " is " + age + " years old.");
} 
```

Enter fullscreen mode Exit fullscreen mode

你需要研究一下`console.log()`是做什么的，记住字符串是用`" "`包装的。祝你好运！

* * *

*感谢阅读。为了跟上我的编码之旅，来打个招呼吧👋在 [twitter](https://twitter.com/lawyerscode) 或我们的#devNewbie [Discord](https://discordapp.com/invite/mBsMP2H) 服务器上，我们有一群友好的学习者分享他们的经验。*