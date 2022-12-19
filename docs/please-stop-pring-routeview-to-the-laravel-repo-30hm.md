# 请停止 PRing 路线::查看 Laravel 回购

> 原文：<https://dev.to/m1guelpf/please-stop-pring-routeview-to-the-laravel-repo-30hm>

自从第一次引入`Route::view()`作为路由闭包的替代，已经有各种各样的 pr(在撰写本文时超过 22 个)到`laravel/laravel`存储库来更新默认的欢迎路由以使用这个新特性。

这个改变被拒绝了(并且一直被拒绝),因为基于闭包的方法更适合于原型开发，正如 [Taylor 在第一个实现这个改变的 PR](https://github.com/laravel/laravel/pull/4310#issuecomment-312509833) 中所说的。

<figure>[![Please stop PRing Route::view to the Laravel repo](../Images/a158c42c41c3d26732fd7955cf93a70b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q_l91jkl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miguelpiedrafita.com/content/images/2018/10/Image-2018-10-15-at-7.45.48-PM.png) 

<figcaption>就这样了伙计们，BDFL 已经发话了，让我们继续前进吧...伙计们。你在听吗？</figcaption>

</figure>

如果你认为再次推动这一变化可能会改变泰勒的想法，那么看看 [#4598](https://github.com/laravel/laravel/pull/4598) 、 [#4569](https://github.com/laravel/laravel/pull/4569) 、 [#4498](https://github.com/laravel/laravel/pull/4498) 、 [#4481](https://github.com/laravel/laravel/pull/4481) 、 [#4443](https://github.com/laravel/laravel/pull/4443) 、 [#4439](https://github.com/laravel/laravel/pull/4439) 、 [#4437](https://github.com/laravel/laravel/pull/4437) 、 [#4424](https://github.com/laravel/laravel/pull/4424) 、  [#4386](https://github.com/laravel/laravel/pull/4386) ， [#4381](https://github.com/laravel/laravel/pull/4381) ， [#4378](https://github.com/laravel/laravel/pull/4378) ， [#4367](https://github.com/laravel/laravel/pull/4367) ， [#4310](https://github.com/laravel/laravel/pull/4310) 或 [#4681](https://github.com/laravel/laravel/pull/4681) 。 所有这些拉请求都引入了相同的更改，并且都被拒绝。

所以，如果有人从你的 PR 链接了这篇文章，帮维护者一个忙，关闭它。请记住，在你做出改变之前，最好先了解一下是否有人提出了你的改变:)

编码快乐！

* * *

*喜欢这篇文章？考虑[帮助我实现我的梦想](https://miguelpiedrafita.com/patreon) :)*