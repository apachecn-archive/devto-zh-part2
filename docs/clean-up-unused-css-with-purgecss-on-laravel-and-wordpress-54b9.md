# 用 Laravel 和 WordPress 上的 PurgeCSS 清理不用的 CSS

> 原文：<https://dev.to/cristiannebunu/clean-up-unused-css-with-purgecss-on-laravel-and-wordpress-54b9>

我最近读了一篇文章[关于我如何用 PurgeCSS](https://medium.freecodecamp.org/how-i-dropped-250kb-of-dead-css-weight-with-purgecss-28821049fb) 减少了 250KB 的死 CSS 重量，我想我应该为这种情况设置一下。我使用 Laravel Mix 进行 Laravel 开发和我的 WordPress 工作。我的主栈包括 Vue.js 和 TailwindCSS，所以这些代码片段在使用这些框架时会做得很好。除此之外，我不能做出任何承诺。

但是作为对 PurgeCSS 的认可，我要告诉你，在过去我在 WordPress 做的几个项目中，我设法把我的 CSS 文件压缩到 50 kB 左右。

我的 Laravel 配置文件如下所示

[![laravel config](img/5b8fbe42b8b97571e8552385f9e9b51f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iaQerYS2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k2owuc8th6x969lbe79p.png)

我的 WordPress 配置文件看起来像这样

[![wordpress config](img/0650b1604ea3800b3731e5c9f63b5ef5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X-0uex9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/53i67a8oi9q05y6rv0iy.png)

*最初发布于[cristiannebunu.com](http://cristiannebunu.com/clean-up-unused-css-with-purgecss-on-laravel-and-wordpress)2018 年 7 月 25 日*