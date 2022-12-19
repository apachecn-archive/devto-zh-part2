# 新手外壳脚本 02

> 原文：<https://dev.to/bardiniaz/shell-scripting-for-newbies-02-inj>

### 变量

通过简单的变量，您可以...

*   使用 **=** 保存数值
*   找到那些在你的**回声**中输入 **$** 的值

*“一个眼神胜过千言万语”*

```
#/bin/bash

var="Yes, did it!"
echo "Have you done it? $var" 
```

Enter fullscreen mode Exit fullscreen mode

运行这个脚本的结果是:

```
Have you done it? Yes, did it! 
```

Enter fullscreen mode Exit fullscreen mode

### 与用户交谈

但是让我们用这些概念做一些有用的事情。想象一下，如果用户知道**秘密密码**，你只能向他显示日期！您可能想问他密码，所以使用命令 **read** 来完成。

然后，如果用户键入~~有史以来最简单的~~正确密码“dog”，则显示日期，否则，程序关闭。让我们先看一下代码。

```
#/bin/bash

echo "What's THE SECRET PASSWORD?"

#Puts the user's answer in the variable answer
read answer

#If answer is dog dog, print date!
test "$answer" == "dog" && date 
```

Enter fullscreen mode Exit fullscreen mode

现在，你要明白:

*   运算符 **==** 表示**等于**
*   运算符 **& &** 表示**和**

由于我们已经用命令 **read** 将用户的答案保存在变量 **$answer** 中，当我们将变量 **$answer** 与**" dog】**进行比较时，如果它们相同，它将打印日期；否则，它结束程序(因为没有更多的命令要执行)。

因此，脚本的结果必须如下所示:

用正确的密码:

```
What's THE SECRET PASSWORD?
dog
Ter Jul 17 23:32:39 -03 2018 
```

Enter fullscreen mode Exit fullscreen mode

密码错误:

```
What's THE SECRET PASSWORD?
cat 
```

Enter fullscreen mode Exit fullscreen mode

### 我不服气！

所以让我们再多破解一点这个代码吧！

```
#/bin/bash

echo "What's THE SECRET PASSWORD?"

#Puts the user's answer in the variable answer
read answer

#REVERSE LOGIC!
#If answer is not dog, exit; Else, print date!
test "$answer" != "dog" && exit
date 
```

Enter fullscreen mode Exit fullscreen mode

这里的情况不同:

*   操作员**！=** 表示**与众不同**

现在我们用相反的逻辑来做同样的事情。这一次，如果用户的回答与**【狗】**不同，命令**退出**只能**发生。否则，它将继续运行脚本，并最终到达命令日期**。****

 ****结果是**与前面脚本中的**完全相同！

## 关于

你可以在[我的 github](https://github.com/azbardini/shellscript) 里找到我所有的脚本！

更多文章关注我！****