# cronjobs 中的 ps 辅助输出

> 原文：<https://dev.to/coolgoose/ps-aux-output-in-cronjobs-80>

你好，

在检查现有进程的过程中，我遇到了一个奇怪的情况，当从终端运行时，命令本身工作正常，但当 cronjob 运行它时却失败了。

该命令大致如下:

```
ps aux | grep 'php artisan a_specific_command --with-some-long-parameters-that were generated' | grep -v grep 
```

Enter fullscreen mode Exit fullscreen mode

我完全忘记的是，默认情况下，`ps`的输出受到`$COLUMNS`数量的限制。作为一个标准，它被设置为 80 个字符([，当然你可以很容易地改变](https://askubuntu.com/questions/219547/how-do-i-get-more-than-80-columns-in-command-line-mode))。

因此，当运行 cron 命令时，输出行本身被截断了(在我的大约有 171 列的全高清屏幕上运行良好，您可以使用`echo $COLUMNS`进行测试)。

解决这个问题最简单的方法是使用 [*宽输出*模式](http://man7.org/linux/man-pages/man1/ps.1.html)两次，以获得无限的宽度

```
ps auxww | grep 'php artisan a_specific_command --with-some-long-parameters-that were generated' | grep -v grep 
```

Enter fullscreen mode Exit fullscreen mode