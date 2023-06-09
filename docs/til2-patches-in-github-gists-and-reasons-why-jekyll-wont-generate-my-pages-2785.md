# TIL # 2:GitHub gist 中的补丁和 jekyll 不生成我的页面的原因

> 原文：<https://dev.to/pxlpnk/til2-patches-in-github-gists-and-reasons-why-jekyll-wont-generate-my-pages-2785>

## GitHub gists 中 Patchfiles 语法高亮显示

今天有人给我发了 gist，里面有一个补丁文件。令我高兴的是，我注意到他们很好地突出了这些变化。这极大地提高了补丁的可读性。查看这个补丁文件:[https://gist . github . com/pxl pnk/357 a 32 e 789 b 63 D5 d 809 a 40d 886 BC 9337](https://gist.github.com/pxlpnk/357a32e789b63d5d809a40d886bc9337)

文件名以`.patch`结尾很重要。

## 化身和未来的日期

今天早上，我花了大约 15 分钟试图找出为什么一个使用 [jekyll](https://jekyllrb.com/) 的帖子不会出现。一些愤怒的谷歌搜索导致了一个*案例。这很有道理，但是为什么呢？*

事实证明，jekyll 不会生成任何日期设置为未来时间的东西。对我来说，日期定在今天，但一小时后。

以下是我了解到的为什么它可能不会产生帖子的原因:

*   帖子没有放在 _posts 目录中。
*   这篇文章的标题不正确。帖子应该命名为年月日标题。标记(注意标记扩展，通常是。md 或者。降价)
*   帖子的日期是未来。您可以通过在 _config.yml (documentation)中设置 future: true 来使帖子可见
*   邮报发表了:假在其前面的事情。将其设置为 true。

非常感谢 StackOverflow 和他们的用户再次帮助我:[https://stack overflow . com/questions/30625044/Jekyll-post-not-generated](https://stackoverflow.com/questions/30625044/jekyll-post-not-generated)