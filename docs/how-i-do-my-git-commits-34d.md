# 我的提交消息工作流

> 原文：<https://dev.to/shreyasminocha/how-i-do-my-git-commits-34d>

*本文原载于[http://shreyasminocha.me/blog](http://shreyasminocha.me/blog)T3】*

[![Commit message example](img/4a0fd7bacc50b360c8392384ad9b9772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--msuUwPib--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h5gh9chb9p0plwebemct.jpg)

我完全沉迷于提交消息。我经常花几分钟盯着空间，试图想出最好的方式来总结提交带来的变化。我虔诚地遵循好的提交信息的七条规则。正如您可能已经猜到的，我已经花了一些时间来开发一个用于编写提交消息的工作流。

我使用 Sublime Text 3 来满足我的大部分文本编辑需求。我还把它设为我的核心编辑器。

```
git config --global core.editor "subl -n -w" 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:以上要求你安装`subl`命令，如果它不能开箱即用。安装说明:[MAC OS](////stackoverflow.com/questions/16199581/open-sublime-text-from-terminal-in-macos)T5】LinuxT7】Windows。

早些时候，我曾经使用 [a 包](////packagecontrol.io/packages/Git%20Commit%20Message%20Syntax)为提交消息提供语法高亮显示。然而，build 3170 带来了对各种 git 格式的本地支持，包括提交消息。

Sublime 允许您覆盖特定语法的设置。你可以从‹Preferences› → ‹Settings 编辑这些-语法特定。这是我的`Git Commit.sublime-settings`文件:

```
{  "rulers":  [50,  72],  "spell_check":  true,  "word_wrap":  "true",  "wrap_width":  72,  "font_size":  14,  "draw_centered":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

**注** : VS 码迷可以这样做。

在互联网上的某个地方，我找到了一个提交消息的模板:

```
# If applied, this commit will…

# Explain why this change is being made

# Provide links to any relevant tickets, articles or other resources 
```

Enter fullscreen mode Exit fullscreen mode

这个模板使得根据我前面提到的七条规则来构建提交变得更加容易。我似乎不记得我是在哪里找到这个的，但是在我试图追踪它的时候，我发现 T2 的一篇博客文章提供了一个非常相似的模板。事实证明，git 允许使用文本文件作为提交消息的模板。

```
git config --global commit.template "/Users/example/dotfiles/commit-msg-template" 
```

Enter fullscreen mode Exit fullscreen mode

当我开始使用这个模板的时候，我对我的设置有一点小小的不满。运行`git commit`将会在光标位于第一行时激发 Sublime，而实际上*写*消息时，我将不得不移动光标到下一行。通过研究和一些实验，我解决了这个问题:

```
git config --global core.editor = "sublime -n -w $1:2" 
```

Enter fullscreen mode Exit fullscreen mode

值末尾的`$1:2`打开参数，光标位于第 2 行。

我对提交消息文本的另一个不满意之处是注释掉的`git status` :
上面的这个小片段

```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit. 
```

Enter fullscreen mode Exit fullscreen mode

虽然对新手很有帮助，但这个小通知最终变成了一个烦恼。我在 StackOverflow 上找到了一个[有用的答案，建议使用](////stackoverflow.com/a/3967136)[全局`prepare-commit-msg`钩子](////git-scm.com/docs/githooks#_prepare_commit_msg)。如果您还没有一个全局 git 钩子目录，那么创建一个并告诉 git:

```
git config --global core.hookspath "/Users/example/dotfiles/git-hooks" 
```

Enter fullscreen mode Exit fullscreen mode

在该目录下创建`prepare-commit-msg`，内容如下:

```
#!/usr/bin/env bash

sed -i.bak '/^# Please/,/^#$/ d' $1 && rm $1.bak 
```

Enter fullscreen mode Exit fullscreen mode

编辑:这个代码片段最初使用的是`perl`后跟`grep`。感谢 [@shaiay](https://dev.to/shaiay) 和 [@jwmevans_77](https://dev.to/jwmevans_77) 在评论中的建议。

确保使用`chmod +x`将挂钩设置为可执行。现在讨厌的招聘启事再也不会打扰你了。

[![Relevant xkcd](img/8eac34bf3860aad0da7682337b3ff744.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RFmYXWgV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6q1bi503x31zgfjbrdw0.png)

最近，我发现了[这个非常酷的工具](////github.com/clns/node-commit-msg)，它允许你从命令行验证提交消息。我在一个[全局`commit-msg`钩子](////git-scm.com/docs/githooks#_commit_msg)中使用该实用程序的一个修改形式来自动验证我所做的每一次提交。在你的全局 git 钩子目录中创建`commit-msg`:

```
#!/usr/bin/env bash

commit-msg file $1 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:上面要求`commit-msg`指令在你的`$PATH`上。使用`npm i -g commit-msg`从[源代码](////github.com/clns/node-commit-msg)手动安装。

同样，确保脚本被设置为可执行的。这个挂钩的结果是，如果我的提交消息不符合标准，提交将会中止。

[![An example of an invalid commit message](img/79f55eb314f61f27b070850d675bf9bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gokhNiIR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ecoe98uzm3pyrvko9p1q.jpg)

感谢阅读！

## 进一步阅读

*   [Tim Pope 关于提交消息的注释](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
*   [提交来自 Pro Git 的指南建议](https://www.git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project#_commit_guidelines)
*   [Linus Torvald 关于提交消息的注释](https://github.com/torvalds/subsurface-for-dirk/blob/master/README#L92-L120)
*   [提交 Spring 项目的消息格式指南](https://github.com/spring-projects/spring-framework/blob/30bce7/CONTRIBUTING.md#format-commit-messages)