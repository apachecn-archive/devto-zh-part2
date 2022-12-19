# 开发到的特殊进料

> 原文：<https://dev.to/anonjr/special-feed-for-devto-59i8>

早在 2017 年 1 月，我就开始关注 [Dev.to](https://dev.to/) 的一个看起来很小、很有前途的程序员社区(以及相邻领域的程序员)。当我把这个网站的所有信息都输入到我的 Feedly 列表中时，我已经评论了一些帖子，我开始考虑要么振兴这个博客，要么选择其他平台来发布。

最后，我决定试着振兴这个网站，从几个项目背后的技术开始。

我当时考虑的事情之一，也是在[Projects Projects](https://www.anonjr.com/2018/11/Projects-Projects-Projects.html)中提到的事情，是为我想在 Dev.To 上提交的文章设置一个自定义 RSS 提要。我计划在这里发布各种各样的文章，我知道不是所有的文章都适合那里的社区，所以我想确保我筛选掉了其余的。我试着在我的个人资料上运行我的常规 RSS 订阅，但它在我的仪表板上的*草稿*中留下了很多垃圾，即使我删除了草稿，它们也会在下次刷新时再次出现。

这就把我们带到了这一点——我现在有了第二个 RSS feed，里面只有我想发送到那里的文章。

在前面的问题中我有一个`repost`变量是这样的:

<figure>[![(side note: need to fix the highlighting/rendering of markdown code in posts...)](../Images/d986c6d875d2057dc3edb2fed6d6739c.png "(side note: need to fix the highlighting/rendering of markdown code in posts...)")](https://res.cloudinary.com/practicaldev/image/fetch/s--F_tJsfnz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.anonjr.com/img/2018-11-04-frontmatter.png) 

<figcaption>
(旁注:需要修复帖子中 markdown 代码的高亮/渲染...)
</figcaption>

T8】</figure>

最初，我打算设置一个“to-dev.to”标签，这样做，但是对于访问我的站点的人来说，这似乎没有用。

现在有一个`todev.to.xml`过滤器过滤我想重新发布的帖子。

<figure>[![](../Images/ef928c9920bec9b2c3c538d859853c53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5k_c4uAs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.anonjr.com/img/2018-11-04-devtoxml.png) 

<figcaption>
</figcaption>

</figure>

在我的下一个机会，我将尝试通过[压缩布局](https://github.com/penibelst/jekyll-compress-html)泵送它，看看是否可以在不破坏任何东西的情况下缩小尺寸。尽管现在我想了想，我不得不要么修改它以与[椒盐卷饼](https://github.com/Code52/pretzel)一起工作，要么首先进行椒盐卷饼到哲基尔的转换。

所以有一个简短的版本。我肯定有更有效的方法来做到这一点，但我可能不会太担心它，直到我转换到 Jekyll。在那之前，我不打算增加任何功能。

(热该死！这使得我的“[几乎 NaNoWriMo](https://www.anonjr.com/2018/11/Almost-NaNoWriMo.html) ”上的 4 对 4！)