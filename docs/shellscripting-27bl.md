# 外壳脚本

> 原文：<https://dev.to/puritanic/shellscripting-27bl>

[![Semi random image](img/c50aff2132b9f071504b3474a43e4439.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tstJ4oxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z5a2a7yzn0pmc10w703n.jpg)

## **变量和设帮**

* * *

### shell 编程小入门

Shell 是每个 Unix 和 Linux 系统的标准接口；用户和管理员都有使用 shell 的经验，将命令组合成脚本是一个自然的过程。然而，这只是冰山一角。

我最近花了一些时间学习 shell 和编写脚本，我意识到 shell 实际上是一种完整的编程语言，具有变量和函数，以及更高级的结构，如数组(包括关联数组)，并且与内核直接链接，它的语法中内置了本机 IO 原语，以及进程和作业控制。

我已经计划了一系列的文章，我会尽量简明扼要。

*那么，什么是 shellscripting 呢？*

Shellscripting 是编写一系列命令供 shell 执行。它可以将冗长和重复的命令序列合并成一个简单的脚本，可以随时存储和执行，这对于自动化任务非常有用。这减少了最终用户所需的工作量。

脚本的命令由解释器(shell)一个接一个地执行，您可以在命令行中输入的任何内容也可以放入脚本中。

> 在运行脚本之前，我们需要使用`chmod 755 script.sh`设置执行权限
> 
> 然后，我们可以通过命令行运行带有`./script.sh`的脚本。

* * *

### #！

`#!` (shebang)指定我们要执行脚本的 shell(解释器)的二进制，例如:
`#! /bin/bash`、`#! /bin/zsh`或者为了最好的移植性`#! /bin/sh`(这样会运行系统 shell)。

*注意，本系列中的大部分代码只使用了`bash`和`zsh` shell 进行测试，大多数 sh 脚本无需修改就可以在 Bash 上运行，但是有些东西无法运行。*

```
#! /bin/sh
sleep 90 
```

Enter fullscreen mode Exit fullscreen mode

当我们执行包含`#!`的脚本时，实际发生的是解释器被执行，用于调用脚本的路径作为参数被传递。为了确认这一点，假设我们有`sleepy.sh`脚本，那么我们可以用`./sleepy.sh &`运行脚本，其中`&`用于(似乎)返回脚本执行进程的 PID，然后我们可以运行`ps -fp [PID]`来查看进程信息:

```
UID PID PPID C STIME TTY TIME CMD
505 65418 59985 0 7:09PM ttys000 0:00.01 /bin/zsh ./sleepy.sh 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到,`./sleepy.sh`作为参数传递给了我的`/bin/zsh`二进制文件。

如果脚本不包含`#!`命令，则使用默认 shell 执行，但最好是显式的，因为不同的 shell 有稍微不同的语法。

此外，我们不必只使用 shells 作为脚本的解释器。我们也可以使用其他二进制文件，比如`python` :

```
#! /usr/bin/python

print "This is a Python script" 
```

Enter fullscreen mode Exit fullscreen mode

```
chmod 755 hi.py
./hi.py
This is a Python script 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 变量

变量是有名称的存储位置，您可以将它们视为名称-值对。
用来创建变量的语法是:`VARIABLE_NAME="Value"`。值得注意的是，变量名区分大小写，按照惯例，变量名应该全部大写。在声明变量时，也要确保不要在`=`符号前后使用空格。

默认情况下，所有变量都是全局的，而且在使用前必须定义。
变量可以在函数中定义(我们最终会谈到它们)，但是在函数被调用之前我们不能访问它们。

```
function var(){
    FUNC_VAR=1
}
# FUNC_VAR is not defined at this point and this will not return anything
echo $FUNC_VAR
var # This is how we call a function in the shell
# FUNC_VAR is now available because the function has been called
echo $FUNC_VAR # Output: 1 
```

Enter fullscreen mode Exit fullscreen mode

有效的变量名可以由字母、数字和下划线组成，但数字不能是名称的第一个字符。

```
# Valid names
DARK_JEDI="Vader"
GR4Y_J3DI="Ahsoka"
Regular_Jedi="Obi-Wan"

# Invalid names
3DARK_LORDS="Vader Sidius Plagueis"
TWO-REBELS="Solo Leia"
ONE@SHIP="Ebon Hawk" 
```

Enter fullscreen mode Exit fullscreen mode

```
#! /bin/bash
MY_SHELL="zsh"
echo "I like the $MY_SHELL shell" # Output: I like the zsh shell 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以用花括号将变量名括起来:

```
MY_SHELL="zsh"
echo "I like the ${MY_SHELL} shell" # Output: I like the zsh shell 
```

Enter fullscreen mode Exit fullscreen mode

花括号语法是可选的，除非你需要在变量之前或之后添加额外的数据，比如:

```
MY_SHELL="bash"
echo "I'm ${MY_SHELL}ing on my keyboard!" # Output: I'm bashing on my keyboard. 
```

Enter fullscreen mode Exit fullscreen mode

如果没有花括号，这将不起作用，因为解释器将把名称变量后面的`ing`作为变量名称的一部分。

另一个最佳实践是在使用变量时，用引号将变量括起来，以防止一些意想不到的副作用。

我们也可以将命令的输出赋值给一个变量:

```
SERVER_NAME=$(hostname)
echo "You are running this script on ${SERVER_NAME}" 
```

Enter fullscreen mode Exit fullscreen mode

#### 局部变量

局部变量是用`local`关键字创建的，只有**函数可以有局部变量**，所以它们只能在声明它们的函数中被访问。

```
function myFunc(){
    local LOCAL_VAR=" I'm locally scoped"
} 
```

Enter fullscreen mode Exit fullscreen mode

在函数中只使用局部变量是最佳实践。

* * *

现在就这样，我将在下一篇文章中写一些关于测试和循环的内容。感谢您的阅读，如果您有任何问题，请尽管问吧！