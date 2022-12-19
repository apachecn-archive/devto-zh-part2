# 你的 Git repo 是不是一塌糊涂？我们的也是

> 原文：<https://dev.to/soluto/is-your-git-repo-a-hot-mess-ours-was-too-15li>

<figure>[![](../Images/084fe179aba8fbed913fd7e76c947d0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xb_MhTgy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjArs7qA1LiBmF0MQYf-FVw.jpeg) 

<figcaption>到处转贴</figcaption>

</figure>

还记得那次你想在你的公寓开一个小型聚会吗？你邀请了几个朋友，买了几杯饮料，放了一些好听的音乐……然后你的朋友鲍勃邀请了他的室友，你的朋友爱丽丝邀请了她的同事，突然之间，派对就全面展开了。那是一片混乱。

现在想想你的 Git 回购。类似于聚会，只有几个开发者在做同一个回购的时候，一切都井井有条。但是当一群开发人员开始出现时，事情就有点失控了。

在 Soluto，我们有两个主要的回购协议，全球每天都有 100 多名开发人员提交。当我们刚开始的时候，每个人都可以将新代码推送到一个主分支，这很快就变得一团糟。然后我们开始需要一个 PR (pull request)来推送新代码。它在一段时间内运行良好，但是不清楚谁应该审查每部分代码的 PR，并且一些代码块被完全忽略了。

### 代号车主来救援

Github [去年引入了一个叫做 code owners 的新功能。代码所有者允许您定义哪个团队或开发人员是每段代码的所有者。它非常易于使用，并且引入了一种在大型回购中审查变更的有效方法。](https://blog.github.com/2017-07-06-introducing-code-owners/)

#### 怎么做到的

你所要做的就是定义一个 *CODEOWNERS* 文件，它使用的模式与*的规则相同。gitignore* 文件。然后，在每个 PR 中，代码变更部分的所有者需要批准 PR，以便将其合并到主分支。如果你想了解更多关于代码所有者的信息，点击[这里](https://help.github.com/articles/about-codeowners/)。

现在，您已经有了一些组织好的代码和经过良好审查的 PRs。您和其他开发人员更致力于代码，更好地了解它，并在将新代码推向生产时更有信心。

### 更上一层楼

代码所有者改变了我们的工作方式，我们喜欢这样。但是，我们经常注意到，在大型 PR 中——有许多更改的文件——有多个所有者被要求审查 PR。对于所有者来说，跟踪他们必须查看的文件很快变得乏味。

[![](../Images/d56b33aba089fbdcc329e329d94a2c8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TyTp5Fgo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/310/0%2ARfwjKC4TtgPpYxEq.gif)

#### 介绍代码所有者 Chrome 扩展

我们开发了一个简单而有用的 Chrome 扩展，允许代码所有者点击一个按钮，立即只看到他拥有的文件。你可能会问，幕后发生了什么？首先，它定位 *CODEOWNERS* 文件，然后识别出你是其所有者并且需要审查的文件，过滤掉剩余的更改文件。多酷啊。

[![Code owners chrome extension](../Images/63a3413a433e6a4ffc1527331a136580.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ti3zD1XA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/997/0%2AAOCvvR06zK5dtOWN.jpg)

因此，由于 PRs 的存在，现在代码更加有组织和清晰。由于代码所有者特性，开发人员对代码有了更好的了解。审查者很乐意用我们令人敬畏的扩展以更有效的方式审查代码。😉

如果你想试用我们的 chrome 扩展，可以从这里下载。

请随意在我们的 [Github](https://github.com/code-owners/codeowners-ext) 上添加任何问题或 PRs 这将有助于我们改进扩展。

*原载于* [*阳光工程博客*](https://blog.solutotlv.com/is-your-git-repo-a-hot-mess-ours-was-too/) *。*

* * *