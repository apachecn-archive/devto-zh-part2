# 关于 JavaScript 中的析构，你只需要知道

> 原文：<https://dev.to/quantumsheep/all-you-need-to-know-about-destructuring-in-javascript-1hla>

没有人能否认一个事实——任何东西，一旦建成，就可能被摧毁。这个概念也适用于编程。在本文中，我将讨论 JavaScript 中的析构。

在 JavaScript 中，析构是指分解一个对象的属性或一个数组的索引来分离它们以创建特定的变量。这并不意味着这些分离的对象或数组永远不能在程序中再次使用。

然而，在我们开始之前，我将快速概述数组和对象之间的一些重要区别。数组有数字索引，对象有字符串索引。此外，数组和对象使用不同的语法。

[![](../Images/94ef5d02472d92a4129bbc417864db01.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YHfw82al--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AJmmIJw7Sp36pKyQ8JsgAUw.png)

在 JavaScript 中，我们可以把对象放在数组中，也可以把数组放在对象中，语法让我们可以把任何东西放在我们想要的地方。

* * *

现在让我们来谈谈主题，析构。析构一个对象或一个数组并不意味着你将从程序中删除它并且不能再使用它，它意味着你将得到它的一个特定部分。我们用例子，用 Axios，一个著名的库。我们可以执行一个 HTTP 请求(如 Fetch API 或 XmlHttpRequest ),它会返回以下对象的模式:

[![](../Images/73638376c31d15d6d34abec17bb3285e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--i9J9OMLd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A60Jsu7-3_dUlS74MK5KJTw.png)

我们将坚持数据属性！它是一个包含所有服务器响应数据的对象。假设我们想从我们的 NodeJS API 和 MongoDB 数据库中获取所有用户，我们可以做一些类似于下面代码的事情。

[![](../Images/9af3dc0bd07d40917ce63791961b62d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_UjInaPB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AvQR1NPR7ba0u7pNOvtYvAw.png)

在这种情况下，`req`常量将是一个与之前看到的模式相同的对象。因此，为了获得用户数据，我们有包含用户数组的`req.data`。

现在我们已经看到了如何执行 Axios 请求，假设我们只想要一个可以从 route `/api/users/:userid`获得的用户。例如，如果想要的用户 id 是`7`，我们向`/api/users/7`发出请求。但是如果 API 返回一个数组呢？然后我们可以做`req.data[0]`,这可能是一个很好的方法，但不像我们使用析构函数那样实用…

首先，我们来获取`data`对象。我们可以做`req.data`，但是我们不关心其他请求的属性，所以我们只获取数据。我们要做的是一个对象析构赋值。(最后，有些激动)

[![](../Images/230a9a974e7dff544f259057b63f3ea0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HdD9QhZH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AmRLJVFae81GWKe4HN92e2g.png)

是的，我们只获得了`data`属性，它创建了一个名为`data`的对象！而且你已经用 JavaScript 完成了一个“析构赋值”，太棒了！

例如，我们可以在同一个析构赋值中析构多个属性，Axios 提供了一个`status`属性，所以让我们通过析构来获取它！

[![](../Images/68e1b84ebbd8bbe63c30f1c5ea6492c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ES3kWbC6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ACdBhBeLM15c95OT5wUSN1Q.png)

我们也可以给任何析构的属性赋予默认值，如下所示。

[![](../Images/c6f3e09ae82dd563d574781c96392644.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tqqXvoo0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AMqa0o0Rlg1suvJzefnnHtw.png)

析构属性的默认值，但是我们的对象名并不是我们真正想要的，我们希望一个`users`对象变得更容易理解。所以让我们在不创建任何新变量的情况下进行名称的赋值。

[![](../Images/22fb67d0cf6743fa43c7210d2c3b7c3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nCTFChIT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AivviQSrGSSYuVJ6bSDkCmA.png)

很酷吧？你可以在同一行中给新变量的名字分配析构属性，看起来一点也不差！所以现在我们有了一个名为`users`的对象。

尽管如此，保持一个数组，我们能用它做什么呢？我向你展示，数组的析构任务:

[![](../Images/4633596825dd8d08ead895ea08a676cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qvCKpM1N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A4EzYu67iOKLLIye9DW82Ig.png)

在这种情况下，`a`是一个新的常量，它将接收数组`[1, 2, 3]`的索引`0`(其值为`1`)。常量声明在析构赋值中的位置定义了将被取值的选定索引。

在这种情况下，a 是一个新的常量，它将接收数组`[1, 2, 3]`的索引`0`(其值为`1`)。常量声明在析构赋值中的位置定义了将被取值的选定索引。

像对象一样，我们可以有默认值和多个声明。它们的位置总是与选定数组的索引相匹配。

[![](../Images/0a470385cc443ba76591f2ad73694148.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T32z56jB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A0YnMr2QWr6L6X3iBCWhmtg.png)

我们也可以通过放置一个没有任何变量声明的`,`来绕过我们想要的任何索引。在以下情况下，我们将绕过索引`0`和`1` (2 次昏迷)。`c`将等于值为`6`的第三个指数。

[![](../Images/8afda8bc98928ce4ba0f89454f6a856c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g0xnXqX1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AK9OYIt3aqnVlJLBGSpQOIg.png)

绕过数组析构赋值中的索引现在我们知道了如何使用数组和对象的析构赋值，我们终于可以解决变量`users`是只有一个索引的数组的情况了。

我们可以在彼此内部使用析构赋值，这样就可以了。因此，将数组的析构赋值放入对象的析构赋值中，您将在所选属性上获得相同的结果:

[![](../Images/e8e722b20cdf8fd7dab9587ed1a6362a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wqR8WZrT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ah-SEMV_yjAGiBpWCe39Yww.png)

总而言之，我们已经析构了`axios.get('/api/users/7')`指令的属性`data`。接下来，我们将数据赋给一个合适的变量名`users`。之后，我们使用数组的析构赋值将变量`users`定义为`data`属性的第一个索引。

每个析构赋值都可以像前面看到的那样，以你想要的任何顺序递归使用。对象的析构赋值可以用于其他对象的析构赋值，数组的析构赋值也是如此。

* * *

现在我们完全知道如何在 JavaScript 中析构对象和数组。但是你可以用另一个技巧来处理破坏性的任务，即“休息模式”或“扩散”。这使您有可能将剩余的未指定属性/索引带到一个新变量中。这里有一个关于数组的演示，但是你也可以对对象做同样的事情:

[![](../Images/962bfa74ab4f9b66e2448b43d26a40ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---ErMCv7S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AK-rA3NKjh0mz7cq9BmKvmQ.png)

* * *

感谢您的阅读，希望现在您已经了解了 JavaScript 中析构的所有内容！