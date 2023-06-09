# 世界上最小的蒯因，“保证”

> 原文：<https://dev.to/awwsmm/worlds-smallest-quine-guaranteed-b5m>

计算机科学中的蒯因是一个程序，它不接受任何输入，只输出一份源代码。有“quine relays”或“ouroboros programs ”,它们用不同的编程语言产生一系列连续的文件，最终回到原始源代码。例如，Java 程序可以产生 C 程序作为其唯一的输出，当运行 C 程序时，产生原始 Java 程序作为其唯一的输出。一个特别惊人/漂亮的例子是 [mame 的 quine-relay](https://github.com/mame/quine-relay) ，它目前在一个循环中包含 128 种不同的编程语言。

长蒯因继电器很容易写，但本着[代码高尔夫](https://codegolf.stackexchange.com/)的精神，任何语言中最短的蒯因是什么(深奥的或其他的)？下面的例子可以在 [this codegolf.stackexchange 线程](https://codegolf.stackexchange.com/questions/69/golf-you-a-quine-for-great-good)中找到。(注意，这里字符被认为是 8 位 ASCII 字符。)

# C (60 字节)

```
main(s){printf(s="main(s){printf(s=%c%s%1$c,34,s);}",34,s);} 
```

Enter fullscreen mode Exit fullscreen mode

在这里试试吧！

## 解释:

省略了返回类型的`main(s)`被大多数 C 编译器接受(但有时会给出警告)，这些编译器只是寻找一个`main`方法，并假设返回类型是`int`。这是一个更老的(1989 年以前)风格的 C 语法，仍然被一些编译器接受。

`s`由 C 编译器自动转换成必要的类型，原因与上面给出的相同。`s="main(s){printf(s=%c%s%1$c,34,s);}"`设置`s`等于给定的字符串。

`printf(s=..., 34, s);`首先将 ASCII 字符#34 ( `"`)替换为`%c`，所以现在要打印的字符串是`main(s){printf(s="%s%1$c,34,s);}`。

传递给`printf`、`s`的第三个参数，整个字符串，现在被代入`%s`。`s`在`printf`语句开始时被初始化，所以从那以后就没变过。所以现在要打印的字符串变成了`main(s){printf(s="main(s){printf(s=%c%s%1$c,34,s);}%1$c,34,s);}`。

最后，来自原始`printf`函数调用的`%1$c`被解析。`%1`获取第一个参数(`34`),`$c`将其格式化为一个字符。[这是从一个 POSIX 扩展到 C 语言的](https://stackoverflow.com/questions/19327441/gcc-dollar-sign-in-printf-format-string)。最后一个字符串变成(并打印为):

```
main(s){printf(s="main(s){printf(s=%c%s%1$c,34,s);}",34,s);} 
```

Enter fullscreen mode Exit fullscreen mode

...哪个是原程序！这个例子抓住了 quine 编程的精髓。

# JavaScript (36 或 53 字节)

```
!function a(){alert("!"+a+"()")}() 
```

Enter fullscreen mode Exit fullscreen mode

在这里试试吧！

## 解释:

虽然这需要用`<script></script>`标记包装起来才能在浏览器中运行，这给解决方案增加了 17 个字节，但仍然比上面显示的 C 语言示例要小。

JavaScript 中的`alert()`函数向用户发送一个警告对话框。在这种情况下，警报包含字符`!`，加上一些变量`a`，再加上字符串`()`。事实上，`a`是一个函数的名字，在上面的代码中定义了。当像这样打印一个函数时，函数的源代码只是为用户打印的。

调用`a()`的方法是在函数定义前加上感叹号(`!`)-[，把它变成一个函数表达式](https://stackoverflow.com/questions/3755606/what-does-the-exclamation-mark-do-before-the-function) -并在最后加上`()`，这导致函数被立即调用。这些需要添加回打印函数的开头和结尾，以使其成为真正的查询。

*更新:(28 或 45 字节)*

开发者对用户 [prplz](https://dev.to/prplz) 建议使用 [ES6](http://es6-features.org) :
的一个更短的 JavaScript 查询

```
(z=x=>alert(`(z=${z})()`))() 
```

Enter fullscreen mode Exit fullscreen mode

它仍然需要被包装在`<script></script>`标签中，但是它比以前的解决方案少需要 8 个字符。

# 鸡(7 个字节)

```
chicken 
```

Enter fullscreen mode Exit fullscreen mode

在这里试试吧！

## 解释:

chicken 是一种深奥的编程语言，对它来说“Chicken”是唯一有效的符号。它的灵感来自于【2002 年的这篇论文和[附带的幻灯片[*。道格·宗克(Doug Zongker)写的，本意是讽刺难以理解的会议演示。(没有原始演示的视频存在，尽管 2007 年拍摄了宗克的第二次演示视频](http://isotropic.org/papers/chicken.ppt)。)

在这种语言中，一行中的单个`chicken`编码操作#1，它将字符串`chicken`推到堆栈上。换行符(或空行)编码操作#0，它将堆栈的顶部打印到屏幕上并退出。所以脚本`chicken`执行 op1，将一个`chicken`推到栈顶，然后文件结束(EOF)执行 op0，将栈顶(`chicken`)打印到终端。一路都是鸡。

# GolfScript (2 字节)

```
1 
```

Enter fullscreen mode Exit fullscreen mode

在这里试试吧！

## 解释:

[GolfScript](http://www.golfscript.com/golfscript/tutorial.html) 是一种专门为代码 golf 编写的编程语言，在这种语言中，任务是在尽可能最短的字节数内完成的。在 GolfScript 中，当一个数字被写入代码的任何地方时，它都会被添加到堆栈的顶部。可以对堆栈中最上面的两个项目执行操作。在 GolfScript 程序结束时，堆栈的内容被打印到屏幕上，后面跟一个换行符`\n`。所以上面的代码在栈顶添加了一个`1`，然后打印它，并附加一个`\n`(因此在末尾添加了必要的换行符，使字节数加倍)。

另见:7，Applescript，memes，说真的，Y

# HQ9+ (1 字节)

```
Q 
```

Enter fullscreen mode Exit fullscreen mode

在这里试试吧！

## 解释:

HQ9+是一种深奥的编程语言，只有四个命令:

*   *H* :打印“你好，世界！”
*   *Q* :打印程序的源代码(a quine)
*   *9* :打印歌曲《99 瓶啤酒》的歌词
*   *+* :递增一个不可访问的累加器(基本不做任何事情)

扩展编程语言的定义(HQ9+不是图灵完整的)，这种语言仍然将它的查询降低到一个字节。在这一点上，很难区分哪些语言可以创建编码一些命令的“真”查询，而不是“假”查询，后者只是将它们的内容作为默认动作打印到终端，而没有实际编码一些其他命令(例如，将字符推入堆栈，等等)。).

并列:Arcyóu，Burlesque，CJam，J，TeaScript，TI-BASIC

# C，再一次(0 字节)

Enter fullscreen mode Exit fullscreen mode

## 解释:

著名的对蒯因定义的扩展是 1994 年国际混淆 C 代码竞赛(IOCCC)的参赛作品，由一个名为 smr.c 的空文件组成。“严格来说，smr.c 不是一个有效的 C 程序，它也不是一个无效的 C 程序”，评委写道，[发布免责声明](http://www0.us.ioccc.org/1994/smr.hint)未来的规则将指定最小文件大小为 1 个字符。(注意:大多数现代 C 编译器会抱怨“给定这段代码时，对“`main'`”的未定义引用”。)

* * *

所以你有它:“真”奎因可能在 1 或 2 个字符左右开始变成“假”奎因，并且由于现在有许多许多专门为 code golf 设计的深奥的编程语言，现在有许多许多 1 和 2 个字符的奎因可用。去打高尔夫吧！

这篇文章最初以稍微不同的形式出现在我的 Wordpress 博客上。