# Rust 代替 Ruby:案例研究

> 原文：<https://dev.to/rhymes/rust-instead-of-ruby-a-case-study-2gpp>

通过[Highscalability.com](http://highscalability.com/)我看到了一个名为[“Tilde 的竞争优势是什么样的”](https://www.rust-lang.org/pdfs/Rust-Tilde-Whitepaper.pdf)的案例研究，其中解释了该公司如何节省时间和金钱(客户和他们自己的)并为他们的服务创建了一个非常强大的监控代理。他们显然还能够“再培训”他们的员工，而无需在培训或处理不必要的复杂性上花费过多的时间。

第一个版本用的是 Ruby，内存占用有时超过 100MB，不够健壮，给客户带来了问题。

新版本处于 Rust 中，其内存占用约为 8MB，客户更高兴，他们能够在占用相同内存和稳定性的情况下提供更多功能。

[![memory occupation](../Images/93b7fb6d1d76aca0262b988b79a148cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sv2yZRRw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kboa7n8pch2jjrp10akd.png)

(摘自案例研究)

我觉得这非常了不起。

由于两种语言都不流利，我很想知道他们是否考虑过围棋，如果是，为什么他们放弃了它。

加分:案例研究中引用了我们自己的[@ vaidehijoshi](https://dev.to/vaidehijoshi):-)

可以在这里找到:[《蒂尔德的竞争优势是如何生锈的》](https://www.rust-lang.org/pdfs/Rust-Tilde-Whitepaper.pdf)