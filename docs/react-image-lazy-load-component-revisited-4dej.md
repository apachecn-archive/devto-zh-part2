# 重新访问 React 图像延迟加载组件

> 原文：<https://dev.to/jsguru_io/react-image-lazy-load-component-revisited-4dej>

[![title image](img/8d9b28ac0c0fb8b9086627cd9d4190f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KpRqXosx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3s28bia94sk35q3v5487.png)

很久以前，我创建了一个简单的 React 组件，它可以延迟加载图像，当它忙于加载图像时，它会显示一个占位符，并编写了一个教程来说明我是如何做的。它的灵感来自聚合物的铁的形象和一个整洁的小技巧，如何从占位符过渡到加载图像。

神奇的
在玩聚合物的时候(不要脸的[链接](https://medium.com/jsguru/polymer-tonnes-of-potential-not-quite-there-yet-f7516a65c96d)到我写的另一篇文章)，我学到了你可以制作一个非常酷的“模糊”动画，方法是把你想要展示的图像，缩小到其宽度和高度的 1%，用它作为占位符，当原始大小的图像加载完成后，你在占位符上淡入它。

这产生了一种看起来像模糊图像的效果，暗示着它应该显示的内容突然锐化以显示原始图像。

[![bad@$$ demo gif](img/2b70d3a7434372717ba4cb5f9d0c08a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0nLBPw9e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6fxt34nkz0pc3qd028ky.gif)

太棒了。
反应
呵呵……看到我在那里做了什么吗？标题是反应，我们正在谈论一个反应组件！…很抱歉让你看到了这个。
当我把我的组件放到野外时，我以为很多人不会对它感兴趣，因为它是一个相对基础的组件，但令我惊讶的是，人们对我的组件的兴趣开始越来越大。

感谢 React 社区！感激不尽！我写的那篇文章不断获得浏览量和掌声，根据媒体统计，这种活动主要来自谷歌搜索！

让我非常高兴的是，我找到了一个让 React dev 社区继续感兴趣的话题，并且我提供了一个可能的问题解决方案，同时分享了我的一些知识。

意识到看着发生的一切，我突然明白了一些事情…

虽然这篇文章解释了图像延迟加载背后的概念，以及我从 Polymer 那里学到的巧妙技巧很酷，但它本身却耗费了大量时间。

这就是为什么我决定重做组件，并将其作为库发布在 npm 上！

好东西
如果你在寻找甜甜项目中使用的甜甜组件，那么去甜甜 npm 安装它(甜甜链接

如果你正在寻找 Github 页面，你可以在这里找到它([链接](https://github.com/jsguru-io/react-image-lazy-load)

在你离开之前…
如果你喜欢读这篇文章，请分享它。你应该看看我们的其他出版物，你可能也会喜欢它们！我们不时地写一些关于软件开发、技巧和诀窍，以及如何成为一个更好的开发人员和商业人士的文章。加入我们不断改进的旅程吧！

在[脸书](https://www.facebook.com/jsguruio/)、[推特](https://twitter.com/jsguru_software)、 [LinkedIn](https://www.linkedin.com/company/jsguru) 、 [Medium](https://medium.com/jsguru) 或 [DEV.to](https://dev.to/jsguru_io) 上关注我们。

最初发布于 [jsguru.io](https://jsguru.io/blog/React-image-lazy-load-component-Revisited) 。