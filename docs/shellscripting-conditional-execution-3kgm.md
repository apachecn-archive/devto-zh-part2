# shell 脚本:条件执行

> 原文：<https://dev.to/puritanic/shellscripting-conditional-execution-3kgm>

[![Alt text of image](img/d59c433d6576de368e4b6e0ffc71e895.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t6fTV7OT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mzfpomwt6xfadtk9lpei.png)

这是 shell 脚本系列的第二部分。如果你错过了，你可以在这里找到第一部分[。](https://dev.to/puritanic/shellscripting-27bl)

*另外，请注意大部分代码只在`bash`和`zsh`shell 中测试过，可能无法在其他 shell 中运行。*

**条件执行**是指只有满足一定的条件，你才可以选择执行代码。如果没有这个功能，您所能做的就是一个接一个地执行命令。测试关于系统状态和流程环境变量的各种事情的能力意味着 shell 脚本可以做比其他方式更强大的事情。在这篇文章中，我们将探索`test operators`、`if/then/else`条件句和`case`语句。

### `test`又名`[`

通过测试，我们可以检查例如:如果文件存在，如果一个数字大于另一个，比较字符串是否相等...

语法:

```
[ condition-to-test-for ] 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
[ -e /etc/passwd ] 
```

Enter fullscreen mode Exit fullscreen mode

正如上面的标题所说，`test`的别称是`[`。它也是一个内置的 shell(这意味着 shell 本身会将`[`解释为`test`，即使您的 Unix 环境设置不同)。当`[`被调用时，它需要一个`]`括住它的参数，否则，它做同样的工作。

```
test  -e /etc/passwd
# as above so below
[ -e /etc/passwd ] 
```

Enter fullscreen mode Exit fullscreen mode

测试`etc/passwd`是否存在，如果存在，返回`true`-`0`的命令退出状态。如果不存在，该命令将退出，退出状态为`1`(下一篇文章将详细介绍退出状态)。

**明白了**:符号`[`和`]`周围的空格是必需的！比如:
`[-e /etc/passwd ]`就不行；它被解释为`test-e /etc/passwd ]`出错，因为`]`没有开头`[`。`[`其实是一个程序，和`ls`等程序一样，必须用空格包围。
**这个故事的寓意:在所有操作符周围放置空格。**

*注意*:您可以用`!` :
反转测试结果

```
if [ ! -r “$1” ]; then echo "File $1 is not readable – skipping."; fi 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的,`test`是一个简单而强大的比较工具。要了解完整的细节，请在您的系统上运行`man test`,但这里有一些用法和典型示例:

文件测试操作符:

```
-d FILE #True if the file is a directory
-e FILE #True if the file exists
-f FILE #True if the file exists and it's regular file
-r FILE #True if the file is readable by you
-s FILE #True if the file exists and it's not empty
-w FILE #True if the file is writable by you
-x FILE #True if the file is executable by you 
```

Enter fullscreen mode Exit fullscreen mode

字符串测试运算符:

```
-z STRING #True if the string is empty
-n STRING #True if the string is not empty
STRING1 = STRING2 #True if the strings are equal
STRING1 != STRING2 #True if the strings are not equal 
```

Enter fullscreen mode Exit fullscreen mode

算术测试:

```
arg1 -eq arg2 #True if the arguments are equal
arg1 -ne arg2 #True if the arguments are not equal
arg1 -lt arg2 #True if the arg1 is less than arg2
arg1 -le arg2 #True if arg1 is less than or equal to arg2
arg1 -gt arg2 #True if arg1 is greater than arg2
arg1 -ge arg2 #True if arg1 is greater than or equal to arg2 
```

Enter fullscreen mode Exit fullscreen mode

### `&&`和`||`

可以通过使用`&&`和`||`操作符来组合测试和/或链接多个命令。它们分别执行逻辑 AND 和逻辑 OR。

*   `&& = AND`
    T1】

    当且仅当前一个命令成功时，将执行`&&`之后的命令(也就是以`0`退出状态退出)。

*   `|| = OR`
    T1】

    `||`操作符执行逻辑 or，所以当只满足其中一个条件而不满足哪一个条件时，这是要使用的特性。

```
#! /bin/bash
HOST="google.com"
ping -c 1 $HOST && echo "$HOST reachable." 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 如果/那么

几乎每种编程语言都有 if/then/else 结构，shell 也不例外。语法使用方括号来执行测试，并且需要`then`和`fi`语句，就像 C 和其他一些语言中的{和}花括号一样。

语法:

```
if [ condition ]
then statements for when the condition is true fi 
```

Enter fullscreen mode Exit fullscreen mode

除了`then`后面的换行符，所有这些换行符都是必需的，或者可以用分号替换。提醒:`[`和`]`符号周围的空格也是必需的，所以这最多可以减少(请不要)为:
`if [ condition ];then statements;fi`

> 使用分号将 then 和 if 放在同一行是很常见的。

示例:

```
MY_SHELL="zsh"

if [ "$MY_SHELL" = "zsh" ]
    then echo "You are the zsh shell user!"
fi 
```

Enter fullscreen mode Exit fullscreen mode

#### 目不斜视

如果可能的话，您可能想要运行该命令，但是如果无法运行，那么就继续执行脚本。一种(更简单也是最常见的)方法是使用 ELSE 语句:

```
if [ condition ]; then statements for when the condition is true else statements for when the condition is false fi 
```

Enter fullscreen mode Exit fullscreen mode

```
#!/bin/bash

# Check for likely causes of failure
if [ -r "$1" ]; then cat "$1"
else echo "Error: $1 is not a readable file."
fi 
```

Enter fullscreen mode Exit fullscreen mode

这个代码片段试图`cat`传递给它的文件作为它的第一个参数(`"$1"`用双引号括起来，以允许文件名包含空格)，如果没有这样做，就抛出一个错误消息。

#### ELIF

`elif`是一个构造，允许您向`if`语句的`else`部分添加条件。它是“else if”的缩写，这样一长串可能的动作就可以写得更简洁。这使得编写更容易，阅读更容易，最重要的是，调试更容易。

```
#!/bin/bash 
OS=`uname -s`
if [ "$OS" = "FreeBSD" ]; then echo "This Is FreeBSD"
elif [ "$OS" = "CYGWIN_NT-5.1" ]; then echo "This is Cygwin"
elif [ "$OS" = "SunOS" ]; then echo "This is Solaris"
elif [ "$OS" = "Darwin" ]; then echo "This is Mac OSX"
elif [ "$OS" = "Linux" ]; then echo "This is Linux"
else echo "Failed to identify this OS"
fi 
```

Enter fullscreen mode Exit fullscreen mode

这比可能变成的嵌套代码地狱更具可读性。

* * *

### `case`声明

`case`为`if/then/else`结构提供了一个更干净、更容易编写、可读性更好的替代方案，特别是当有很多可能的值需要测试的时候。使用 case，您可以列出想要识别和操作的值，然后为每个值提供一个代码块。【case 语句的一个常见应用是系统启动脚本。
语法:

```
case "$VAR" in pattern_1)
        # Some commands here.
        ;; # Execution will stop when the double semicolon is reached 
    pattern_n)
        # Some commands here.
        ;;
esac 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
#!/bin/bash
OS=`uname -s`

case "$OS" in FreeBSD) echo "This is FreeBSD" ;;
    CYGWIN_NT-5.1) echo "This is Cygwin" ;;
    SunOS) echo "This is Solaris" ;;
    Darwin) echo "This is Mac OSX" ;;
    Linux) echo "This is Linux" ;;
    *) echo "Failed to identify this OS" ;;
esac 
```

Enter fullscreen mode Exit fullscreen mode

尽管看起来像是一个特殊的指令，但`*`只是最通用的通配符，因为它可以匹配任何字符串。这也表明我们能够进行更高级的模式匹配，例如`RegEx`。

> 请注意，模式是区分大小写的。

`case`的 bash 实现的一个不太为人所知的特性是，可以用`;;&`或`;&`来结束语句，而不仅仅是用`;;`。虽然`;;`意味着其他语句都不会被执行，但是如果您用`;;&`结束一条语句，所有后续的情况仍然会被评估。如果您以`;&`结束一个语句，则该案例将被视为已匹配。

```
#!/bin/bash

read -p "Give me a word: " input
echo -en "That's "
case $input in
  *[[:digit:]]*) echo -en "numerical " ;;&
  *[[:lower:]]*) echo -en "lowercase " ;;&
  *[[:upper:]]*) echo -en "uppercase " ;;&
  *) echo "input." ;;
esac

$ ./case1.sh
Give me a word: Hello 123
That's numerical lowercase uppercase input. 
```

Enter fullscreen mode Exit fullscreen mode

> 这个特性是特定于 bash shell 的；这不是 Bourne shell 的一个标准特性，所以如果您需要编写一个可移植的脚本，不要期望它能工作。这将导致其他 shells 出现语法错误消息。

* * *

这篇文章涵盖了控制条件执行的各种方法——从简单的`if/then/else construct`,到可以用`test`完成的不同事情，再到更灵活的用于匹配不同输入集的`case`语句。

我必须承认，开始时写关于 shell 编程的文章看起来最多只有两三篇，但是有很多内容需要讨论，甚至这篇和以前的文章都不到我所写主题的一半。如果你想更深入地学习 shell 编程，我强烈推荐[高级 Bash 脚本指南](http://tldp.org/LDP/abs/html/index.html)和[经典 Shell 脚本](http://shop.oreilly.com/product/9780596005955.do)。我将在下一篇文章中写一些关于位置参数、退出代码和(希望)函数的内容。感谢阅读！