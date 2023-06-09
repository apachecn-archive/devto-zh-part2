# 分两步找回丢失的 Git

> 原文：<https://dev.to/meduzen/recover-a-lost-git-stash-in-two-steps-569>

有时候你最终会[隐藏](https://css-irl.info/how-git-stash-can-help-you-juggle-multiple-branches) [代码](https://www.git-scm.com/docs/git-stash)，然后在某个时候这些隐藏的代码会被清除。而有一天，你可能会遇到我这个星期面临的情况:

> 好吧，这段代码被合并了，所以让我们删除相关的代码。搞定了。还有…嘿…这部分功能不是应该在代码库中吗？我刚刚删除的信息会永远消失吗？

幸运的是，我设法找回了丢失的东西。我不是 Git 专家，但下面是我经过各种阅读(包括[一些](https://stackoverflow.com/questions/89332/how-to-recover-a-dropped-stash-in-git) [栈溢出](https://stackoverflow.com/questions/32517870/how-to-undo-git-stash-clear) [答案](https://stackoverflow.com/questions/20537223/when-should-i-use-git-stash))后，对我起作用的。

这是两步恢复程序。

## 1。列出丢失的物品

让我们为一个项目运行这个命令，在这个项目中所有的仓库都被清空:

```
git fsck --unreachable | grep commit | cut -d ' ' -f3 | xargs git log --merges --no-walk 
```

Enter fullscreen mode Exit fullscreen mode

它返回一个按日期排序的遗失物品列表。
[![Ho, 3 lost stashes!](img/ced0cf64b567a562f1f5dc493d00d02d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--4JIMymbF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e696wbixz6qvz2r3bc4w.png)

*   *要退出隐藏列表，按下 [Q 键](https://unsplash.com/search/photos/cookies)。*
*   *使用`up`和`down`箭头在一个长长的隐藏列表中导航。*
*   对于 Windows 用户来说，也许 [johnwait 的评论](#comment-node-358103)会在战斗中帮到你。

## 2。把丢失的东西送回原处

让我们使用第二个存储的提交散列:

```
git update-ref refs/stash 4b3fc45c94caadcc87d783064624585c194f4be8 -m "My recovered stash" 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！你会像往常一样找到你的收藏，使用`git stash list`或者通过查看你最喜欢的[Git 客户端](http://gitup.co/)。

## 明白了

### 1。我还是看不到我找回的东西

使用`--create-reflog`参数重试(感谢 [studoggithub](https://dev.to/studoggithub/comment/d54a) ):

```
git update-ref refs/stash 4b3fc45c94caadcc87d783064624585c194f4be8 --create-reflog -m "My recovered stash" 
```

Enter fullscreen mode Exit fullscreen mode

### 2。我的 Git 不是英语

如果你的 Git 不是英文的，每次你想恢复一组 stashes 的时候，你都必须运行`alias git='LANG=en_GB git'`(感谢 [mathieuschopfer](https://dev.to/mathieuschopfer/comment/egd0) )。

## 一些建议

### 提交消息正常

**总是使用提交消息**使用`git stash save -m "My commit message"`:没有消息，唯一有助于识别存储的信息是它的时间戳和保存它的分支，与强显式名称相比，这可能还不够。

提交消息还有助于 Git 客户端:

*   我使用的 Git 客户端 GitUp 在显示未命名的隐藏时完全失败。这可能就是为什么你不能在 GitUp 中创建一个没有名字的存储库，这很好！
*   众所周知的 [SourceTree](https://www.sourcetreeapp.com/) 成功地显示了未命名的藏身地，但是你可以猜到，这个列表浏览起来并不友好:![Unnamed stashes in SourceTree](img/71aa1f177e5245b6707f01c4f1350eb8.png)

### 是的，`git stash apply` > `git stash pop`

与`git stash pop`， **`git stash apply`** 不同的是，`git stash apply` 并不从藏物列表中移除藏物，可以避免一些损失。

### 树枝>藏东西

最后，我建议避免`git stash`。相反，尝试使用分支。这似乎是显而易见的，但它只是在我找到一个恢复贮藏的方法时才出现的:也许我应该用临时树枝代替贮藏。在工作中使用 [Git Flow](http://nvie.com/posts/a-successful-git-branching-model/) 方法，在遭遇痛苦的经历之前，我就会想到这一点。

如果你有任何*隐藏*的提示或经验想要分享，欢迎评论。