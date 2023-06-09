# 与 REPL 一起加快步伐

> 原文：<https://dev.to/joncassdev/moving-faster-with-repl--hk0>

## REPL 简介

开发人员喜欢“快速行动，打破常规”好吧，反正我们喜欢动作快。“REPL”是我发现的一种工具，它可以防止我陷入大型应用程序的工作环境中。当然，在某些时候，我的新特性或 bugfix 必须集成到代码库中，但从那里开始会增加摩擦并减慢我的速度。在这篇文章中，你将了解什么是 REPLs，以及如何使用它们来有效地工作。

REPL 是一个阅读-评估-打印的循环。这个概念最初是在 Lisp 编程语言中引入的，以允许在 Lisp 中进行快速实验。在 Lisp 中，下面是一个基本 REPL 的实现:

```
(loop (print (eval (read)))) 
```

Enter fullscreen mode Exit fullscreen mode

从里到外阅读这些命令(这是它们在 Lisp 中的执行方式)，您可以看到 REPL 得名的原因！

一般来说，您可以从命令行调用 REPL。当你启动 REPL 时，它会把你带到一个新的界面，类似于命令行，但你的指令是用 REPL 的语言解释的。事实上，[您可以将命令提示符视为 Bash](https://stackoverflow.com/questions/19728253/how-do-i-get-a-repl-in-bash) 的 REPL。一旦进入 REPL，你就可以运行命令、定义变量、编写函数等等。看看结果。

## 例子:Python 和 Node

Python 和 Node 在安装时都会附带相当复杂的 REPLs。这里有一些例子你可以试试！

### 巨蟒

通过在命令提示符下键入`python3`(在本例中为`user@comp ~$`)并按回车键，启动 Python REPL。它会打印出一些关于你的`python`安装的信息，并在 REPL 提示符(T3)下提示你:

```
user@comp ~$ python3
Python 3.6.1 (default, Apr  4 2017, 09:36:47) 
[GCC 4.2.1 Compatible Apple LLVM 7.0.2 (clang-700.1.81)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

Enter fullscreen mode Exit fullscreen mode

从这里，你可以执行计算，定义变量和函数等:

```
>>> 1+1
2
>>> greeting = 'hello world'
>>> print(greeting)
hello world
>>> def print_greeting(greeting):
...     print(greeting)
... 
>>> print_greeting('hello world')
hello world
>>> print_greeting('hello function')
hello function 
```

Enter fullscreen mode Exit fullscreen mode

用`^d` (ctrl+d)
退出

```
>>> ^d
user@comp ~$ 
```

Enter fullscreen mode Exit fullscreen mode

### 节点

进入节点 REPL

```
user@comp ~$ node
> 
```

Enter fullscreen mode Exit fullscreen mode

就像在 Python 中一样，你可以执行计算，定义变量和函数等:

```
> 1+1
2
> const greeting = 'hello world';
undefined
> console.log(greeting);
hello world
undefined
> const printGreeting = (greeting) => console.log(greeting);
undefined
> printGreeting('hello world');
hello world
undefined
> printGreeting('hello function');
hello function
undefined 
```

Enter fullscreen mode Exit fullscreen mode

用`^d` (ctrl+d)
退出

```
> ^d
user@comp ~$ 
```

Enter fullscreen mode Exit fullscreen mode

节点示例中出现的`undefined`是每个语句的返回值。如果您的语句有一个已定义的返回值，它将被打印出来，如`1+1`示例所示。还要注意，这些 REPLs 有命令历史记录，所以您可以按“up”来查看过去的命令，甚至在会话之间。

## 实现

REPL 就像你从命令行运行的任何程序一样。当您运行它时，它会给出一些输出，然后等待用户输入。当输入一条语句时，它对该语句求值，并打印出结果。在 Python 和 Node 中，都有内置模块，可以为 REPL 提供一些注入的“上下文”。你可以在这里阅读 [@rpalo](https://dev.to/rpalo) 的精彩帖子[，了解如何利用 Python 库`Code`创建你自己的 REPL。下面是如何在 Node:](https://dev.to/rpalo/your-own-python-repl-in-twenty-lines-or-less) 

```
// include the repl library
const repl = require('repl');

// start it up, with the desired prompt string
const r = repl.start('> ');

// inject a couple pieces of context
r.context.text = 'This is some text';
r.context.greet = (name) => console.log(`hello ${name}`); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将它保存为`my_repl.js`，然后启动并如下使用它(注意`text`和`greet`已经为您定义了，因为它们被注入到上下文中):

```
user@comp ~$ node my_repl.js 
> 
```

Enter fullscreen mode Exit fullscreen mode

```
> 1+1
2
> text
'This is some text'
> greet('Jon')
hello Jon
undefined
> const greetAndCompliment = (name) => {
...   greet(name);
...   console.log('nice code');
... }
undefined
> greetAndCompliment('Jon')
hello Jon
nice code
undefined
> 
```

Enter fullscreen mode Exit fullscreen mode

## 日常使用

我发现 REPLs 在进行简单实验时最有用。例如，不用创建一个`test.py`脚本来确认默认参数的工作方式，我只需启动 REPL 并确认它:

```
>>> def print_greeting(greeting='hello world'):
...     print(greeting)
... 
>>> print_greeting()
hello world
>>> print_greeting('hello overridden default')
hello overridden default 
```

Enter fullscreen mode Exit fullscreen mode

## 总结起来

既然你已经学习了 REPLs，你可能也会对[单元测试](https://en.wikipedia.org/wiki/Unit_testing)和[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)或者 TDD 感兴趣。通过缩短周期时间，这些以类似的方式提高了开发速度。它们具有提高代码质量的额外优势。为了进一步阅读和回复，请查看[维基百科页面](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop)或 [repl.it](https://repl.it) 。

感谢阅读！