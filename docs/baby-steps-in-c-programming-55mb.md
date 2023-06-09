# C 编程的初级步骤

> 原文：<https://dev.to/bauripalash/baby-steps-in-c-programming-55mb>

[![.](img/9b052cb010875e144ac7b36a75a3f2dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BcL8O7bk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://palash.tk/asseimg/babysteps-c-prog.jpg)

C 语言是迄今为止最常用的编程语言之一。它是最强大的语言之一，是许多现代编程语言(如 Python、Ruby)的母亲

[![](img/b9a18f708cda62f578f8f8620507d4d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dWwH4rJ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media2.giphy.com/media/fnD9cHHIrYRYk/giphy.gif)

🍭C 是一种编译语言，不像 [Python](https://python.org) 或者 [Ruby](https://www.ruby-lang.org/en/) ，C 程序必须从人类可读代码翻译成机器可读代码。

> 把人类可读的 C 代码翻译成机器可读的代码的程序叫做编译器
> 
> 你会惊讶地发现大多数 C 语言的编译器也是用 C 语言编写的

你会发现有人说，C 语言太难了，但我说没什么难的，只是需要一个好老师来解释一切。

所以，不再多说，让我们迈出 C 编程世界的第一步。

## 设置工作空间

正如我前面提到的，C 是一种编译语言，所以你需要一个编译器如 GCC 来编译 C 程序。

今天我们将使用 Repl.it 在线编译和运行 C 程序。

如果你想在你的本地设备上运行和编译 C 程序，请阅读这篇文章

要在不安装任何东西的情况下在线运行和编译 C 程序，使用 [REPL.it](https://repl.it/languages/c)

当打开 Repl.it 时，你会看到一个样例程序已经写在了左边的面板中。先清除它。

## 书写繁体 Hello World

正如我前面提到的，Hello World 是每个程序员在学习新语言时首先编写的传统程序。它只是在控制台窗口中打印出 **Hello World** 文本

C 语言的 Hello World 程序比 Python 或 Ruby 要长一点。

让我们写下

```
#include<stdio.h> 
int main()
{
    printf("Hello World\n");
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 repl.it 的左窗格中编写上述代码，或者如果使用本地设备，在名为 **hello.c** 的文件中编写上述脚本，并在终端中执行该命令

`gcc -o hello hello.c`

如果你在 repl.it 中，点击* *运行** 按钮，如果在本地设备上，执行该命令

`./hello`如果在 linux 或者 Mac 设备上
或者在 MS Windows 设备上
或者`hello`

如果一切正常，你会在控制台/终端看到一个 **Hello World** 文本

[![](img/eae352cdd8f3211faff7e898aa72880d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qxQFeeOP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-XZzF3zjKqLI/VHs78mMe7SI/AAAAAAAACHE/T4Vk3REwaaI/s1600/Hello_world.jpg)

如果有任何问题，请在下面的评论中告诉我👇

否则让我们进入下一章

## 了解 Hello World 计划

写代码不理解真的没用。复制粘贴不会永远有效。
那么，我们来试着理解一下上面的代码。

让我们试着从第一行
开始理解

```
#include<stdio.h> 
```

Enter fullscreen mode Exit fullscreen mode

它包括针对 C 编译器的指令和`printf`的定义，这就像是针对 C 编译器的一本字典，从这本字典中编译器知道如何处理`printf`

让我们进入下一行，我们会找到这个，

```
int main() 
```

Enter fullscreen mode Exit fullscreen mode

`int`是整数的首字母缩写

这里的`main()`是一个函数，函数中的一切都被当作一个指令，由此我们的程序知道该做什么。可能还有其他函数，但是`main()`是一个特殊的函数，在每个程序中这个函数都是首先执行的。

这个括号帮助编译器识别它是一个函数。

函数就像一个罐子，里面包含了一些特殊的指令。

在下一行我们会发现一个花括号，它表示一个函数的开始，就像一个罐子的盖子。

在下一行我们会找到

```
printf("Hello World"); 
```

Enter fullscreen mode Exit fullscreen mode

`printf`是一个也像函数但预定义的东西，它的引号里面的东西会被写进去。运行程序时的控制台窗口。

括号`()`表示`printf`功能的开始和结束

我们看到一个文本，`Hello World\n`在引号内，这是将被写入控制台窗口的文本，在最后有一个`\n`告诉我们的程序开始新的一行，并将光标放在新的一行。

> 尝试更改文本并运行它

实际上这并不是必须的，但是当处理一个需要打印两行或更多行的程序时，这是必须的。但是你应该把它作为一个好的练习。

> 你会在行尾看到一个分号`;`,这不是为了风格，而是必要的，它帮助编译器理解这一行现在已经结束了。

在下一行我们会找到

```
return 0; 
```

Enter fullscreen mode Exit fullscreen mode

这实际上不是对我们来说，而是对机器来说，它返回 0 给机器，这就是机器如何理解程序成功运行，现在结束。

> 如果一个程序返回一个除之外的数字，计算机将认为它没有成功运行。试着改变它并运行它。

最后，在结尾，`}`花括号表示我们的主函数结束了。

### 必读此评论:【https://dev.to/tux0r/comment/4o9i】T2

* * *

伙计们，现在就这样，如果你们有任何问题，请在下面的评论中告诉我👇

* * *

如果你喜欢我的作品(我的文章、故事、软件、研究等等),考虑给我买一杯☕咖啡🤗