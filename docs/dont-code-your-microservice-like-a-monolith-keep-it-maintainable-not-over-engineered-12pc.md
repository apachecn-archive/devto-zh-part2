# 不要把你的微服务编码成一个整体，保持它的可维护性而不是过度工程化

> 原文：<https://dev.to/geshan/dont-code-your-microservice-like-a-monolith-keep-it-maintainable-not-over-engineered-12pc>

你会用枪打死一只苍蝇吗？答案是否定的。当你想写一个 10K 代码行最多的微服务应用时，情况也是如此。不要过度工程化。

好的，你一直在编写和维护大型代码库。有些可能有 100K 多行代码，但是你需要克服你的习惯，用不同的方式思考。本帖帮助你揭开编写小型可维护微服务的奥秘。这些建议是固执己见的，但在百万美元的业务中却是可行的。

[![Don't code your microservice like a monolith, keep it maintainable not over-engineered](img/066a3c159eebc0e072e06214a8efd750.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z45GxSP---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.img/microservice-code/lego.jpg)

## TLDR；

> 微服务是小型软件系统。你可以安全地抛弃 MVC。对 ORM 说不，也不要带着微服务的设计模式包袱。关注代码的性能、可读性和可维护性，而不是一些旧的规则和模式。那些模式是在人们不做微服务的时候做的。

## 为什么一开始要写微服务？

在我看来，微服务架构是将一个或多个单片系统分解成多个更小的系统。这些是基于业务功能的更易维护、独立开发和部署的软件。这些较小的(大概是“微”)系统应该只专注于一个业务功能，并把它做好。这里的问题是“微观的”，这些部分应该在 10K 代码行之下。

由于它们是独立的，这有助于业务更快地发布特性。

> 发货团队不依赖于结账团队。部署在发货应用程序上的东西永远不会破坏结账。

它变得非常松散。每次变化的爆炸半径是可控的。这就是微服务被迅速采用的原因。

现在让我们看看你习惯的做事方式，以及为什么它在这个微服务时代变得不那么有意义。

## 需要 MVC 吗？

模型-视图-控制器，我是在 2007 年或者更早的时候接触到它的。我曾经认为它是解决所有软件架构问题的灵丹妙药。我不再持有那种观点了。

> 是的，你曾经使用 Java 或 PHP，其他的框架都是基于 MVC 的。现在，你不再需要对 MVC 严格要求了。

专注于清晰和完成任务。

如果你想使用控制器，如果它仍然有意义的话。想象一下，我的应用程序收到 HTTP 请求，它必须返回 HTTP 响应。请考虑拥有一个后端 API 和使用它的前端。检查下面的代码，肯定不是 MVC:

```
async function get(params) {
  const today = new Date().toISOString().split('T')[0];
  const {fromCurrency='AUD', toCurrency='USD', onDate=today} = params;

  let exchangeRates = await db.query(
    `SELECT rate, created_at FROM exchange_rates WHERE from_currency = ? AND to_currency = ? AND on_date = ?`, 
    [fromCurrency, toCurrency, onDate]
  );

  if (exchangeRates.length) {
    const rate = Number(exchangeRates[0].rate);
    console.log(`Found exchange rate of ${rate} for ${fromCurrency} to ${toCurrency} of ${onDate} in the db`);
    return {fromCurrency, toCurrency, onDate, rate};
  }

  return getExternal(fromCurrency, toCurrency, onDate);
}

module.exports = {
  get
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到完整的应用程序。验证结构不是 MVC:)

因此，与其努力获得精确的 M-V-C 线，不如编写测试，实现持续集成。在应用程序中添加一些日志和监控。使代码可维护，尽可能保持精简和简单。

## 不承担 ORM 开销

对象关系映射(ORM)当我第一次看到 ORM 运行时，我对自己说这是程序员所知道的最好的事情之一。10 多年后，我会谨慎地向任何软件工程师推荐 ORM。

> 去年，我将一个完整的 ORM 实现重构为一个原始的 SQL 查询方式，它使应用程序的这一部分的执行速度提高了 20%。

最重要的是，数据库事务是显而易见的，代码可读性更好，因此更容易维护。

数据映射器或活动记录都带来了自己的观点、做事方式和额外的权重。这不仅会导致性能问题，还会影响代码的可读性。想想前挂钩和后挂钩/事件监听器原则，它们像魔术一样工作，理解魔术总是很棘手。

[![Do not code your microservice like a monolith, keep it maintainable not over-engineered](img/7a2325a5461798a1e77b7ee2bacff329.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LFfuhz5T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.img/microservice-code/pot-on-head.jpg)

试试这个，向初学者/初级软件工程师解释 ORM 相关的插入代码是如何工作的，而简单直接的插入 SQL 查询是如何工作的。你会后悔用了那个表格。特别是在微服务的背景下，ORM 是一个明显的开销。微服务预计将是最大的 10K 代码行，影响不到 10 个表，所以不要使用 ORM。

## 设计图案可能是一个包袱

我不是说你不需要学习[软件设计模式](https://en.wikipedia.org/wiki/Software_design_pattern)。你应该知道固体，德米特定律，工厂模式，策略模式，单例，适配器模式等。

> 嗯，如果你做面向对象编程，这些大部分都是有意义的，对吗？如果你在 Node JS 中写一个 1k 行代码跨~7 个文件的微服务会怎么样？

它只完成一小部分业务功能。所有这些模式在那个时候都变得很好。重新思考一下，所有这些模式和规则都是在微服务被广泛采用之前，在考虑大型代码库的情况下制定的。

设计模式与已经很大的代码库相关，并且在接下来的 6-12 个月内会变得更大，这是你通常使用的模块。对于现在有数百行代码的服务来说，它们可能会成为“额外的负担”,而在接下来的 6-12 个月内，它们将变成数千行代码。我们从来没有预见到它会比这更大，因为要做其他部分，我们将有另一个微服务。所以，保持你的微服务代码不含脂肪并经过良好的测试。

[![Do not code your microservice like a monolith, keep it maintainable not over-engineered](img/ec9b2c82a11de1d9a054c5640ecf8c61.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7JoEvmFC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.img/microservice-code/baggage.jpg)

## 结论

如果你仍然想像你工作的最后一个整体一样编码你的微服务，也许你做错了。再想想，如果你去一日游，你不会像去度假两周那样打包行李。想想代码性能和可维护性，让数据为你说话，打破常规。软件工程快乐！

* * *

最初发表于[geshan.com.np](https://geshan.com.np/blog/2018/10/dont-code-your-microservice-like-a-monolith/)。