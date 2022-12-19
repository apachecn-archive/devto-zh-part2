# 异步和延迟，区别。

> 原文：<https://dev.to/thomasbnt/async-and-defer-the-difference-1542>

*   **简介**

我最近遇到了一个 JavaScript 文件的问题,**一加载网页**就会变慢。我明白，等着不太好。这就是我为什么要看 async 和 defer 的历史。
两种不同的加载模式，一种是在 JS 执行过程中加载，另一种是等待 JS 运行结束时加载所有内容。

*   **不同执行模式的说明**

正如前面提到的 **async** 一样，它在 HTML 分析和 JavaScript 执行期间加载。所以他突然开始装货。
在**推迟**的同时，耐心等待 HTML 的页面加载和解析，在最后时刻运行。

**异步**的模式

[![](img/53824dcedecffd144fe528a02202fdc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_dwz7o9R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ybpov5zmmcs71nm9pwt.png)

代码示例:

```
<script type="text/javascript" src="assets/js/init.js" async></script> 
```

Enter fullscreen mode Exit fullscreen mode

**延期**的方案

[![](img/377e359776de754bbf3f968270fecf31.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3FL1M9qZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ni4se2n04s32192ho6z.png)

代码示例:

```
<script type="text/javascript" src="assets/js/init.js" defer></script> 
```

Enter fullscreen mode Exit fullscreen mode

*   **两者都没有，怎么回事？**

所以你不必加载这两种形式，虽然这很有用，但是如果你没有在代码中添加任何可以恢复 JS 资源的东西，它会在加载的同时运行。

[![](img/0fb40decd9db17fed82d2fbb746dfaf1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hEo8gQAR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y4q7gdgjsuko0y217c8c.png)

*   **学分**

    *   `cover_image`用无刷随机背景图像和来自 [Fork Awesome](https://forkawesome.github.io/Fork-Awesome/) 的旋转渲染
    *   帖子[是几个月前在 Busy](https://busy.org/@thomasbnt/async-and-defer-la-difference) 上写的，用法语写的。

| ☕ | 查看我的[推特账号](https://twitter.com/thomasbnt_)。您可以看到许多项目和更新。你也可以[在给我买杯咖啡上支持我，Stripe 或者 GitHub 赞助商](https://thomasbnt.dev/donate)。感谢阅读我的帖子！🤩 |
| --- | --- |