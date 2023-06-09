# 变好

> 原文：<https://dev.to/stuki/gitting-good-5bcj>

[![Not like I actually know how to git](img/bbcc66f13989d41abe1b4235061fcbdd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TaeJl9uf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/03vh1xen1pugi8zbklr7.png)

以下是我在过去一个月左右的工作中需要使用的命令列表。其中大部分是针对更具体的场景，但也有一些只是我不知道的非常方便的选项。

为了获得更好的场景列表和非常有用的提示，我推荐 Seth Robertson 关于主题 <sup id="fnref1">[1](#fn1)</sup> 的页面。

## 反向摘樱桃

当我们接近发布时，我们为我们的产品创建了发布分支，但是意识到有些特性不应该被包含时已经太晚了。所以我们不得不逆向挑选出特性并提交。查阅互联网，我们发现`git revert COMMIT_SHA`是我们一直在寻找的命令。由于提交分散在中，我们无法恢复一系列提交，但是如果您有那么幸运，那么工作流会更加容易。使用`..`范围选择器 <sup id="fnref2">[2](#fn2)</sup> 可以恢复提交范围。

```
git revert -n OLDER_COMMIT^..NEWER_COMMIT 
```

Enter fullscreen mode Exit fullscreen mode

这里的`^`读作“的父代”，所以这将产生命令:从旧提交的父代到新提交。

## 修复以前的提交

我当然不会犯不应该犯的事情，或者犯任何错误，但是我发现写“fix”或者更糟的“asdf2”在第 n 次之后真的变得很乏味，并且在你的非官方编码简历上看起来很糟糕。幸运的是，其他人也会犯错，所以 git 当然有一个`--fixup`提交选项。`git commit --fixup COMMIT_SHA`追加一个“修正！”在你传递的提交信息之前。为了充分利用这一功能，您可以使用`git rebase --interactive --autosquash`和 voilà。就像扫除地毯下的所有污垢，眼不见，心不烦。

如果您将上面的命令与这个命令结合起来，您甚至可以编写`git commit --fixup HEAD`，这甚至更容易，因为您不必搜索提交散列。更酷的是使用了`:/`，通过它你可以基于提交消息 <sup id="fnref3">[3](#fn3)</sup> 。比方说，您有几个提交:

```
054808a Hello there
7434f0b These are
c538016 Some commits 
```

Enter fullscreen mode Exit fullscreen mode

您需要修复第一次提交。您可以只使用`:/`符号，而不是复制 hash 或 HEAD 的父代。

```
git commit --fixup :/commits 
```

Enter fullscreen mode Exit fullscreen mode

然后搜索消息中包含字符串“commits”的第一个提交。这不是很好吗？

## 放弃自上次推送以来的所有工作

修复某个东西也是如此(试图变得快速和肮脏，但是这些词和 git 在大多数时候并不完全匹配),并且正要将修复推送到远程存储库，这时发生了我没有获得最新提交的情况。所以，当然，它自动创建了一个丑陋的合并，否则我可能不会关心，但这是我的职业自豪感。于是我上网搜了一下，发现可以通过`@{u}`(指的是当前分支正在跟踪的上游分支。所以基本上是一个更长的字符串的简写，[更此处](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/gitrevisions.html)作为你最后一次推到原点的参考。所以一个`git reset --hard @{u}`之后，我有了一个干净的石板，可以拉新的提交，应用我的一行修复，并推送到服务器。我发誓没人看见任何东西。

## Gitconfig

好吧，好吧，我应该知道更好，当然有一个。gitconfig 文件，你可以做。有用的东西包括:

```
# so you don't have to write --autosquash every time you want to rebase your fix
[rebase]
  autosquash = true

# saves you writing the branch name when you're pushing to the remote
[push]
  default = current 
```

Enter fullscreen mode Exit fullscreen mode

但是鲍勃是你的叔叔，所以尽情发挥吧，按照你喜欢的方式来配置它。

* * *

所以你有它。Git 同样令人害怕和惊奇，我不认为当我真正需要它们的时候我会记得它们中的任何一个，但是现在*我*知道我把命令放在哪里了，也许你，亲爱的搜索引擎用户对这个有一些用处。正如我在开始时所说的，赛斯·罗伯逊<sup id="fnref1">T31</sup>在我看来像是一个饭桶神(或者是饭桶 Gud)，所以一定要看看他在这个主题上写了什么。

* * *

1.  [http://sethrobertson.github.io/](http://sethrobertson.github.io/)T2】↩

2.  [https://stackoverflow.com/a/24186641](https://stackoverflow.com/a/24186641)T2】↩

3.  [https://robots . thoughtbot . com/auto squasing-git-commits # type-words-not-shas](https://robots.thoughtbot.com/autosquashing-git-commits#type-words-not-shas)T2】↩