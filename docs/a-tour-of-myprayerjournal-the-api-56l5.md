# 我的祈祷者杂志之旅:API

> 原文：<https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-api-56l5>

*备注:*

*   这是系列文章中的第 4 篇；请参见[介绍](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-introduction-jl5)中的所有内容，以及构建该软件的要求。
*   *以文本“mpj:”开头的链接是指向 myPrayerJournal 的 1.0.0 标签(1.0 版本)的链接，除非另有说明。*

现在我们有了一个出色的、闪亮的、[反应式的](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-state-in-the-browser-95o) [前端](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-front-end-4589)，我们需要能够将一些数据放入其中。我们将通过 <abbr title="JavaScript Object Notation">JSON</abbr> 在应用程序和服务器之间进行通信。在这篇文章中，我们还将尝试解释一些作为 API 一部分的 F#语言特性。

## 数据

实体在 Data.fs ( [mpj:Data.fs](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Data.fs) )中定义。我们将在[“数据存储”文章](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-data-store-5efa)中更全面地挖掘它们，但是现在，我们将只关注类型和字段。我们有四种类型:`History`(33-39 行)`Note`(67-71 行)`Request`(94-110 行)`JournalRequest`(153-173 行)。一个`Request`可以有多个`Note`和`History`条目，而`JournalRequest`基于一个视图，它将这些条目组合在一起，并计算诸如请求的当前文本以及何时应该再次显示之类的事情。

我们没有应用特殊的 JSON 转换，所以这些记录类型中的字段是导出的 JSON 中的属性。

## 网址

为了将 API 与其他 URL 区分开来，它们都以`/api/`开头。请求 URL 一般遵循形式`request/[id]/[action]`，日志有一个单独的 URL。`Program.fs` ( [mpj:Program.fs](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Program.fs) )中的 54 线有路线的定义。我们使用了[长颈鹿](https://github.com/giraffe-fsharp/Giraffe)的[令牌路由器](https://github.com/giraffe-fsharp/Giraffe.TokenRouter)而不是传统的，因为我们不需要支持任何它不支持的 URL 方案。对于 API 支持的路由，结果看起来确实像一个漂亮、干净的“内容表”。 [<sup>1</sup>](#note-1)

不过，我们还没有完成路线。我们来看看那个`notFound`处理程序([mpj:handlers . fs](https://github.com/bit-badger/myPrayerJournal/blob/1.0.0/src/api/MyPrayerJournal.Api/Handlers.fs))；在第 27 行。因为我们提供的是一个 <abbr title="Single Page Application">SPA</abbr> ，我们需要为属于它的 URL 返回 SPA 的入口点`index.html`。想象一个用户坐在`https://prayerjournal.me/journal`前按下“刷新”我们不想退回 404！由于应用程序有一组有限的 URL 前缀，我们将检查其中是否有一个是 URL。如果是，我们发送 Vue app 如果没有，我们发送 404 响应。通过这种方式，我们可以为我们将收到的不可避免的黑客攻击返回 true 404 响应(pro tip，hackers - `/wp-admin/wp-upload.php`不存在)。

## 定义经手人

Giraffe 使用术语“处理程序”来定义处理请求的功能。处理程序有签名`HttpFunc -> HttpContext -> Task<HttpContext option>`(别名`HttpHandler`)，可以通过`>=>`(“鱼”)操作符组成。签名中的`option`部分是组成处理函数的关键。`>=>`操作符创建一个管道，将一个函数的输出发送到另一个函数的输入；然而，如果一个函数不能为`HttpContext`参数返回一个`Some`选项，它将短路剩余的逻辑。 [<sup>2</sup>](#note-2)

myPrayerJournal 中该组合的最大用途是确定用户是否登录。授权也在获得[自己的帖子](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-authentication-343e)，所以我们在这里只关注是/否的回答。`authorized`处理程序(第 71 行)寻找用户的存在。如果有，它返回`next ctx`，其中`next`是下一个`HttpFunc`,`ctx`是它收到的`HttpContext`；这导致一个继续处理的`Task<HttpContext option>`，有希望沿着愉快的路径并最终返回`Some`。但是，如果用户不在那里，它返回`notAuthorized`处理程序，同时传递`next`和`ctx`；然而，如果我们查看第 67 行和`notAuthorized`处理程序的定义，我们会看到它忽略了`next`和`ctx`，并返回`None`。但是，请注意，这个处理程序中有一些 fish 成分；`setStatusCode`返回`Some`(它已经成功了)，但是我们立即短路了管道。

我们可以在第 137 行开始的`/api/journal`端点的处理程序中看到这一点。`authorize`和它下面的内联函数都有`HttpHandler`签名，所以我们可以用`>=>`操作符组合它们。如果用户登录，他们会得到一个日志；如果没有，他们会得到 403 分。

当一个处理程序需要一个来自路由的参数时，处理程序的签名会有些不同。第 246 行的`/api/request/[id]`的处理程序有一个额外的参数`reqId`。如果我们回头看第 64 行`Program.fs`,我们会看到这个处理程序被分配了它的路线；如果你将它与 59 号线`/api/journal`的路线进行比较，你会发现它看起来是一样的。这里的区别是`route`(对于《华尔街日报》)和`routef`(对于请求)的期望。`route`不期望任何参数，但是`routef`将提取参数`Printf` style，并将它们作为`HttpHandler`签名之前的参数传递。

## 执行处理程序

myPrayerJournal 定义了`GET`、`POST`和`PATCH`路线。在接下来的几个段落中，我们将看看其中每一个的典型例子。

对于我们的`GET`示例，让我们回到`Request.get`处理程序，从第 246 行开始。一旦我们清除了`authorize`障碍，代码就试图通过传递给处理程序的请求 ID 和作为请求的一部分传递的用户 ID 来检索一个`JournalRequest`。如果它存在，我们返回它的 JSON 表示；如果没有，我们返回一个 404。注意`json`结果处理器的参数顺序——是`json [object] next ctx`(或`json [object] HttpHandler`)。我们还在第 75 行定义了一个`asJson`函数；这翻转了参数，因此输出对象是最后一个参数(`asJson next ctx [object]`)，启用了在第 137 行的`journal`处理程序中看到的流程。

对于我们的`POST`示例，我们将使用`Request.addNote`，从第 218 行开始。它检查授权，并确保当前用户的请求存在。如果一切正常，它就会使用 Giraffe 的`BindJsonAsync<'T>`扩展方法来创建预期输入表单的实例。由于处理程序没有指定预期输入对象的地方，这种类型的模型绑定不能自动发生；好的一面是，如果不需要的话，你不会浪费 CPU 周期去尝试这么做。一旦我们绑定了模型，我们就创建一个新的`Note`，添加它，然后返回一个 201 响应。

`PATCH`处理程序非常相似；`Request.snooze`就是这样一个处理程序，从第 291 行开始。该流程与`Request.addNote`的流程非常相似，除了我们正在更新而不是添加，并且我们返回 204 而不是 201。

## 配置 Web 服务器

长颈鹿在茶隼和 ASP.NET 核心基础设施上实现了[般温和的](https://suave.io)功能组合。myPrayerJournal 没有使用`Startup`类，而是使用了函数配置策略。呼叫在从 115 行开始的`Program.fs`中；还有很多关于如何配置 ASP.NET 核心的其他指南，所以我不会说太多。

请注意，第 31 行。前面，我们讨论了`>=>`操作符及其工作原理。这是一个`>>`复合运算符的例子，它是 F#的一部分。对于其输出可以直接进入下一个函数的输入的函数，`>>`操作符允许将这些函数组合成一个函数。如果我们要查看括号内组合函数的签名，它的签名应该是`string -> unit`；因此，我们将字符串“Kestrel”传递给它，它运行并返回`unit`，这就是我们期望的配置函数。这是如何分解的:

*   `ctx.Configuration.GetSection`的签名是`string -> IConfigurationSection`
*   `opts.Configure`的签名是`IConfiguration -> KestrelConfigurationLoader` ( `IConfigurationSection`实现`IConfiguration`)
*   `ignore`的签名是`'a -> unit`

如果这仍然没有意义，也许这将有所帮助。也可以使用`|>`管道操作符来编写`Configure.kestrel`函数，等效代码如下:

```
let kestrel (ctx : WebHostBuilderContext) (opts : KestrelServerOptions) =
  ctx.Configuration.GetSection "Kestrel"
  |> opts.Configure
  |> ignore 
```

Enter fullscreen mode Exit fullscreen mode

我们对 API 的介绍到此结束，不过下次我们会再看一遍，那时我们会更深入地研究使用 Auth0 的[认证和授权。](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-authentication-343e)

* * *

T5】当我们试图在很大程度上遵循 <abbr title="Representational State Transfer">REST</abbr> 原则时，REST 纯粹主义者可能会说，它还不够宁静，不足以自称为 REST。但是，嘿，我们确实使用了`PATCH`，所以也许我们会得到部分积分……

*Scott Wlaschin 有一篇名为[“面向铁路的编程”](https://fsharpforfunandprofit.com/posts/recipe-part2/)的伟大文章，它从总体上解释了这个概念，并具体解释了[fish 操作符](https://fsharpforfunandprofit.com/posts/recipe-part2/#an-alternative-to-bind)。把他的定义翻译成长颈鹿的处理程序，第一个函数是`switch1`，`next`参数是`switch2`，`HttpContext`是`x`参数；代替`Success`和`Failure`，返回类型利用选项的非此即彼性质，即`Some`或`None`。如果你想了解是什么让 F#成为如此伟大的编程模型，你会花更多的时间在他的网站上，而不是在 Bit Badger 博客上。*