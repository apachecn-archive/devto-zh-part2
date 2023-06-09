# 如何更改所有提交的作者

> 原文：<https://dev.to/chrisvasqm/how-to-change-the-author-of-all-your-commits-2j7a>

正如一位智者曾经说过的:

> *“能力越大，责任越大”*——本杰明·帕克，蜘蛛侠漫画中的角色。

在处理 Git 时，尤其是在重写提交历史时，这并不陌生。

任何时候当你遇到一个`--force`在寻找如何用 Git 做某事的答案时，你都应该*真的*小心，并且在将改变推给`master`或任何其他正在与其他人合作的分支之前阅读关于它的所有细节。

但是这一次我有一个特殊的需求，我一直在做一个已经提交了大约 10 次的副业项目，当我检查我用`git log`命令做了什么的时候，我很快意识到作者有相同的用户名但是不同的电子邮件。我有来自工作和私人邮件的提交。

我不认为这将导致任何真正的问题，因为我只是做了一个原型，但我也注意到，由于我的电子邮件问题，我的个人资料的贡献图没有显示任何关于这些提交的信息。

这让我想到了最合理的解决方案，我说:

> “哦，糟了！”。

接着在谷歌上搜索我们如何才能做到这一点，这让我找到了这个 StackOverflow 页面，上面有一个由 T4·塔兰迪普·辛格的 T2 评论，这正是我所需要的。

也就是:

```
git filter-branch -f --env-filter "GIT_AUTHOR_NAME='yourname'; GIT_AUTHOR_EMAIL='youremail@example.com'; GIT_COMMITTER_NAME='yourname'; GIT_COMMITTER_EMAIL='youremail@example.com';" HEAD; 
```

Enter fullscreen mode Exit fullscreen mode

<center>**Note: make sure to change the placeholders to what you need them to be.**</center>

在使用`git log`快速检查之后，我必须运行:

```
git push --force origin master 
```

Enter fullscreen mode Exit fullscreen mode

这将确保重写我的所有遥控器的`master`分支提交，以显示我的个人电子邮件和用户名。

**记住，使用`git push --force`时你应该非常小心，我这样做是因为我知道我是那个项目中唯一的一个人，但是在其他情况下使用这个命令可能会导致其他问题。**

希望这能帮助其他处于这种情况的人:)

## 另类

感谢 [Rob](https://dev.to/hoelzro) 在评论中分享了这个选择！

[![hoelzro profile image](img/f4ae6137251e6d7a910f7d69ba5bf4a4.png) ](/hoelzro) [ Rob Hoelz ](/hoelzro) • [<time datetime="2018-09-28T20:38:46Z"> Sep 28 '18 </time>](https://dev.to/hoelzro/comment/5obp) 

很棒的提示！或者，如果你不喜欢用`git-filter-branch`重写一个项目的所有历史，Git 有一个名为 [mailmaps](https://git-scm.com/docs/git-shortlog#_mapping_authors) 的特性，允许你规范化用户名和电子邮件地址。