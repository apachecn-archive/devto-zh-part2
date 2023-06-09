# 介绍 SHanC——静态黑客新闻克隆

> 原文：<https://dev.to/dance2die/introducing-shanc--a-static-hacker-news-clone-3ldj>

我每天都看[黑客新闻](https://news.ycombinator.com/) (HN，以下简称。

我的阅读方式是，我 ***快速浏览*** 标题，打开 5~10 个故事，实际阅读 2~5 个。

我和 HN 有两个问题。

1.  每个 HN 页面只显示 30 个故事
2.  不得不点击“更多”来查看帖子似乎太痛苦了

我不需要故事的实时更新。

所以我创建了 [SHaNc](http://shanc.netlify.com/) ，一个静态页面黑客新闻克隆。

在这篇文章中，我将只向你介绍 SHaNc。关于它是如何建造的细节和建造时面临的问题将在下一篇文章中讨论。

### ❓为什么？

它解决了上面提到的两个问题。

1.  一次浏览 30 页会分散注意力
2.  用回发加载下一页很慢

SHaNc 将所有的故事加载到一个页面中(解决了第一个问题),因为它是一个静态页面

*,所以速度很快，同时还可以学习 [GatsbyJS](https://www.gatsbyjs.org/) & [样式组件](https://www.styled-components.com/)。*

### 🤷什么？

SHaNc 是一个静态页面，它显示所有的故事(计划的最佳和新故事页面)，这解决了两个问题-加载所有的故事和快速(作为一个静态页面)。

根据定义，静态页面是不会改变的。因此，页面每小时都会重新构建一次。

*所以你可能最多会落后 1 个小时。*

### 🤔其他静态 HN 克隆体怎么样？😕

还有其他两个可怕的静态 HN 克隆。

1.  [Nuxt 黑客新闻](https://hn.nuxtjs.org/news)
2.  [黑客下一个](https://next-news.now.sh/)

它们很快，但我仍然有一个问题，必须手动加载下一页，这会分散你的注意力。

### 🏭技术栈&架构

没什么特别的。

1.  [GatsbyJS](https://www.gatsbyjs.org/)–使用 [ReactJS](https://reactjs.org/) 的静态现场发电机
2.  [样式组件](https://www.styled-components.com/)–JS 中的 CSS
3.  [Netlify](https://www.netlify.com/)–部署&构建 WebHook
4.  [Azure 功能](https://azure.microsoft.com/en-us/services/functions/)–每小时通过 Build WebHok 触发站点构建

SHaNc 建筑(手绘在一张纸上😝)

[![architecture](img/9ff785345af4e7bcd64f366c1e9be991.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jIO1_O98--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.slightedgecoder.com/wp-content/uploads/2018/05/architecture.jpg%3Fresize%3D1024%252C653)

### 🏃‍♂️临别赠言

如果你有类似 HN 的阅读方式，我希望 SHaNc 能够帮助解决你的问题。

请在 [GitHub 页面](https://github.com/dance2die/SHANc/issues)上留下反馈、问题和建议(麻省理工学院许可)。

* * *

*介绍静态黑客新闻克隆人【SHanC 的帖子[最早出现在](https://www.slightedgecoder.com/2018/05/26/introducing-shanc-a-static-hacker-news-clone/)[微边缘编码器](https://www.slightedgecoder.com)T5 上*