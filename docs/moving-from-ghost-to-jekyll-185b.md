# 从《幽灵》到《哲基尔》

> 原文：<https://dev.to/nervewax/moving-from-ghost-to-jekyll-185b>

我时常喜欢重做我的网站，同时尝试一些新的东西。过去我用过 Tumblr、WordPress 和 Ghost，但这次我尝试了 Jekyll。

[![Jekyll](img/86c9a095d9000628e34be0d3d2f29ff9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NbZaxvJy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nervewax.com/conteimg/2018/03/jekyll.png)

Jekyll 是一个相当成熟的静态站点生成器，学习新的东西真的很有趣。我也非常喜欢这个过程是如何简洁明了地围绕它来构建我的网站。我是从看[这个视频](https://www.youtube.com/watch?v=iWowJBRMtpc)开始的，它很好地介绍了哲基尔的基础知识。

## 转换我的内容

从幽灵到哲基尔的转变出奇的容易。我使用一个名为 [jekyll_post_importer](https://github.com/eloyesp/jekyll_ghost_importer) 的程序将我的内容从 ghost 备份文件转换到 jekyll 的 markdown 文件。

之后，需要做一些手工工作来确保所有的图片和标签都工作正常。幸运的是，我没有写那么多实际内容！

## 我的标签页在哪里？？

[![Where are my dragons?!](img/4cbd84d99e78f6309c91c5196be2e817.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EdTPBV-u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://nervewax.com/conteimg/2018/03/wamd.gif)

所以从 Ghost 和 Wordpress 开始，我希望 Jekyll 有某种标记结构，它确实有……但是 Jekyll 分类法并不像你所期望的那样有自己的页面。

为了以与传统博客 CMS 相同的方式生成标签存档页面，需要一些额外的东西。

在搜索的时候，我找到了这个博客，虽然我不太能找到它，但是它让我确信这是可能的。然后我检查了[这个 Jekyll 插件](https://github.com/pattex/jekyll-tagging)，只做了一点小小的调整就解决了这个问题。🙌

所以[正如你所看到的](https://nervewax.com/tag/project/)，我为我的每个标签都准备了一个页面，甚至成功地使用了一点古老的[插值](https://stackoverflow.com/a/41721856/2351788)来获取最近帖子的图片。

## 相关内容

因为我所有的内容都有标签，所以我想如果有相关的帖子的话，我会觉得很酷。几乎不用搜索，我就找到了这个聪明的片段，我最终修改了它供自己使用。

## 原来如此！

它不是适合所有人的 CMS，但是到目前为止，我很喜欢它，它确实能完成工作。满足我的开发人员对高性能和精益代码库的需求的加分。

在 Atom 中直接写这个也很好，现在要找出最好的方法来让它运行起来👋