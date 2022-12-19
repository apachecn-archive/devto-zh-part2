# 新手 Shell 脚本编写 01

> 原文：<https://dev.to/bardiniaz/shell-scripting-for-newbies-4642>

让我们假设您想要检查您的任何 linux 系统的统计数据，您可能会使用诸如 **w** 、 **date** 或 **df** 这样的命令。这意味着您需要编写三个命令，并逐个检查它们的结果...不是最搞笑的爱好！

这就是 shell 脚本的由来:让任务自动化，等等...

### 你好火星

首先，让我们创建一个 **hellomars.sh** 文件。通过在你的终端中输入
来做到这点

```
touch hellomars.sh 
```

Enter fullscreen mode Exit fullscreen mode

然后，使用 ~~vim~~ 你最好的编辑器编辑这个文件，并有一些~~缺乏~~的乐趣来创建你的第一个 Hello Mars 脚本！

```
#/bin/bash
echo "Hello Mars!" 
```

Enter fullscreen mode Exit fullscreen mode

创建后，通过键入:
授予该文件执行权限并执行文件本身

```
chmod +x hellomars.sh
./hellomars.sh 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到了， **echo** 是写一些东西作为输出的命令。 *#/bin/bash* 用来告诉 linux 必须使用哪个解释器(有很多， *sh* ， *bash* ， *dash* ...).该脚本的输出可能如下:

```
Hello Mars! 
```

Enter fullscreen mode Exit fullscreen mode

### 一个接一个

现在你知道如何在屏幕上写东西了，让我们写一些相关的东西。把所有的第一个命令放在一起，你可以创建另一个文件，我们称之为 *mysystem.sh* :

```
#/bin/bash
echo "Aliens using this computer:"
w
echo "What year am I?"
date
echo "Is there any computer left?"
df -h 
```

Enter fullscreen mode Exit fullscreen mode

*   提示:-h 旗转 *df* 多一点*人可读*。

通过运行这个脚本，输出必须如下所示:

```
Aliens using this computer:
 22:13:13 up  1:54,  1 user,  load average: 0,55, 0,56, 0,60
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
bardini  tty7     :0               20:22    1:54m  2:28   0.60s /sbin/upstart --user
What year am I?
Ter Jul 17 22:13:13 -03 2018
Is there any computer left?
Filesystem      Size  Used Avail Use% Mounted on
udev            2,9G     0  2,9G   0% /dev
tmpfs           586M   17M  570M   3% /run
/dev/sda1       105G   33G   67G  33% /
tmpfs           2,9G   31M  2,9G   2% /dev/shm
tmpfs           5,0M  4,0K  5,0M   1% /run/lock 
```

Enter fullscreen mode Exit fullscreen mode

人们可能不理解你在脚本中使用的函数，所以用标签来评论它

```
#/bin/bash
#This shows users in this computer
echo "Aliens using this computer:"
w

#This shows the date and time of this computer
echo "What year am I?"
date

#This shows the disk usage of the computer
echo "Is there any computer left?"
df -h 
```

Enter fullscreen mode Exit fullscreen mode

## 关于

你可以在[我的 github](https://github.com/azbardini/shellscript) 里找到我所有的脚本！

更多文章关注我！