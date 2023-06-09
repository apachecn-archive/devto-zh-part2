# 文档的静态站点生成器

> 原文：<https://dev.to/kahlil/static-site-generators-for-documentation-47id>

我们在工作中使用了一些没有很好记录的内部库。因此，参与这个项目非常困难，而且非常耗时。我清楚地认识到，对于一个团队成员不断变化的团队来说，没有好的文档会带来很高的成本。

因此，我开始记录我们的应用程序的内部工作方式，把它写在纸上确实令人满意。

带着文档，我参加了我们当地的 JavaScript 聚会 [KarlsruheJS](http://karlsruhejs.org/) ，这也是我参与组织的。有趣的是，它特别报道了一个关于 [VuePress](https://vuepress.vuejs.org/) 的话题，这是一个基于 VueJS 的静态站点生成器，专门用于创建文档。我立刻被迷住了！

[在发了关于我对 VuePress KarlsruheJS 成员 Carsten](https://twitter.com/kahliltweets/status/1057937401191059456) [的兴奋](https://twitter.com/Jupiterrrr/status/1058267113138409472)的推文后，将我的注意力引向了脸书的 Docusaurus，它(显然)是一个基于 React 的静态文档站点生成器。由于[我的同事艾玛完全迷上了盖茨比](https://twitter.com/EmmaWedekind/status/1051767104108732416)，我也仔细观察了那里，也发现了一个盖茨比主题的文档。

我的文档还没有准备好被托管，但是知道有一些可靠的方法可以快速建立文档站点是很好的。因为我们的项目是用 React 编写的，所以我不会使用基于 Vue 的解决方案。不是多库阿龙就是盖茨比。