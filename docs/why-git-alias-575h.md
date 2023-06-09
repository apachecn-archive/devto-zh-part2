# 为什么是 Git 别名

> 原文：<https://dev.to/devcamilla/why-git-alias-575h>

我使用 git 别名已经有一段时间了。人们看到我使用它们，问我，我解释，鼓励他们也使用它，但失败了。为什么？

也许我的说服力不是那么有效，所以我会尝试再上诉一次。

## Git Alias

Git 别名是 git 命令的简写。把它加到你的`~/.gitconfig`里就行了。

```
[alias]
    st = status
    ch = checkout
    chb = checkout -b 
```

Enter fullscreen mode Exit fullscreen mode

现在，不用输入`checkout -b`，只需输入`chb`即可。

```
git checkout -b bug12345
git chb bug12345 
```

Enter fullscreen mode Exit fullscreen mode

很酷，对吧？那么，还有什么？

### 为什么 Git 别名？

#### 真酷！

是的，我又说了一遍。但这真的很酷吗？能够一次完成多个命令。当其他人一个接一个地执行 git 命令时，您走捷径，使用别名。做一些不同于传统的事情。不得不承认，有时使用 git 别名感觉很糟糕(*尤其是结对编程的时候*

[![alt text](img/333de370cff211fa654a58a76e00475f.png)T2】](https://i.giphy.com/media/MM0Jrc8BHKx3y/giphy.gif)

#### 节省时间

我想我不需要再强调这个了。只需点击几下鼠标，您就可以准备、提交并推送您的更改。考虑到你一天大概要犯 10 次错误，这将为你节省大约 10 秒钟，每天至少可以节省 2 分钟。你可以用它来煮咖啡，去休息室，更新你的代码审查程序，或者只是舒展一下筋骨，小睡一会儿。

[![alt text](img/020fa7ef43015fc8f5aa97c90812cfe7.png)T2】](https://i.giphy.com/media/l6gZ4YYesTutW/giphy.gif)

#### 我的饭桶别名

这里有一些我经常使用的别名。全部，我在这里分享。

```
[alias]
    #current branch
        me = !git rev-parse --abbrev-ref HEAD
    #publish
        up = !git push origin -u $(git me)
    #unpublish
        down = !git push origin --delete $(git me)
    #stage all then commit with message
        acm = !git add . && echo 'Staged all changes, if any.' && git commit -m $message 
```

Enter fullscreen mode Exit fullscreen mode

我最近在这里发现了`fixup` git 别名。它允许您修改特定提交的分段更改。

```
fixup = "!f() { TARGET=$(git rev-parse "$1"); git commit --fixup=$TARGET ${@:2} && EDITOR=true git rebase -i --autostash --autosquash $TARGET^; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

要使用，只需提供几个您想要修改的提交的 SHA-1 散列。

```
git fixup d670460b 
```

Enter fullscreen mode Exit fullscreen mode

然后瞧！您最近的阶段性更改应该被修复到该提交。

你可以用别名做很多事情。首先，在解决 rebase 冲突时，我还使用 git alias 打开源文件上的 *TortoiseGit* 并执行 *diff* 。这取决于我们去发现这些可能性。

### 为什么不是 Git 别名

x:那么发布 branch 的 git 命令又是什么呢？
我:`git up`
X:不是饭桶命令
我:(*记得是化名*)哦，等等。让我查一下。

好吧，因为你使用别名，忘记原来的命令是正常的。这是无法逃避的。但别担心，我会支持你的。这也有一个 git 别名。

```
# list aliases
    la = !git config -l | grep alias 
```

Enter fullscreen mode Exit fullscreen mode

使用`git la`列出您所有的别名和相应的命令。使用 git 别名破解的好方法。

## 结论

我希望我对 Git Alias 的介绍足以让您尝试一下。只是想分享一下使用它的极乐。希望您在日常编程体验中也能发现它的用处。

对于其他使用它的人，介意分享你最喜欢的别名吗？你最喜欢它的什么？

对于其他没有的人，为什么不呢？