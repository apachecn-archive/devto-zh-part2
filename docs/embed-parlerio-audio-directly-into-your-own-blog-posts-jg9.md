# 将 parler.io 音频直接嵌入到您自己的博客文章中

> 原文：<https://dev.to/kylegalbraith/embed-parlerio-audio-directly-into-your-own-blog-posts-jg9>

你可能已经看到我最近在 dev.to 上的一些帖子在顶部包含了一个嵌入式音频播放器。这是我[在开发回购](https://github.com/thepracticaldev/dev.to/pull/828)中贡献的一个新的液体标签。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ forem ](https://github.com/forem) / [ forem](https://github.com/forem/forem)

### 为社区赋权🌱

<article class="markdown-body entry-content container-lg" itemprop="text">

# Forem <g-emoji class="g-emoji" alias="seedling" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f331.png">🌱</g-emoji>

**For Empowering Community**

[![ruby version](img/aaf756b59de4ea4c46da5dee9a410afa.png)](https://www.ruby-lang.org/en/)[![rails version](img/731972711baa670d316b0f4907534c07.png)](http://rubyonrails.org/)[![Travis Status for forem/forem](img/0205420a37df99bb306f4d97f7092012.png)](https://travis-ci.com/forem/forem)[![Code Climate maintainability](img/5561c43af60e22ffdd92568ec2e089a1.png)](https://codeclimate.com/github/forem/forem/maintainability)[![Code Climate technical debt](img/155d31d7d22d7f1bb337536ea5a7b645.png)](https://codeclimate.com/github/forem/forem/trends/technical_debt)[![CodeTriage badge](img/9f8f7d8f3aa46c9508230cd4e192ce14.png)](https://www.codetriage.com/forem/forem)[![Dependabot Badge](img/ceaafd8fbfca88745417252ad2c74450.png)](https://camo.githubusercontent.com/03860bd4c939150eeab6549d39d93ec07c4f4efd39c731484527b753fc72ab28/68747470733a2f2f62616467656e2e6e65742f646570656e6461626f742f666f72656d2f666f72656d3f69636f6e3d646570656e6461626f74)[![GitPod badge](img/e87aed75ceeae18e025435d5084e1209.png)](https://gitpod.io/from-referrer/)[![Netlify badge](img/e73223f9d7b5b58f5df7493b0887eda0.png)](https://app.netlify.com/sites/docsdevto/deploys)[![GitHub commit activity](img/c636d7221527a9f58a1bcf7eb4f234ec.png)](https://camo.githubusercontent.com/64b9f7c7c5f41ec22113b61235256435cd61779a0554b0595b88b6011f94c60b/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f636f6d6d69742d61637469766974792f772f666f72656d2f666f72656d)[![GitHub issues ready for dev](img/a2f086092c44aa16ddbf13a9cf99fea1.png)](https://github.com/forem/forem/issues?q=is%3Aissue+is%3Aopen+label%3A%22ready+for+dev%22)[![Honeybadger badge](img/3d57ff0653509b0092ec0e6674a3abfa.png)](https://app.honeybadger.io/project/Pl5JzZB5ax)[![Knapsack Pro Parallel CI builds for dev.to](img/78a30552f148d10404f265b48df96a2b.png)](https://knapsackpro.com/dashboard/organizations/1142/projects/1022/test_suites/1434/builds)

欢迎来到 [Forem](https://forem.com) 代码库，这个平台为[开发到](https://dev.to)提供动力。我们很高兴你能来。在你们的帮助下，我们可以增强 Forem 的可用性、可伸缩性和稳定性，从而更好地为我们的社区服务。

## 什么是 Forem？

Forem 是用于构建社区的开源软件。为你的同行、客户、粉丝、家人、朋友以及任何其他需要聚集在一起成为集体一员的时间和空间的社区[参见我们的公告帖子](https://dev.to/devteam/for-empowering-community-2k6h)以获得 Forem 是什么的高级概述。

[dev.to](https://dev.to) (或者只是 dev)由 Forem 托管。这是一个软件开发人员的社区，他们撰写文章，参与讨论，并建立自己的专业档案。我们重视支持性和建设性的对话，为所有成员追求伟大的准则和职业发展。该生态系统涵盖从初学者到高级开发人员，欢迎所有人找到自己的位置…

</article>

[View on GitHub](https://github.com/forem/forem)

这个标签允许你将你从 [parler.io](https://www.parler.io/) 转换的音频嵌入到你的帖子中，这是一个文本到语音转换服务，我已经在这里直接构建了。

Parler 是我在业余时间积极从事的一个兼职项目。目标是将书面内容转换成音频格式，然后可以在其他平台上发布、嵌入和共享。未来的版本将致力于通过观察你的博客来自动进行转换。

既然你已经知道它是什么，以及它是如何帮助你的，让我们来看看你是如何把 parler audio 放到你自己的博客文章中的。

### 如何将 parler 音频添加到您自己的开发博客文章中

1.  从 dev.to 或您自己的博客获取 RSS 源。你的 dev.to feed 可以在这里找到:`https://dev.to/feed/<your-username>`。
2.  将进给输入到 [parler.io](https://www.parler.io/)
3.  在您的博客文章上单击转换。这可能需要几分钟的处理时间。
4.  一旦你的博客文章被转换成音频，在页面底部找到音频的网址。

[![parler.io audio download link](img/bff7fac818d0f24b743662b8b5c6ee3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yr0_stqY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qcddscjrtrkd7w8o4cb0.png)

一旦你的帖子被 Parler 转换，你就可以使用下面的 Liquid 标签将它嵌入到 dev.to。

`{% parler https://www.parler.io/audio/73240183203/ab59b90c45cd1f2144291f1a1cfd69894f87aabf.e466ec75-50e8-47c9-b376-55122f2e3a08.mp3 %}`

保存你的文章，以音频和书面形式分享你的精彩内容。

[![parler audio player](img/4a307ac584ec0736f83eb1aa295a1d58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WJ33HWYf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zt57dlk5jhv4e3pmbd94.png)

值得注意的是，你也可以将转换后的音频嵌入到其他网站。这可以通过从你的转换屏幕复制`<iframe>`代码并粘贴到你想要的站点来完成。

### parler.io 的下一步是什么？

现在 Parler 正在发布，我正在收集像你们一样的用户的反馈。目的是扩展它的更多功能，如批量转换，可选择的声音，以及在 MVP 和其他方面的增强嵌入播放器。

如果您有任何问题或反馈，请随时给我留言。在 [parler.io](https://www.parler.io) 登录页面上还有一个邮件列表，您可以订阅该列表，以便及时了解最新进展。