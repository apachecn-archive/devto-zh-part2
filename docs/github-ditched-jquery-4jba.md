# GitHub 抛弃了 jQuery

> 原文：<https://dev.to/rhymes/github-ditched-jquery-4jba>

在前面关于 JavaScript 和渐进式改进的讨论之后

[![ben](../Images/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 浏览器还应该允许用户禁用 JavaScript 吗？

### Ben Hal pern Aug 30 ' 181min read

#healthydebate #webdev #javascript #browsers](/ben/should-browsers-still-allow-users-to-disable-javascript--211d)

我发现阅读 GitHub 最近成功完成的从 jQuery 的迁移令人耳目一新:[从 GitHub.com 前端移除 jQuery](https://githubengineering.com/removing-jquery-from-github-frontend/)。

出于显而易见的原因，他们使用了现代浏览器和(大量)polyfills。好像他们根本没用过巴别塔。

这篇文章的两段给我留下了最深的印象:

> 作为我们在 GitHub.com 上构建前端功能的改进方法的一部分，我们专注于尽可能摆脱常规的 HTML 基础，只添加 JavaScript 行为作为渐进的增强。因此，即使那些使用 JS 增强的 web 表单和其他 UI 元素通常也可以在浏览器中禁用 JavaScript 的情况下工作。在某些情况下，我们能够完全删除某些遗留行为，而不是用普通的 JS 重写它们。
> 
> 我们追求不断改进的总体理念也延伸到了定制元素。这意味着我们在标记中保留尽可能多的内容，并且只在其上添加行为。例如，默认情况下显示原始时间戳，并进行升级以将时间转换为本地时区，而当嵌套在元素中时，即使没有 JavaScript 也是交互式的，但通过可访问性增强进行了升级。

渐进增强是一个至少有十年历史的概念，我很高兴越来越多的人记住了它。

另外，我刚刚注意到 GitHub 主页上的提要根本没有显示禁用 JavaScript 的:D