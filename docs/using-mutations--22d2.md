# 在 Vue.js 中使用 GraphQL 突变👽

> 原文：<https://dev.to/malgamves/using-mutations--22d2>

[![Header](../Images/71da3a5de4badf3996218fe81cc857f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zpo7PNGM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yeoew36oi290er3a05hk.png)

在我的[上一篇文章](https://dev.to/malgamves/vue-x-hasuragraphql-2pki)中，我们从前端使用 Vue.js 的基本应用程序和后端 GraphQL 上的 [Hasura](https://hasura.io) powered Postgres 数据库开始。

[![malgamves](../Images/de2c5b8f35d0fd19ad172bb8922e7555.png)](/malgamves) [## Vue x Hasura GraphQL

### 丹尼尔·马达里索·菲里 10 月 18 日 184 分钟阅读

#graphql #javascript #vue #hasura](/malgamves/vue-x-hasuragraphql-2pki)

这很简洁，但也很基本。我们所能做的就是在数据库中查询我读过的书，仅此而已。正如我在上一篇文章的结尾提到的，随着我们的继续，我们将更深入地了解不同的 Vue 和 GraphQL 概念。在过去的几天里，我给这个应用添加了一些功能，这就是它现在的样子。

[![App Gif](../Images/442096f8769225f50ee54265c1371021.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aEQ1KYu7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jvh85capimaemrvuimn8.gif)

现在我可以通过点击*添加一本书*按钮时弹出的窗体来添加我喜欢的所有书。点击*刷新列表*，你应该会看到更新的书籍列表，你最近阅读的书籍被牢牢地放在底部😁

我们将从上一篇文章停止的地方继续。像往常一样，我将文章分为前端和后端部分，并回顾我所做的修改。以前，这个应用程序看起来像这样。

[![Prev](../Images/bdc91ec0fd645ad4d90ab563927ae7a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EunIF9FM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ajs93bdm1rhpuxyuuoq.gif)

## 前端位

这里的目的是让我们能够通过在表单中输入数据来添加一本新书。为此打开一个全新的页面是没有意义的，考虑到这一点，我提供了一个单击按钮就会弹出的模式。在 **src >组件**中，创建一个名为 **Modal.vue** 的新文件，并将以下代码粘贴到其中。