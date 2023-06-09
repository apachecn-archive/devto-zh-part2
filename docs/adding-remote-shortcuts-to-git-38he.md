# 向 Git 添加远程快捷方式

> 原文：<https://dev.to/hoelzro/adding-remote-shortcuts-to-git-38he>

*最初发布于[https://hoelz.ro/blog/adding-remote-shortcuts-to-git](https://hoelz.ro/blog/adding-remote-shortcuts-to-git)T3】*

如果您像我一样，很可能是一个 Git 用户，经常使用少量主机作为存储库。我在这里使用的例子是 [GitHub](https://github.com) 。

要克隆另一个用户的存储库，您最终需要键入如下内容:

```
 $ git clone https://github.com/miyagawa/cpanminus.git 
```

Enter fullscreen mode Exit fullscreen mode

如果你想克隆你的一个仓库，你会得到这样的结果:

```
 $ git clone git@github.com:hoelzro/linotify.git 
```

Enter fullscreen mode Exit fullscreen mode

现在，这不是太多的打字，但是肯定有更短的方法！如果我能把这个打出来不是很好吗？

```
 $ git clone github:miyagawa/cpanminus 
```

Enter fullscreen mode Exit fullscreen mode

还是这个？

```
 $ git clone hoelzro:linotify 
```

Enter fullscreen mode Exit fullscreen mode

事实证明只要对你的。gitconfig 你可以！

您可以在 gitconfig 中添加一个 URL 部分，用 insteadOf 属性描述您想要使用的前缀。下面是前面两个例子在我的。gitconfig:

```
[url "git@github.com:hoelzro/"]
    insteadOf = hoelzro:
[url "https://github.com/"]
    insteadOf = github: 
```

Enter fullscreen mode Exit fullscreen mode

冲洗和重复你的各种来源！