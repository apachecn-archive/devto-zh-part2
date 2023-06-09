# 我的祈祷者杂志之旅:简介

> 原文：<https://dev.to/danieljsummers/a-tour-of-myprayerjournal-introduction-jl5>

最近，我们发布了 myPrayerJournal 的 1.0 版本，这是一个简约的祈祷日志应用程序。本系列旨在提供一个代码之旅，沿途有几站:

*   **第 0 部分:简介** *(本帖)*
*   **[第 1 部分:前端](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-front-end-4589)** - Vue 组件和路由
*   **[第二部分:浏览器中的状态](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-state-in-the-browser-95o)** - Vuex 并从 API 获取信息
*   **[第三部分:API](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-api-56l5)**——长颈鹿和 JSON web 端点
*   **[第 4 部分:认证](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-authentication-343e)** - Auth0，使用 app 和 API 中的信息
*   **[第 5 部分:PostgreSQL 支持的数据存储](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-the-data-store-5efa)** - EF 核心，F#中定义了`DbContext`
*   **[第 6 部分:文档](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-documentation-4nf3)**——每次提交时生成的 GitHub 页面
*   **[第 7 部分:结论](https://dev.to/danieljsummers/a-tour-of-myprayerjournal-conclusion-296l)**——基于开发经验的经验教训和意见

从技术的角度来看，这个应用程序将是一个学习的经历。我们知道我们想要使用带有 API 的单页面应用程序(SPA)框架；我们以前构建过 API，但还没有构建过 SPA。对于前端框架，我们从 [Angular](https://angular.io) 开始，经过 [Aurelia](https://aurelia.io) 和 [Elm](http://elm-lang.org) ，再决定 [Vue](https://vuejs.org) 。对于后端 API，我们从 [Suave](https://suave.io) 开始，然后用 [Koa](https://koajs.com) 上线[node . js](https://nodejs.org)；后来我们把它移到了 [Go](https://golang.org) ，以及之后。网芯 2.1 发布，登陆[长颈鹿](https://github.com/giraffe-fsharp/Giraffe)。“学习经历”部分是成功的；通过所有这些尝试，我们使用了 5 种不同的语言和 3 种不同的数据库访问技术。

为了理解需求，对过程的简短解释将会有所帮助。“祈祷日志”是一种训练，一个人会写下他们为之祈祷的事情；这提供了一个明确的列表来帮助指导他们的祈祷，并帮助他们不忘记事情。然后，随着情况的变化，他们可以记录更新，直到情况得到解决(也称为请求被“回答”)。这种训练不仅有助于集中精力，还提供了请求和回答的记录。虽然人们已经成功地使用笔记本或类似的东西很长时间了，但这种方法也有一些缺点:

*   对于长期请求，您可能没有空间进行更新。
*   物理日志一次只能在一个地方。
*   已回答的请求和未回答的请求并存，所以你必须跳过它们。
*   书籍可能会被其他东西压在下面，成为“眼不见，心不烦”的牺牲品。

为了解决其中的一些问题，最初的需求从下面的前三个要点开始。其余的需求是在开发应用程序的过程中出现的。

*   列出未回答的请求，以某种方式将它们标记为祈祷或回答，并进行更新
*   列出已回答的请求，并允许查看完整的请求(及其历史记录)
*   以不分散注意力的方式做上面的事情
*   允许为请求记录笔记；并不是每一个情况的更新都需要改变请求的措辞
*   允许请求被“暂停”(从日志中删除，指定它们重新出现的日期)，并列出暂停的请求，以便暂停可以过期(立即将请求返回到日志)
*   允许优先处理请求(这成为了请求重复特性)

了解了这些要求后，我们将在下次继续讨论 Vue 前端。