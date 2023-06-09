# 如何生存到 Android 版 Chrome 禁用 2G 或更慢连接的 JavaScript？

> 原文：<https://dev.to/mangel0111/how-to-survive-to-chrome-for-android-disabling-javascript-for-2g-or-slower-connections--1jj2>

我发现了这篇文章，仔细阅读后，我开始思考这个问题，我将在这里与你分享[链接。](https://www.xda-developers.com/google-chrome-android-disable-javascript-2g-connections/)

基本上说的是，谷歌正在考虑在他的浏览器中禁用 JavaScript，以应对 Android 中缓慢的互联网连接，也许将来会在他的所有版本中禁用。

看起来像谷歌的一个奇怪的举动，如果这成为现实，JS 世界将如何生存？

今天，我们不能谈论前端或 Web 开发而不谈论 JavaScript。现在我们有非常流行的框架和库，比如 React、Angular、Vue 等。而且几乎每个月(或者每周)都会出一个新的 JS 框架？).

我在想如何处理这个新的场景，也许使用一些渐进的增强，在渲染任何东西之前创建幻影布局，并意识到现在我们不仅要支持旧的浏览器，还要支持慢速网络。

当网络变慢时，禁用 JS 的想法让我害怕的是，我们必须支持哪些新的奇怪行为和错误？如果现在我们应该处理浏览器将击落你的应用程序，我们会有这种状态(onJsDisabled？)?新的网络中立条例将如何影响我们网站的速度？

也许我有点偏执，但我想知道社区对此有何看法？这种可能的变化会如何影响我们？又该如何应对？

最诚挚的问候。忧心忡忡的戴夫。