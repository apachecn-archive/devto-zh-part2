# 如何在 shell 中运行命令直到成功

> 原文：<https://dev.to/mayeu/how-to-run-a-command-in-your-shell-until-it-succeed-42dp>

我经常通过`rsync`在互联网上复制大文件，由于我经常旅行，我通常无法获得稳定的互联网连接，所以我最终会多次重新运行该命令，直到它成功。

事实证明有更好的方法。当然，您可以使用`while`并测试命令的返回，但是对于一个*特设的*命令来说，这听起来工作量很大。但是我很懒，我最近发现有一个`until`命令存在！

非常简单易用:

```
$ until <put your command here>; do echo "Retrying at `date -Iminutes`"; done 
```

Enter fullscreen mode Exit fullscreen mode

所以用`rsync`你得到:

```
$ until rsync -aP src:/path/to/src dest/; do echo "Retrying at `date -Iminutes`"; done 
```

Enter fullscreen mode Exit fullscreen mode

而如果你真的很懒，就创建一个别名就行了(留作练习；) ).

*本文最初发表于 [mayeu.me](https://mayeu.me/blog/run-command-until-stops/) 。*