# 外壳脚本:函数

> 原文：<https://dev.to/puritanic/shellscripting-functions-2696>

这是我关于 Shellscripting 的系列文章的第三部分，你可以在这里查看以前的文章:

*   [外壳脚本:简介](https://dev.to/puritanic/shellscripting-27bl)
*   [外壳脚本:条件执行](https://dev.to/puritanic/shellscripting-conditional-execution-3kgm)

* * *

> 对于函数，我们可以说它们是 shellscript 中的 shell script。

我们正在使用函数的一个主要原因是遵循 DRY 原则，这意味着我们应该编写一个函数一次，然后我们可以多次使用它。这有时可以极大地减少脚本长度，而且维护起来也容易得多，因为我们只有一个功能来编辑和排除故障。

功能:

*   对主脚本隐藏实现细节，简化 shell 脚本的代码主体
*   如果它所处理的底层细节发生变化，则可以被替换
*   可以作为一个更大的脚本的一小部分反复测试，通过改变输入值来证明代码是正确的
*   允许一致地重用脚本中的代码，甚至是几个 shell 脚本之间的代码。

* * *

<figure>

[![xkcd](img/316b112c973b1e3b5e4eee72694b30be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EKRlNJnB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4l1bsjjtpua17f1w4v1o.png)

<figcaption>[Slightly relevant xkcd 😄](https://xkcd.com/1168/)</figcaption>

</figure>

### 退出状态

在我们深入研究函数之前，有必要知道在 shell 中执行的每个命令都返回一个范围在 0 到 255 之间的退出状态。
成功的实际状态为`0`，所有其他状态为错误状态的代码。
这个代码可以在脚本中使用，用于抛出和检查错误。通常，我们可以通过检查文档中的错误代码或查看源代码来找到各种退出状态的含义。

我们可以使用`$?`来检查之前执行的命令的退出状态。

```
$ ls dir/
$ echo $? 
```

Enter fullscreen mode Exit fullscreen mode

如果`dir`存在，`echo $?`将返回`0`状态码，否则将返回`2`，未找到目录的错误代码。

我们可以用`exit`命令:
明确定义返回代码

```
#! bin/bash
HOST="google.com"

ping -c 1 $HOST

if ["$?" -ne "0"]
then echo "$HOST unreachable"
    exit 1
fi exit 0 
```

Enter fullscreen mode Exit fullscreen mode

只需在脚本中使用`exit`命令，并在它后面跟随一个 0 到 255 之间的整数。如果我们没有用`exit`命令指定返回代码，那么 shellscript 中先前执行的命令的退出状态将被用作退出状态。如果我们完全不包括`exit`司令部，情况也是如此。

> 每当到达`exit`命令时，shell 脚本将停止运行。

所有功能都有退出状态。我们可以使用关键字
在函数中显式返回状态

```
function myFunc() {
    return 1 # returning exit status code 1
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，status 可以隐式返回函数中执行的最后一个命令的退出状态。

* * *

### 位置参数

位置参数是我们可以用来指定通过命令行传递给函数的参数的变量。例如，如果我们像这样执行脚本:

`script.sh param1 param2 param3`

在该脚本中，我们可以像这样访问所有命令行参数:

```
$0: "script.sh" # $0 is always the name of the script
$1: "param1" # $1 is the first parameter,
$2: "param2" # $2 is the second
$3: "param3" # $3 is the third, and so on... 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:您不能更改这些变量的值

一个实际例子:

```
# args.sh
#!/bin/bash
echo "I was called as $0"
echo "My first argument is: $1"
echo "My second argument is: $2"
echo "I was called with $# parameters." 
```

Enter fullscreen mode Exit fullscreen mode

```
$ ./args.sh one two
I was called as ./args.sh
My first argument is: one
My second argument is: two
I was called with 2 parameters. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用`$#`来检查脚本调用了多少参数，这是检查用户是否使用了足够数量的参数来执行脚本的好方法，例如:

```
$ cat argCheck.sh
#!/bin/bash
if [ "$#" -eq "2" ]; then echo "The script was called with exactly two parameters. Let’s continue."
else echo "You provided $# parameters, but 2 are required." 
```

Enter fullscreen mode Exit fullscreen mode

当我们想要循环脚本参数时，我们可以使用`$@`变量:

```
# This will loop through all parameter passed to the script when executed
for USER in "$@"; do passwd -l $USER # lock the account
done 
```

Enter fullscreen mode Exit fullscreen mode

* * *

#### 创建功能

让我们回到函数上来。
需要注意的是，函数必须在被调用之前定义，习惯上在文件的开头定义函数，尽管这并不是绝对必要的。
定义为函数的代码块可以用三种不同的方式来声明，这取决于所使用的具体 shell。标准的 Bourne shell 语法使用函数名，后面紧跟着一对括号`()`和花括号`{}`括住代码本身:

```
# The most common way
myFunc() {
    # Code
    echo "Hello"
}
# function keyword is optional
# so this is also correct
function mySecondFunc () {
    # More code
    echo "World"
} 
```

Enter fullscreen mode Exit fullscreen mode

还有第三种语法，Bourne shell 不接受，尽管 bash 和 ksh 都接受。函数名前面加上关键字 function:
`function myFunc`，而不是在函数名后面加上一对括号

据我所知，第一个(没有关键字`function`)是最常用的，因为它被所有 shells 接受。第二种语法也经常使用，通过使用`function`关键字，它提供了一个更清晰的声明，即它是一个函数。关于第三个，我找不到任何关于它的信息，只知道它存在:|

#### 调用函数

我们可以通过简单地在脚本中输入它的名字来调用和执行这个函数(在它被定义之后):

```
function hello() {
    echo "Hello World!"
}

hello 
```

Enter fullscreen mode Exit fullscreen mode

```
function hello() {
    echo "Hello ${1}!"
}

hello World # Output: Hello World 
```

Enter fullscreen mode Exit fullscreen mode

函数也可以调用其他函数。在这里我们可以看到,`hello`函数在声明之前调用了`now`函数，但是这没关系，因为在调用`hello`函数之前,`now`函数被读入脚本，所以按照执行顺序，它在使用之前就被定义了。

```
function hello() {
    now
}
function now() {
    echo "$(date +%r)"
}

hello # Output: 02:15:36 PM 
```

Enter fullscreen mode Exit fullscreen mode

但是，举个例子，像这样的东西是行不通的:

```
function hello() {
    now
}
hello # hello is called before now is defined
function now() {
    echo "$(date +%r)"
} 
```

Enter fullscreen mode Exit fullscreen mode

除了调用其他函数，shell 函数还可以递归调用自己。证明这一点的一个简单例子是数学阶乘函数。

```
$ cat factorial.sh 
#!/bin/bash

function factorial() {
    if [ "$1" -gt "1" ]; then previous=`expr $1 - 1` 
        parent=`factorial $previous` 
        result=`expr $1 \* $parent` 
        echo $result
    else echo 1
    fi
}
factorial $1
$ ./factorial.sh 6 720 
```

Enter fullscreen mode Exit fullscreen mode

当使用递归函数 tho 时，你应该非常小心，特别是当你在其中创建文件时，你可能会打开比系统允许的更多的文件。

函数也有`positional parameters`，`$@`也可以用来检索所有传递的参数列表。

```
# $0 is the script itself, not the function name
function helloFunc(){
    echo "Hello ${1}  ${2}!"
}
helloFunc Shell World
# Output: Hello Shell World! 
```

Enter fullscreen mode Exit fullscreen mode

> 函数也可以访问所有全局变量。但是，提醒一下，最好的做法是在函数中只使用`local`变量，以避免副作用，副作用最终会导致 bug。

* * *

关于 shellscript 中的函数还有很多要说的，所以把这篇文章看作是对它们在脚本中的用法的一个小介绍。在下一篇文章中，我会写一些关于通配符、字符类以及日志和调试 shellscript 的内容。
感谢阅读！