# 将长的终端输出发送到日志文件

> 原文：<https://dev.to/flaviabastos/send-long-terminal-output-to-a-log-file-3n8e>

有时命令行输出太长，很难滚动所有行来查看堆栈跟踪的开始。这通常发生在您遇到错误时，修复错误的最佳方式是阅读前几行，以了解您得到了什么错误消息以及错误来自哪里。

前几天，一位同事在我们的 Slack 频道分享了一个关于命令*脚本*的技巧，这是一个在输出超长的情况下使用的伟大工具:它将所有输出保存在一个日志文件中。然后，您可以在文本编辑器中打开它，并正确地分析它。

在命令行中键入:

```
script my\_script\_name.scr 
```

Unix 脚本命令将开始“记录”输出。然后，您可以再次运行给出长输出的命令，脚本命令将保存所有内容，直到您键入

```
exit 
```

然后，您只需在您最喜欢的文本编辑器中打开 my_script_name.scr，以便轻松阅读。

你应该知道的事情:

–它仅适用于 Unix 系统(Mac、Linux)

–如果您忘记给出文件名，它会将输出保存在一个名为ˋtypescriptˋ的文件中

–您也可以传递带有文件名的路径，否则它将保存在您当前的工作目录中

–有关此命令的更多信息，您可以阅读手册页(手册脚本)或查看此链接:[https://www-users . cs . umn . edu/~ Gini/1901-07s/files/script . html](https://www-users.cs.umn.edu/~gini/1901-07s/files/script.html)

一件很酷的事情是，我在同事分享它的同一天开始使用这个工具。我希望这对你也有用。

> 帖子[将长终端输出发送到日志文件](https://wp.me/pa0b0y-t)最初发布在 [flaviabastos.ca](https://flaviabastos.ca) 上

*照片:[马库斯·斯皮斯克在 Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片*