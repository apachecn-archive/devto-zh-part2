# belly:改善 Git 的命令行用户体验

> 原文：<https://dev.to/kahlil/belly-improving-gits-command-line-user-experience-akj>

我更喜欢在命令行上使用 Git。我有一组 Git 命令序列，我一直都在使用它们，我已经记住了它们，但是每次都要打出来，这很麻烦；还有一组命令序列，我经常需要它们，但是必须一直查找。为了改善我个人的 Git 用户体验，我创建了*。*

 *belly 是一个 cli 工具，为一些常见的 Git 命令序列提供了更好的用户体验:

## 提交&推送

当我在 GitHub Flow 中处理一个个人项目或者一个特性分支时，我总是这样做:

```
git commit -m "my commit message"
git push 
```

Enter fullscreen mode Exit fullscreen mode

对于我一天要做几十次的事情来说，打字量太大了。

belly 将这些合并成一个命令:

```
belly c my commit message 
```

Enter fullscreen mode Exit fullscreen mode

*提示:不用输入`belly`，你可以直接输入`b`。*

## Git 结帐分支或创建并结帐

我一直为特性、错误修复和热修复创建分支。我也经常要在分支机构之间切换。

belly 将切换、创建和切换组合成一个命令。所以与其:

```
git checkout my-branch 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
git checkout -b my-branch 
```

Enter fullscreen mode Exit fullscreen mode

我能做的只是:

```
belly s my-branch 
```

Enter fullscreen mode Exit fullscreen mode

如果 belly 存在，它将切换到`my-branch`，如果它还不存在，它将创建并切换到`my-branch`。

## 本地和远程设置和删除标签

当您设置版本标记时，您通常希望在本地和远程存储库中设置它。belly 允许您使用:

```
belly t v1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

肚皮也可以帮你删除那些标签:

```
belly t v1.0.0 -d 
```

Enter fullscreen mode Exit fullscreen mode

## 本地和远程重命名分支

这是我必须查找的 Git 命令之一。如何再次重命名分支，为什么在服务器上重命名它的命令如此不同？

无论如何，没有必要再为这个而使用谷歌了。只是:

```
belly n my-new-branch-name 
```

Enter fullscreen mode Exit fullscreen mode

它将为您重命名本地分支以及相应的远程分支。

## 容易压扁

有几种方法可以挤压您的特性分支提交。如果您想将所有内容压缩到 master 并添加提交消息，您可以使用 belly 的`q`命令:

```
belly q my commit message 
```

Enter fullscreen mode Exit fullscreen mode

## 力-推动正确的方式

当你为了保持你的 Git 历史清晰而使用 rebasing 和 squash 时，你必须定期强制推进你的特性分支。不要使用`--force-push`,建议使用`--force-push-with-lease`,这是一个笨拙命名的标志，如果远程分支已经被其他人更新，它不允许你强制推送。这应该是默认设置。

有了肚子就可以用:

```
belly p 
```

Enter fullscreen mode Exit fullscreen mode

强制推送租赁您当前的分支状态。

如果你认为这些方便的方法中的任何一个对你有用，请随意[获得腹部](https://github.com/kahlil/belly)。如果之前在某期杂志中讨论过，欢迎发表评论。

随着时间的推移，我将改进 belly，并添加更多功能，如果我认为这将是有用的。*