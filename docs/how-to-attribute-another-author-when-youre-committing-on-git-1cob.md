# 当你在 git 上提交时，如何确定另一个作者的属性？

> 原文：<https://dev.to/ztickm/how-to-attribute-another-author-when-youre-committing-on-git-1cob>

# 为什么？

您可能会发现自己处于这样一种情况，即您必须提交一些由其他人创作的代码。他们可能无法访问回购协议，或者他们可能习惯了 Bazaar/SVN/Mercurial，但不习惯 git，或者他们可能只是在休假 3 年。
如果你使用默认的[全局或者项目配置](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)提交，git 会显示你是作者。
你不希望这种情况发生，原因有二:
1-你可能会把别人的工作归功于自己，这是不道德的。你可能会因为他们糟糕的代码而受到指责，这很糟糕。

# 如何？

对于归属于另一个人的新提交，使用以下命令:

```
git commit --author="Ms. Jane Codeldoe <Jane@Codeldoe.com>" -m "Jane wrote this I'm just committing" 
```

在 GitHub 上看起来怎么样:
[![Screenshot showing that a commit on Github](../Images/93eab28d7f4f13924de51584183a5a6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6l1MXKhz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dop2i6rf2ikrmoh8nuod.png)

对于更老的提交，这有点复杂，你要避免这种情况。但是，还是有解决办法的，你只需要做一些传统的谷歌搜索(或者暴饮暴食，或者躲躲藏藏，任何适合你的方式)。
根据提交是否被推送到远程，解决方案可能会有所不同。
解决方案也因你使用的 git 托管服务而异，我见过 GitHub 特定的解决方案在其他地方可能行不通。

***P.S***
*如果你被这种问题困扰，或者你认为我可能能够帮助解决的任何其他问题，请通过`msalim [at] boun.cr`联系我。我很乐意用我微薄的专业知识来帮忙。*