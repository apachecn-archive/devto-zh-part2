# 通过分析 git 提交来衡量团队绩效

> 原文：<https://dev.to/k4ml/measuring-team-performance-by-analyzing-git-commits-24jb>

我在搜索“peter druecker 软件团队”时，无意中看到了来自 [GitPrime](https://www.gitprime.com/product/) 的这份[白皮书](http://cdn2.hubspot.net/hubfs/2494207/Content/whitepaper/GP-DataDrivenTeams.pdf)。据我所知，他们分析 git 提交来提供一些关于开发人员生产力和性能的见解。

虽然这个想法看起来很棒，但我不认为它对衡量团队表现有多大帮助。举个例子，一个由 3 名开发人员组成的团队，花 3 天时间研究一个问题。他们会就这个问题集思广益，在产品上做测试，写一些脚本来重现这个问题，最后，也许他们中的一个会提交一些代码来修复它。因此，如果你只是将代码作为度量标准来看，这意味着只有一个开发人员是有生产力的，而另外两个开发人员在这 3 天的时间里似乎什么也没做。