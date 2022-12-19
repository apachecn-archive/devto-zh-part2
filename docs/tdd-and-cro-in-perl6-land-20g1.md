# Perl6 中的 TDD 和 Cro

> 原文：<https://dev.to/lancew/tdd-and-cro-in-perl6-land-20g1>

最近我在写一些 Perl6，把一个 app 从 Bailador 移植到 Cro。如果你一直在看 https://twitch.tv/lancew?utm\_source=rss T2 UTM medium = RSS T1 的直播，我已经做了很多测试。所以今天我把它整理出来了；随着 Cro::HTTP::Test 模块最近发布(v 0.7.5)。

下面是一个非常简单的例子，它测试了主页加载是否正常:

> 使用 Cro::HTTP::Test；
> 
> 使用路线；
> 
> test-service routes()，{
> 
> test get('/')，
> 
> status = > 200，
> 
> content-type =>' text/html '，
> 
> body =>/\/register/；
> 
> }
> 
> 完成-测试；

这非常简单地在“/”应用程序上执行 GET 然后检查状态是 200，内容类型是 HTML，工作“注册”出现在主体中。因此，您可以通过为路线编写一个测试并从那里向前工作，轻松地开始开发一个新的特性/服务。当我在这里工作时，由于考虑到重新设计我的逻辑以简化表示层，我有点跑题了……但是我停止了，因为我在 Perl6 中使用对象的技能不如我所希望的那么好。

这里有一个更有趣的例子，我得到了一个你必须登录才能访问的路径:

> 使用 Cro::HTTP::Test；
> 
> 使用 Cro::HTTP::Auth；
> 
> 使用路线；
> 
> test-service routes()，fake-auth = > usersession . new(:username(' lancew ')，{
> 
> test get('/user/lancew ')，
> 
> status = > 200，
> 
> content-type =>' text/html '，
> 
> body =>/lancew/；
> 
> }
> 
> 完成-测试；

正如你所看到的，这是非常相似的，主要区别是“**fake-auth =>user session . new(:username(' lancew ')，【T1”)，它让控制器有一个 auth 对象来评估和反应。所以这一小段代码足以访问登录的页面，非常好！Perl6(像 Perl5 一样)有很好的测试工具。测试是内置的(不是一个额外的模块),这很好。我承认我需要提高我的技能。**

迄今为止，与 Cro 一起工作真的很愉快，所以我建议尝试一下。它很小，如果您需要构建一个(或两个)后端服务，它似乎是一个不错的工具。我对 Cro::OpenAPI::routes from definition 模块很感兴趣，看起来它在处理 open API 项目时会很有帮助。

如果你尝试过 cro，请告诉我，因为我想知道它对你的效果如何。我还没有深入研究过你可以创建的服务的链接；我想看看用 cro 实现的 real 项目是什么样子的。

Perl6 中仍然令人不满的一个地方是缺少 perlcritic 和 perlty。林挺和格式化也许是一个惊喜。我没有看；但是希望它在路线图上，因为它已经到达跨语言的点，现在 linter 和代码格式化程序已经成为规范而不是例外(我认为)。Perl5 以 Perlcritic 和 PerlTidy 独领风骚；但是现在 JS、PHP、Go 等等都有 Perl6 似乎缺乏的好工具。

如果现在让我来做一个小的后端，Cro 确实是我可能会去做的事情。比贝拉多这种东西更重要。可能比 JS (express)或带有 Mojo 或 Dancer2 的 Perl5 更重要。我想探究并发性和链接细节；敬请关注。