# 从头开始:学习足够的 Bash 来编写自己的脚本

> 原文：<https://dev.to/ahmedmusallam/bash-from-scratch-learn-enough-bash-to-write-your-own-scripts-189f>

> 你可能应该读一下 [@maxwell_dev 的](https://dev.to/maxwell_dev)帖子:[我希望在这之前有](https://dev.to/maxwell_dev/the-shell-introduction-i-wish-i-had-551k)的 Shell 介绍

我发现自己总是需要编写 shell 脚本，因为它们可以非常方便地自动化任务。或者，最重要的是，因为我忘记了执行某项操作的完整命令，不想继续查找文档；要么是因为医生很烂，要么是我太懒了:)

有时我运行的命令很长或者不友好。比如这个 maven 命令:

```
mvn clean install -PautoInstallPackage,-codeQuality,-frontend -Dcrx.user=user1 -Dcrx.password=pass1 
```

Enter fullscreen mode Exit fullscreen mode

这是不直观的，我想要更简单的东西，如:

```
./install -codeQuality -frontend --user user1 --password pass1 
```

Enter fullscreen mode Exit fullscreen mode

或者更短:

```
./install -c -f -u user1 -p pass1 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可能会想，这比实际的 maven 命令好多少呢？嗯:

1.  我将文档添加到我的`install`脚本中。我在运行`install help`时打印文档
2.  我的脚本打印出它们执行的全部命令。
3.  命令本身是脚本的一部分，我总是可以在我的脚本中找到它。

既然已经解决了这个问题，让我们来学习一些 shell 脚本吧！我将向您介绍一些可以让您立即编写脚本的东西！

## 基础知识

### 创建可执行文件

为了编写脚本并执行它，脚本文件必须具有执行[权限](http://linuxcommand.org/lc3_lts0090.php)。因此，让我们创建一个文件，并给它执行权限

`cd`进入测试目录并运行

```
# create a new file called `script.sh`
touch script.sh
# add `execution` permission to the script to make it executable 
chmod +x script.sh 
```

Enter fullscreen mode Exit fullscreen mode

在同一个目录中运行脚本:

```
./script 
```

Enter fullscreen mode Exit fullscreen mode

> `./`是必要的，它告诉你的终端在当前目录中查找，而不是默认的脚本目录。

现在，在您最喜欢的编辑器中打开该文件，让我们开始学习语法。

### [射棒](https://en.wikipedia.org/wiki/Shebang_%28Unix%29)！

TL；DR；在脚本文件的第一行添加`#!/usr/bin/env bash`,将该文件标记为 bash 可执行文件。

> 你*可以*使用`#!/usr/bin/env sh`来获得[的可移植性](https://en.wikipedia.org/wiki/Shebang_(Unix)#Portability)，但是我们在这里专门使用 bash。

### 打印东西

要将东西打印到终端，可以使用 [`echo`](http://www.linfo.org/echo.html) 或 [`printf`](https://linuxconfig.org/bash-printf-syntax-basics-with-examples)

很简单，你扔给它什么，它就会“发出回声”:

例如，让我们从上面的`script.sh`开始，打印“hello world”

```
#!/usr/bin/env bash
echo hello world 
```

Enter fullscreen mode Exit fullscreen mode

运行它:`./script.sh`，它将打印:`hello world`。

`printf`用于打印和“格式化”字符串，因此末尾有`f`。这里可以找到几个简单的例子
对我来说最大的用例是使用新的行说明符`\n`打印一个新行。

示例:

```
#!/usr/bin/env bash
printf "hello\nworld\n" 
```

Enter fullscreen mode Exit fullscreen mode

印刷品:

```
hello
world 
```

Enter fullscreen mode Exit fullscreen mode

### 注释

任何以`#`开头的行都是注释。

### 声明变量

简单:

```
MY_VARIABLE="my value" 
```

Enter fullscreen mode Exit fullscreen mode

> 重要提示:`=`前后没有空格。Shell 使用空格作为命令参数的分隔符。

### 参数扩展

> 参见[本](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)和[本](http://wiki.bash-hackers.org/syntax/pe)了解更多信息。

#### *引用*或替代变量:

例如:

```
#!/usr/bin/env bash

FAV_FRUIT="apples"
echo FAV_FRUIT

# prints `FAV_FRUIT` 
```

Enter fullscreen mode Exit fullscreen mode

while:

```
#!/usr/bin/env bash

FAV_FRUIT="apples"
echo $FAV_FRUIT

# prints `apples` 
```

Enter fullscreen mode Exit fullscreen mode

你看出区别了吗？是啊！`FAV_FRUIT` vs `$FAV_FRUIT`。

也可以在字符串中使用`$`:

```
#!/usr/bin/env bash

FAV_FRUIT="apples"
I_LIKE_BLANK="I like $FAV_FRUIT"
echo $I_LIKE_BLANK

# prints `I like apples` 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:在上面所有的例子中，你可以用`${variable name}`代替`$`来达到同样的效果。所以`$FAV_FRUIT`和`${FAV_FRUIT}`是一样的
> 
> 第二个注意:注意我们如何在一个字符串中使用`$FAV_FRUIT`？那是给你的字符串模板！

#### 使用默认值

如果变量为空或未定义，则使用某个默认值。最好用一个例子来说明:

```
#!/usr/bin/env bash

SHIRT_COLOR=""
COLOR="${SHIRT_COLOR:-red}"
echo $color

# prints: `red` since SHIRT_COLOR is empty 
```

Enter fullscreen mode Exit fullscreen mode

再比如:

```
#!/usr/bin/env bash

DEFAULT_COLOR="red"
SHIRT_COLOR=""
MY_SHIRT_COLOR="My shirt color is ${SHIRT_COLOR:-$DEFAULT_COLOR}"
echo $MY_SHIRT_COLOR

# prints: `My shirt color is red` 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:注意我们是如何在字符串中使用`${SHIRT_COLOR:-$DEFAULT_COLOR}`的吗？那是给你的字符串模板！

### 从终端向我们的脚本传递变量

要传递变量，可以在运行脚本之前声明它。
这对于传递环境变量特别有帮助。例如:下面的脚本需要一个`FRUIT`，并将打印`I Like <FRUIT>`。如果`FRUIT`未定义，使用`Apricot`。

```
#!/usr/bin/env bash

DEFAULT_FRUIT="Apricot"
FRUIT=${FRUIT:-$DEFAULT_FRUIT}
echo "I Like $FRUIT" 
```

Enter fullscreen mode Exit fullscreen mode

现在你在运行`./script`之前声明`FRUIT="Oranges"`:

```
FRUIT="Oranges" ./script 
```

Enter fullscreen mode Exit fullscreen mode

其中打印:`I Like Oranges`

因为默认是`Apricot` :

```
./script 
```

Enter fullscreen mode Exit fullscreen mode

印刷品:`I Like Apricot`

## 越高级的东西

### If 语句

#### 语法

##### 如果那么

```
if [[ <some test> ]]; then
  <commands>
fi 
```

Enter fullscreen mode Exit fullscreen mode

##### 如果别的

```
if [[ <some test> ]]; then
  <commands>
else
  <other commands>
fi 
```

Enter fullscreen mode Exit fullscreen mode

##### 如果 elseif else

```
if [[ <some test> ]]; then
  <commands>
elif [[ <some test> ]]; then
  <different commands>
else
  <other commands>
fi 
```

Enter fullscreen mode Exit fullscreen mode

#### 测试

上图中的`<some test>`可以用一个测试条件来代替。您可以在终端中输入`man test`来查看所有可用的`test`条件

以下是一些评估为真的示例条件:

| 试验 | 描述 |
| --- | --- |
| `[[2 -gt 1]]` | 2 大于 1。对应方`-lt`:小于，比。 |
| `[[2 -eq 2]]` | 2 等于 2 |
| `[[3 -ge 3]]` | 3 大于或等于 3。对应方`-le`:小于或等于 |
| `[[-n "hello"]]` | “hello”的长度大于 0 |
| `[[-z ""]]` | “”的长度为 0 |
| `[["apple"= "apple"]]` | 字符串，“苹果”等于字符串“苹果”。(`-eq`比较数字，而`=`比较字符) |
| `[["apple"!= "apple1"]]` | 字符串“apple”不等于字符串“apple1” |

使用通配符的示例:
- `[[ "watermelon" = *"melon"* ]]`:字符串“西瓜”包含“甜瓜”

> 因为这篇文章是关于`bash`而不是一般的 shell，所以我们使用 bash 的双方括号`[[]]`。阅读[这个](https://www.mkssoftware.com/docs/man1/test.1.asp)和[这个答案](https://stackoverflow.com/questions/2188199/how-to-use-double-or-single-brackets-parentheses-curly-braces)了解更多信息。

[本文](https://linuxacademy.com/blog/linux/conditions-in-bash-scripting-if-statements/)是进一步测试条件的良好资源

继续例子和水果的精神，这里有一个例子:

```
#!/usr/bin/env bash

if [[ ${#FRUIT} -gt 4 ]]    # FRUIT character count is greater than 4
then echo "[$FRUIT]: yay! your fruit has more than 4 characters!"
elif [[ ${#FRUIT} -lt 4  ]] # FRUIT character count is less than 4
then echo "[$FRUIT]: Unbelievable... your fruit has less than 4 characters..."
else                      # FRUIT character count must be 4
    echo "A fruit with exactly 4 characters, how precious!"
fi 
```

Enter fullscreen mode Exit fullscreen mode

下表显示了上述脚本的命令和输出:

| 命令 | 输出 |
| --- | --- |
| `FRUIT="Apple" ./script.sh` | `[Apple]: yay! your fruit has more than 4 characters!` |
| `FRUIT="Fig" ./script.sh` | `[Fig]: Unbelievable... your fruit has less than 4 characters...` |
| `FRUIT="Pear" ./script.sh` | `A fruit with exactly 4 characters, how precious!` |

> 注意参数扩展`${#FRUIT}`得到了`FRUIT`的字符长度

以下是一些有用的运算符

### 解析自变量(`while`和`case`语句)

这可能是最精彩的部分。现在你已经学会了一些很酷的技巧，让我们来看看用某种 API 传递参数。

我们在这一部分的目标是创建一个脚本来打印关于用户的信息。例如，下面的命令:

```
./script --name Ahmed --height 6ft --occupation "Professional Procrastinator" --username coolestGuyEver23 
```

Enter fullscreen mode Exit fullscreen mode

其中将打印:

```
Hello, my name is Ahmed. I'm 6ft tall.
I work as a Professional Procrastinator and my username is coolestGuyEver23 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，参数的顺序并不重要

我们开始吧！

首先，我们需要一种方法来解析变量的参数`--name Ahmed --height 6ft --occupation= Developer --username coolestGuyEver23`。见[本如此回答](https://stackoverflow.com/questions/192249/how-do-i-parse-command-line-arguments-in-bash)

首先让我们看看 bash 的内置`$#`和`shift`
`$#`返回传递的参数数量。比如:

```
#!/usr/bin/env bash

echo $# 
```

Enter fullscreen mode Exit fullscreen mode

如果运行`./script arg1 arg2 arg3`，输出将是`3`

[T2`shift`](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_09_07.html)

> shift 命令是 Bash 附带的 Bourne shell 内置命令之一。这个命令有一个参数，一个数字。位置参数向左移动这个数字 N。从 N+1 到$#的位置参数被重命名为从$1 到$# - N+1 的变量名。

这个用一个例子来解释更好:

```
#!/usr/bin/env bash

echo $#
shift 2
echo $#
shift
echo $#
echo $@ 
```

Enter fullscreen mode Exit fullscreen mode

运行`./script arg1 arg2 arg3 arg4 arg5`(总共 5 个参数)打印:

```
5
3
2
arg4 arg5 
```

Enter fullscreen mode Exit fullscreen mode

啊？所以`shift 2`基本上去掉了`arg1`和`arg2`，然后我们再次执行`shift`，去掉了`arg3`。*注意`$@`* 是另一个打印参数的内置函数。

现在是告诉你如何访问类似数组的参数的好时机。您可以使用`$1`作为第一个参数，`$2`作为第二个参数，`$3`作为第三个参数..诸如此类。

示例:

```
#!/usr/bin/env bash

echo $1
echo $3
echo $2 
```

Enter fullscreen mode Exit fullscreen mode

跑步:

```
./script arg1 arg2 arg3 
```

Enter fullscreen mode Exit fullscreen mode

印刷品:

```
arg1
arg3
arg2 
```

Enter fullscreen mode Exit fullscreen mode

我们如何使用所有这些信息？看看这个:

```
while [[ $# -gt 0 ]]; do key="$1"
    echo $key
    shift done 
```

Enter fullscreen mode Exit fullscreen mode

现在可能已经点击了！使用上面的`while`循环，用退出条件`[[ $# -gt 0 ]]`和`shift`减少`$#`，我们可以循环所有传递的参数！

让我们用一个 case 语句让它变得更好，让我们从简单开始，只解析`name`和`height`

```
#!/usr/bin/env bash

while [[ $# -gt 0 ]]
do key="$1"
case $key in
    # parse name arg
    -n|--name)
        NAME="$2"   # the value is right after the key
        shift       # past argument (`--name`)
        shift       # past value
    ;;
    # parse height arg
    -h|--height)
        HEIGHT="$2"
        shift # past argument
        shift # past value
    ;;
    # unknown option
    *)
        echo "Unknown argument: $key"
        shift # past argument
    ;;
esac
done echo "NAME: $NAME"
echo "HEIGHT: $HEIGHT" 
```

Enter fullscreen mode Exit fullscreen mode

> 注意事项:
> 
> 1.  每种情况都用管道`|`隔开，例如`-n|--name)`匹配`-n`或`--name`
> 2.  我们使用了两次`shift`来传递参数标志和参数值
> 3.  更多关于 [`case`声明](http://wiki.bash-hackers.org/syntax/ccmd/case)
> 4.  使用`;;`找到第一个匹配后，我们将退出。匹配未知选项在底部。

Rut it:

```
./run.sh --name Ahmed --height 6ft 
```

Enter fullscreen mode Exit fullscreen mode

或者更短

```
./run.sh -n Ahmed -h 6ft 
```

Enter fullscreen mode Exit fullscreen mode

印刷品:

```
NAME: Ahmed
HEIGHT: 6ft 
```

Enter fullscreen mode Exit fullscreen mode

综上所述:

```
#!/usr/bin/env bash

while [[ $# -gt 0 ]]
do key="$1"
case $key in
    # parse name arg
    -n|--name)
        NAME="$2"   # the value is right after the key
        shift       # past argument (`--name`)
        shift       # past value
    ;;
    # parse height arg
    -h|--height)
        HEIGHT="$2"
        shift        # past argument
        shift        # past value
    ;;
    # parse user arg
    -u|--user)
        USER="$2"
        shift # past argument
        shift # past value
    ;;
    # parse occupation argument
    -o|--occupation)
        OCCUPATION="$2"
        shift # past argument
        shift # past value
    ;;
    # parse code quality argument
    -u|--username)
        USERNAME="$2"
        shift # past argument
        shift # past value
    ;;
    # unknown option
    *)
        echo "Unknown argument: $key"
        shift # past argument
    ;;
esac
done echo "Hello, my name is $NAME. I'm $HEIGHT tall.
I work as a $OCCUPATION and my username is $USERNAME" 
```

Enter fullscreen mode Exit fullscreen mode

并运行它:

```
./run.sh --name ahmed --height 6ft --occupation "professional procrastinator" --username coolestGuyEver23 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
./run.sh -n ahmed -h 6ft -o "professional procrastinator" -u coolestGuyEver23 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
Hello, my name is ahmed. I'm 6ft tall.
I work as a professional procrastinator and my username is coolestGuyEver23 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，还记得上面的[使用默认值](https://dev.tousing-a-default-value)一节吗？如果没有传递参数，您可以使用它来添加默认值。

### 评估命令

你可以将一个命令汇编成一个字符串，然后使用`eval`来执行它。例如，还记得帖子开头的那个长长的 maven 命令吗？

```
mvn clean install -PautoInstallPackage -Dcrx.user=user1 -Dcrx.password=pass1 
```

Enter fullscreen mode Exit fullscreen mode

我们可以把它拆开，然后组装起来:

> 假设我已经解析了`PROFILE`、`USER`和`PASSWORD`T3

```
PROFILE="${PROFILE:-autoInstallPackage}"
USER="${USER:-user1}"
PASSWORD="${PASSWORD:-pass1}"

COMMAND="mvn clean install -P$PROFILE -Dcrx.user=$USER -Dcrx.password=$PASSWORD"

eval $COMMAND 
```

Enter fullscreen mode Exit fullscreen mode

该命令将被执行。

> 可能不是所有情况下的最佳选择。在这种情况下完全没问题，因为我是唯一运行脚本的人，而不是最终用户。所以在任何编程语言中使用`eval`时都要小心。

上面的脚本可以不用`eval`重写为:

```
PROFILE="${PROFILE:-autoInstallPackage}"
USER="${USER:-user1}"
PASSWORD="${PASSWORD:-pass1}"
mvn clean install "-P$PROFILE" "-Dcrx.user=$USER" "-Dcrx.password=$PASSWORD" 
```

Enter fullscreen mode Exit fullscreen mode

*感谢[@ lietux](https://dev.to/lietux)T3】*

### 功能

[功能](http://tldp.org/LDP/abs/html/functions.html)简单明了。下面是一个打印传递给它的第一个参数的函数:

```
#!/usr/bin/env bash

printFirstOnly(){
  echo $1
}
# execute it
printFirstOnly hello world 
```

Enter fullscreen mode Exit fullscreen mode

仅打印`hello`

> 使用变量`$N`访问函数参数，其中`N`是参数编号。

我喜欢用函数来漂亮地打印东西:

```
pretty() {
  printf "***\n$1\n***\n"
}

pretty "hi there" 
```

Enter fullscreen mode Exit fullscreen mode

印刷品:

```
***
hi there
*** 
```

Enter fullscreen mode Exit fullscreen mode

但是，您可以将它用于任何目的。

## 结论

如果你看到了这个，那么我做对了一些事情:)我很想听听你对这个帖子的反馈。是有组织的吗？散了？有意义吗？有帮助吗？我能做些什么来改善它？