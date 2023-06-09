# 对同一个 Git 远程使用两种不同的协议

> 原文：<https://dev.to/hoelzro/-using-two-different-protocols-for-the-same-git-remote-4733>

*最初发布于[https://hoelz . ro/blog/using-two-different-protocols-for-the-same-git-remote](https://hoelz.ro/blog/using-two-different-protocols-for-the-same-git-remote)T3】*

如果你阅读了我在[上发表的关于为 Git](https://hoelz.ro/blog/adding-remote-shortcuts-to-git) 添加远程快捷方式的文章，你可能会发现它对于指定快捷方式以方便克隆很有用。如果你还没有读过，总结是，要在 GitHub 上克隆我的 linotify 库，我只需要键入以下内容:

```
 $ git clone hoelzro:linotify 
```

Enter fullscreen mode Exit fullscreen mode

这是因为我设置了一个将`hoelzro:`翻译成`git@github.com/hoelzro/`的别名。这种方式非常有效，但有时对于只读流量来说，使用 SSH 之外的协议可能更好。也许你在一台新机器上，你忘记了将它的公钥与远程端关联起来(使用 GitHub 这很容易，但是如果你在一个只使用基于公钥的认证的 web 服务器上托管你的代码呢？).也许您已经将`ssh-agent`配置为一段时间后身份超时，并且您不想仅仅为了运行`git fetch`而键入您的密码。我不知道有多少次我输入了`git fetch`,却被提示输入我的密钥密码。好烦啊！如果我能告诉 Git 使用 HTTPS 协议从存储库中读取，使用 SSH 进行写入，这不是很好吗？

在稍微钻研了一下`git-config`的联机帮助页之后，我发现了另一个类似于`insteadOf`的宝石:`pushInsteadOf`。所以我修改了我的`gitconfig`来使用它(这说明了`insteadOf`配置选项的另一个优点:我在我的`gitconfig`中更改了它一次，并且这些更改应用于我机器上使用该快捷方式的每个库。如果有一天我决定将我所有的存储库从 GitHub 转移到一个私有服务器上，我不需要在每个存储库中设置新的远程设备。):

```
[url "git@github.com:hoelzro/"]
  pushInsteadOf = hoelzro:
[url "https://github.com/hoelzro/"]
  insteadOf = hoelzro: 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我在我的存储库上运行`git fetch`或`git pull`时，我从来没有被提示输入我的 SSH 密钥的密码，因为从我的存储库
读取是通过 HTTPS 协议完成的。