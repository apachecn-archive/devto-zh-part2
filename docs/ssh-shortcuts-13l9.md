# SSH 快捷方式

> 原文：<https://dev.to/hoelzro/ssh-shortcuts-13l9>

*最初发布于[hoelz . ro](https://hoelz.ro/blog/ssh-shortcuts)T3】*

假设你有一个经常登录的服务器。我们还假设该服务器上的`sshd`运行在一个非标准端口上，以避免烦人的扫描器(在本例中我们将使用 53718)。要 SSH 到该服务器，您可以运行以下命令:

```
 $ ssh -p 53718 rob@example-server.com 
```

多拗口啊！一定有办法避免打那么多字！我经常看到人们使用的一种技术是为此创建一个 shell 别名:

```
 $ alias example='ssh -p 53718 rob@example-server.com' # in bash 
```

所以现在我要做的就是运行`example`。小菜一碟，对吧？

但是如果你用的是`scp`呢？还是`sftp`？还是`rsync`？还是`git remote add`？还是 Vim 的 netrw 插件？突然你简单的外壳别名看起来不那么酷了！

不过，这个是有解决办法的！输入您的 SSH 配置文件。

位于`~/.ssh/config`的 SSH 配置文件可以做很多*很酷的事情*。为了复制上面的例子，我们将把它添加到我们的配置中:

```
Host example
  HostName example-server.com
  User rob
  Port 53718 
```

现在我需要做的就是`ssh example`，瞧！有用！它比别名长四个字符，但该快捷键也适用于以下命令:

```
 scp example:that-important-file.txt .
  sftp example
  rsync -ar example:my-project/ .
  git remote add example ssh://example/~/my-project
  vim scp://example/that-important-file.txt 
```

如果你有兴趣进一步简化 Git 的例子，请看我的博客文章。

你的 SSH 配置文件非常强大；我建议查看`man ssh_config`以获得更多选项！