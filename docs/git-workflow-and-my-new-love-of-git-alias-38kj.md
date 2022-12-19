# Git 工作流和我对 Git alias 的新欢

> 原文：<https://dev.to/cseeman/git-workflow-and-my-new-love-of-git-alias-38kj>

不管是好是坏，我当前的 Git 工作流不包括合并，但是包括重新基础。

[![](../Images/8548a7d3fcd5b4a4afd6413618520345.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rtiwNe94--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://christine-seeman.com/wp-content/uploads/2018/07/Screen-Shot-2018-07-24-at-9.04.01-AM-1024x724.png)

它保持了历史的整洁，与已建立的工作流程(相当标准的 GitHub 流程)一起工作，没有合并提交。我试图找到一个主分支的漂亮图形，但从 GitLab 背景来看，在 GitLab repo 中找到这个信息并不困难，但我不认为 GitHub 真的有这个历史特性。有人知道了解 GitHub 历史的最好方法吗？

我们确实强制确保有意义的提交和提交消息。当您在本地分支工作时，可以随意提交，但是在请求您的拉取请求之前，需要一个[交互式 rebase](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History) 。在你做了几个这样的 rebases 之后，真的没有什么好害怕的了。我没有做过，让我告诉你，我的第一个拉请求是非常痛苦的。我认为通过审查过程，清理我的提交历史，就像编写我的特写> _ <一样耗时。现在我，瑞贝卡斯和补偿是朋友。我还没有机会使用[修正提交](https://fle.github.io/git-tip-keep-your-branch-clean-with-fixup-and-autosquash.html)，但是它们已经在我的攻略上，很快就可以试用了！

我喜欢使用 Git bash 来满足我的源代码库需求。即使我的公司有更先进的工作流程，对我来说它也有最大的灵活性。我之前已经尝试过 [Sourcetree](https://confluence.atlassian.com/get-started-with-sourcetree/install-sourcetree-847359094.html) (与 BitBucket 配合使用比 GitHub 更好)和 [Git Kracken](https://www.gitkraken.com/) ，当你需要处理复杂的冲突情况时，有可视化效果是很好的，但我继续回到我的可信 Git Bash。我以前从来不使用命令行，只是在某些情况下，但是现在特别是自从我切换到 Ruby 和 Rails 之后，我在任何时候都至少有 3-4 个终端是打开的。我昨天甚至不用查就退出了 Vim！我觉得这应该是一个里程碑。

[![](../Images/f1857adfee2268a54d61f99c771cd207.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZLZ4lRit--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://christine-seeman.com/wp-content/uploads/2018/07/Da2x8hMX0AY6ne_-300x260.jpg)

此外，我一直在利用 GIT 的 CLI 别名，还安装了多个脚本运行在终端([终端鹦鹉](https://github.com/jmhobbs/terminal-parrot)我在看着你！).其中一些对我的工作帮助更大。如果你没有 git 别名，就给自己弄一个。你只需要给`~/.gitconfig`
添加行

```
[alias]
    st = status
    ci = commit -v 
```

Enter fullscreen mode Exit fullscreen mode

现在我个人最喜欢的是:

ST = status

co = check out

DM = diff–耐心主

prb = pull–rebase

pu = pull–rebase 原点主

pf = push–force-with-lease

po = push 原点主

你的 Git 别名里有什么？还有在 MacOS 上 Command+Option+Shift+V 有多牛逼？这就是我在复制粘贴文本和不保留旧格式时遇到的问题的关键！