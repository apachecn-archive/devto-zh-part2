# 生产中的 perl 6:MyJudo.net

> 原文：<https://dev.to/lancew/perl6-in-production-myjudonet-2eng>

最近网上有一个帖子问谁在生产中使用新的 Perl6 编程语言；所以我想分享一些想法，因为我确实在一个现场制作的网站上使用了 perl 6:[MyJudo.net](http://myjudo.net?utm_source=rss&utm_medium=rss)。

该网站是奥运会柔道项目的在线训练追踪网站。把它想象成柔道的 Strava 或 FitBit。

我，Perl 之外，是柔道教练；所以我开始了这个项目，为了我自己的愿望，记录下我在教学过程中所教授的内容。我创建这个网站是为了更深入地学习 Perl6，而不是我以前做过的“Hello World”类型的实验。我并不认为自己是 Perl6 开发人员，我认为我写 Perl6 时带有强烈的 Perl5 口音。这意味着我倾向于用编写 Perl5 的方式编写它，偶尔会使用更多的 Perl6 语法。

总的来说，这是一种可行的发展方式；只有偶尔 Perl6 真的反对我的风格。这样很灵活。我不得不说，了解 Perl5 很可能是学习 Perl6 的障碍，而不是优势。尽管它们是相关的语言，但 Perl6 不是 Perl5 的升级版；这是一种新的语言。

Perl6 是多面兽；它结合了长开发周期中的编程风格和特性；这让它变得既神奇又难学。

MyJudo 构建在用于 Perl6 编程语言的 Bailador web 框架之上。Bailador 是一个基于路由的框架；类似于 Perl5 中的 Dancer。它工作得很好，我没有抱怨；可能除了像 Perl6 这样的年轻语言中的许多东西一样，有一些缺失的部分。使用 Perl6 有时会令人沮丧。也就是说，其中一个好处是社区很小，通常你最终会直接接触到关键的开发者。当然，它是新的，有时站在前沿也很好！

我发现 Perl6 和 Bailador 作为一个从事“业余项目”的开发人员很容易提高工作效率。这很简单，当我出错时，错误信息很有帮助，而且通常很清楚。

modules.perl6.org的配套模块……有限。根本没有 Perl5 的 CPAN 或 NodeJS 的 NPM 的深度。

我在保持柔道，慢慢地变得有特色。我承认我也可能用 Elixir 重新实现它，因为我想学习那种语言。我的直觉告诉我，编写 Perl6/Bailador 比编写 NodeJS/Express 更容易。Perl5/Dancer2 对我来说肯定是最快的；但是我想探索新的事物，我的 Judo 已经并正在允许我用 Perl6 来做这件事。