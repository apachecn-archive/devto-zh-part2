# 新手外壳脚本 03

> 原文：<https://dev.to/bardiniaz/shell-scripting-for-newbies-03-3a1c>

### 条件句

在上一个教程中，您已经了解了如何与用户交互，以及如何根据用户交互返回简单的输出。

讽刺的是，你的猫最近一直在用电脑要东西。当然不行，但是你可以解决这个问题！让我们和你的猫谈谈吧！

首先，我们要问他想要什么。这么办吧:

```
#/bin/bash

printf "What do you want, fella?\n"
read answer 
```

Enter fullscreen mode Exit fullscreen mode

*   提示: **printf** 是命令 **echo** 的更好替代。到现在为止，都是一样的，除了你需要在句尾写上 **\n** 才能进入下一行。

现在你已经在变量*答案*中得到他的答案，我们将看到这个新的~~地狱无~~牛逼逻辑: **if** 命令。它的工作就像写的一样简单，但是有一些注意事项。我们已经知道可以用命令 **test** 来测试一个语句的真实性，所以让我们告诉计算机如果为真的话**该做什么:** 

```
#/bin/bash

printf "What do you want, fella?\n"
read answer

#if the test is true, then it'll be printed the message for the cat
if test $answer = 'food'
then printf "There's meat in the fridge, dumbass!\n"
fi 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们真的对猫做出了反应，让它去寻找自己的食物！如果条件结束，要告诉计算机**在哪里，只需使用~~这不是开玩笑~~命令 **fi** 。有一个简单的技巧可以让代码可读性更好。我们可以交换命令**测试**到一对**左右的语句，看看:**** 

```
#/bin/bash

printf "What do you want, fella?\n"
read answer

#if the test is true, then it'll be printed the message for the cat
if [ $answer = 'food' ]
then printf "There's meat in the fridge, dumbass!\n"
fi 
```

Enter fullscreen mode Exit fullscreen mode

很好，但是你的小猫咪可能不知道他想要什么。所以让我们添加另一个文字命令，向 **else** 命令问好！如果他的答案不是食物，我们就忽略它~~可怜的猫~~给他拥抱的选择。

```
#/bin/bash

printf "What do you want, fella?\n"
read answer

#if the test is true, then it'll be printed the message for the cat
if [ $answer = "food" ]
then printf "There's meat in the fridge, dumbass!\n"
else
        #now if his last answer was anything but food, you'll make this question 
        printf "Want some cuddle, so? [yes/no]\n"
        read answer
        #if his answer is yes again, you tell him you are busy,
        #else, you tell him to top annoying!
        if [ $answer = "yes" ]
        then printf "Sorry, I'm busy learning shell scripting.\n"
        else printf "So stop annoying me!\n"

        fi 
fi 
```

Enter fullscreen mode Exit fullscreen mode

*   提示:学习如何使用 **if** 命令实际上是直观的。无聊的~~有趣的部分是完全理解**测试**命令。点击[这里](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)，你可以更深入地了解 bash 表达式以及何时应该使用它们。~~

现在这个越来越大了！一旦猫在第一个问题中的答案不是食物，我们就给他另一个问题~~请停止~~
多两个选项！请注意，只有当第一个答案**不是**食物时，才会提出这个问题。我们脚本的可能输出如下所示。

```
What do you want, fella?
food
There's meat in the fridge, dumbass! 
```

Enter fullscreen mode Exit fullscreen mode

```
What do you want, fella?
idk
Want some cuddle, so? [yes/no]
yes
Sorry, I'm busy learning shell scripting. 
```

Enter fullscreen mode Exit fullscreen mode

```
What do you want, fella?
communism
Want some cuddle, so? [yes/no]
no
So stop annoying me! 
```

Enter fullscreen mode Exit fullscreen mode

## 关于

你可以在[我的 github](https://github.com/azbardini/shellscript) 里找到我所有的脚本！

更多文章关注我！**