# 那个饭桶救了我的命

> 原文：<https://dev.to/nodunayo/the-day-that-git-saved-my-life-17pi>

好吧，所以它没能救我一命。但是它为我节省了几个小时的时间，并防止我经历难以置信的挫折。

我一直在做一个大的新功能，一个棘手的问题。经过几个小时扎实的工作，我完成了。我运行 git status，坐下来自豪地看着红色列表，上面详细列出了我修改或创建的大约十个文件。

我仔细检查并添加了我所有的修改。然后，我用一条简单的描述性消息提交了我的更改。我已经准备好了:

[![](img/be526d68615fd62f5d9adf2a7a60f7f6.png)T2】](https://cdn-images-1.medium.com/max/1000/0*ZW3ipN58OKzZmBkn)

然后我意识到我犯了一个错误。我在研究师父。

你看，我没有开发过我自己的代码库。我一直在为一个朋友做一些东西，我想让他们在我把它们放入官方网站之前回顾一下所有的改动。我决定我应该将我所有的工作提交到一个分支上，并打开一个拉请求。

现在，最安全的做法是——相信我，我有这个想法，所以我不知道为什么我决定不这样做——将[樱桃选择](https://git-scm.com/docs/git-cherry-pick)提交到一个新的分支上，然后将 master 重置为其原点。然而，出于某种*奇怪的*原因，我支持我的 Git 能力，并决定从*的内存*中做一些不同的事情，以便将代码库恢复到提交前的阶段。

### 让我们一起重温灾难

这里是一个重新制定。当我意识到我犯了一个错误的时候，我们会重新开始:

[![](img/b6d50e02735f5fdbebe7cf72b25a9df5.png)T2】](https://cdn-images-1.medium.com/max/1000/0*7Ohlz-SEIWpOHcE8)

顶部的提交是我需要移动的。

我做的第一件事是键入 git reset——软 HEAD^，这样最后一次提交将会消失，但我仍然可以暂存我的所有工作。git 状态揭示了这一点:

[![](img/0aa36f0348c1d5982ae5622c3cf745cd.png)T2】](https://cdn-images-1.medium.com/max/1000/0*VlNsfGbIL9gp6q1O)

git log 向我展示了这个:

[![](img/93c122c0dec86e656e5b21930b3a22cf.png)T2】](https://cdn-images-1.medium.com/max/1000/0*CXZ4B2zVy8Yt4ABE)

太好了！我有我的工作，但是“新的，大的特性…”的承诺从主人那里消失了。

然后，我检查了一个名为 new-big-feature 的新分支，并运行 git status:

[![](img/e67b822e5e66973858f789b99aeff030.png)T2】](https://cdn-images-1.medium.com/max/1000/0*_Qt9mpWcByzykMK4)

此时，我心想:“新的大功能分支看起来不错。在提交这些更改之前，让我整理一下主分支。”

为什么，娜迪亚，为什么？！)

所以，我再次检查了 master 分支并运行 git reset - hard origin/master。

git 状态显示:

[![](img/98b47dbcc397e2e785628535a4463b99.png)T2】](https://cdn-images-1.medium.com/max/1000/0*ShvwNiUp1S9oEUUT)

回到新的大功能分支。

现在，我期望在运行 git status 时看到这个:

[![](img/b2c1f3ffd5f41e723bc6bbbef3b3ed97.png)T2】](https://cdn-images-1.medium.com/max/1000/0*zzsC5I8CRMrA-MoT)

相反，我看到了这个:

[![](img/23e7201ddda203b1fc298db90cb1ff22.png)T2】](https://cdn-images-1.medium.com/max/864/0*_ow9UBqOMhE165uJ)

他妈的怎么回事？！

不，这不可能！

我开始发抖。我的心怦怦直跳。我没有时间重新做所有这些工作。我怎么会这么粗心呢？

我输入 git status 无数次，每次都说这一定是出错了，git status 只是出错，我所有的工作都会在那里，一切都会很好。

在疯狂地在键盘上敲了五分钟左右后，我停下来，双手抱着头，不知道该做什么，并开始为重新开始这项工作做准备。

### Git 如此强大，大多数人只是略读皮毛

突然，我想起有人对我说过这句话。我也隐约记得有人说过:

“它记录了你输入的每一个 Git 命令，你可以检查本地存储库的不同阶段。”

“好吧，饭桶，”我心想，“让我们看看你有多厉害。”

我去谷歌上输入:“恢复未推送的丢失的提交”，这时我发现了这个奇妙的 git reflog:

> 引用日志或“引用日志”记录了分支和其他引用的提示在本地存储库中更新的时间。Reflogs 在各种 Git 命令中非常有用，可以指定引用的旧值。例如，HEAD@{2}表示“HEAD 以前在两个移动之前的位置”，master @ { one . week . ago }表示“master 以前在这个本地存储库中指向一个星期之前的位置”，等等。

满怀希望，我回到终端，输入 git reflog:

[![](img/0d7f9e35d70ea772b5d57959ccac52ff.png)T2】](https://cdn-images-1.medium.com/max/1000/0*_mDfaU9t224Z4AaL)

最后一行看起来很有趣。在我提交之后，在我进行致命的重置之前，它似乎反映了我的本地存储库中的点。

我深吸一口气，交叉手指，然后输入 git reset - hard HEAD@{6}。

哈利路亚！

git 状态揭示了这一点:

[![](img/020a75004b45a7d1909cdbc58c97a8bc.png)T2】](https://cdn-images-1.medium.com/max/1000/0*-1edpHQzvOUx_tmx)

git log 给了我这个:

[![](img/841532d3b73277d42415648a18881e32.png)T2】](https://cdn-images-1.medium.com/max/1000/0*4wZi1SMGkkZ4WMDY)

犯罪再次出现。我的工作恢复了。我重操旧业了。

我切换回我的新的大功能分支，精选 2d6e22b 提交，然后重置主机。

唷。

### Git 支持你

我希望你永远不会遇到这种情况——不小心丢失了提交，并为丢失了几个小时的工作而恐慌。少而多的承诺和有规律的推动将会把你从上述情景中拯救出来。然而，我希望我的故事给了你信心，当你深陷 Git 的泥沼，不知道如何脱身时，你可以继续前进，尝试一些你不确定是否可行的事情，比如复杂的 rebases 和 merges。毕竟，你现在知道你几乎不可能丢失任何已经提交的工作，即使它从未被推动过。

我还希望这个故事让您对发现更多的 Git 表面积感到好奇。这一经历，再加上前几周我发现的- - [意向广告](https://dev.to/nodunayo/tell-git-your-intentions-414e-temp-slug-8483294) d [选项，让我渴望知道 Git 还能为我做些什么。](https://dev.to/nodunayo/tell-git-your-intentions-414e-temp-slug-8483294)

我已经学到了一些东西——使用 Git，您甚至可以恢复您从未提交的工作！

但那是以后的博客了。