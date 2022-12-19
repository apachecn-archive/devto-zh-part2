# 在你的终端中使用的三个技巧和命令。

> 原文：<https://dev.to/antonrich/three-hacks-and-commands-to-use-in-your-terminal-2d43>

### 第一个是:

du -sh */ |排序-右侧| head -6

该命令将显示 6 个最重的文件夹。

du 磁盘使用情况

| -烟斗。其工作原理类似于[仙丹](https://elixirschool.com/en/lessons/basics/pipe-operator/)和[榆树](https://elmprogramming.com/function.html#forward-function-application)中的烟斗。

基本上，第一个函数的输出成为下一个函数的输入，依此类推。

head -仅显示一定量的输出。

### 第二个:

转动这个>

[![A terminal with a long line before the dollar sign](../Images/e0c54bb52ba4b87900f527462c07e463.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xpag0FzQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/37kqeccxn8y1q36ku4p1.png)

到此>

[![a terminal with just a dollar sign](../Images/4f5b177ea87568d74a1730ea5029e7d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o7SDGUOu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/98gyh8ofeek36n4wke7h.png)

把这个加到你的~/里就行了。bashrc:

PS1='\$ '

之后，您需要重启 bash:

来源~/。bashrc

同样，有不同的方法来重启你的 bash。

有更多的方法可以设置它，查看这个[溢出](https://askubuntu.com/questions/145618/how-can-i-shorten-my-command-line-bash-prompt)以了解更多。

### 第三个:

使用模式创建文件夹。最近，我不得不为我的 codewars 挑战创建 8 个文件夹:

mkdir kyu_{1..8}

不幸的是，我不记得在哪里找到了这个命令。

哦，我刚刚找到它。这里是。