# RIP jQuery:现代浏览器的替代方案

> 原文：<https://dev.to/anthonydelgado/a-jquery-alternative-for-modern-browsers-3idj>

#### RIP jQuery

两年前，在第一次[代码新手](https://twitter.com/CodeNewbies)见面会上，我有机会见到了 [jQuery](https://jquery.com/) 、 [John Resig](https://twitter.com/jeresig) 的创始人。当时，我真的开始加倍研究我的全部栈 Javascript / MongoDB，React，Node journey，它们来自 PHP/MYSQL/jQuery 世界。就在那天晚上，jQuery 的创建者走上舞台，告诉我们他不再使用他创建的库，而是只关注 React，这时我告诉自己是时候加倍研究现代 Javascript 库了。

## 听着，我爱 jQuery。

jQuery 很棒，它让我可以创建令人惊叹的前端项目，赢得黑客马拉松，随心所欲地操纵 dom。但是有时候你会发现，在一个人的生命中，是时候去追求更大更好的东西了。所以我一头扎进了 React 和 Node 的世界，没有回头。jQuery 在我心中永远有一个特殊的位置。

但是 jQuery 是一个充斥着猎豹和美洲豹的 javascript 世界中的老狗。它是在 IE6 是最新的浏览器，IE7 是每个人都在等待的升级版本的时候被创造出来的。它是在谷歌 Chrome 出现之前发布的，Adobe Flash 是人们创建交互式网站的主要方式。jQuery 在当时是革命性的，但它从诞生到现在已经有 10 多年了。

这就引出了一个问题....

## 如果 jQuery 是今天写的呢？

满足现金。今天，我在查看我一个朋友的源代码和一些代码时，发现了一个有趣的 Javascript 库，叫做 Cash。Cash 是一个为现代浏览器(Chrome、Firefox、Safari 和 Internet Explorer 9+)构建的超小型库，提供 jQuery 风格的语法来操作 DOM。利用现代浏览器特性来最小化代码库，开发人员可以使用熟悉的 chainable $方法。虽然 Cash 不能保证 100%复制传统 jQuery 的特性，但它确实很接近，涵盖了大多数日常用例，如添加/删除/切换类和解析 html 字符串。

在这里了解更多关于这个项目的信息:[https://www.npmjs.com/package/cash-dom](https://www.npmjs.com/package/cash-dom)

## 美元符号是时候回归了吗？

像 React 和 Angular 这样的工具对于现代的 web 应用程序来说是很棒的，但是对于简单的登录页面来说却感觉有些过头了。

请在下面的评论区告诉我你的想法。