# 如何重写 git 网址克隆更快推送更安全

> 原文：<https://dev.to/jweslley/how-to-rewrite-git-urls-to-clone-faster-and-push-safer-5apf>

每当您想要克隆一个 git 存储库时，您可以选择以下两个选项之一:

1.  在浏览器中访问存储库页面，复制存储库 url，返回到终端，键入`git clone`并粘贴存储库 url。
2.  手动键入整个存储库 url。

当浏览/探索我在那里找到的开源项目时，我通常使用第一种方法。但是对于个人/工作项目来说，这两种选择都是浪费时间。我认为对于几乎所有的个人/工作存储库来说，项目名称是存储库 URL 中唯一改变的部分。因此，我们可以利用一个鲜为人知的 git 功能:URL 重写。

通过 URL 重写，您可以对存储库 URL 使用不同的格式。例如，代替使用这个:

```
git clone git@github.com:jweslley/dotfiles 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用:

```
git clone gh:dotfiles 
```

Enter fullscreen mode Exit fullscreen mode

要启用这个 URL 重写，您需要编辑您的全局 git 配置文件(`~/.gitconfig`)并添加这些行:

```
[url "git@github.com:jweslley/"]
  insteadOf = gh: 
```

Enter fullscreen mode Exit fullscreen mode

您也可以添加多个 URL 重写。比如我用一个给 GitHub，一个给 GitLab。更多信息，请查看我的 [~/。gitconfig](https://github.com/jweslley/dotfiles/blob/e716c0122af2d6277ffaf3bde365838b61a5ea92/.gitconfig#L8-L12) 。

### 总是使用 SSH 推送认证

此外，您还可以为推送重写 URL。当您希望保证使用 SSH 验证进行推送时，这很有用。为此，您可以在`~/.gitconfig`中创建一个配置部分，比如:

```
[url "git@github.com:"]
  pushInsteadOf = https://github.com/
  pushInsteadOf = git://github.com/ 
```

Enter fullscreen mode Exit fullscreen mode

这确保了推送将总是使用 SSH 认证，即使远程 URL 指定了`https://`或`git://`。例如，`https://github.com/jweslley/dotfiles.git`这样的 URL 会被重写为`git@github.com:jweslley/dotfiles`用于推送，但 pulls 仍然会使用原来的 URL。

有关更多详细信息，请访问官方文档:

*   [git-clone](https://git-scm.com/docs/git-clone)
*   [转推](https://git-scm.com/docs/git-push)转推![](img/b32695789fdfa70d8538bcd739346ac3.png)

原贴[此处](http://jonhnnyweslley.net/blog/how-to-rewrite-git-urls-to-clone-faster-and-push-safer/)。