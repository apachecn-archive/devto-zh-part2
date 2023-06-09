# 动态制作的按钮

> 原文：<https://dev.to/asalmeron3/dynamically-made-button-4e22>

在这篇文章中，我将尝试解释动态创建的按钮，因为我理解这个概念。这段代码的实现是通过一个独立创建的库来完成的，这个库类似于 jQuery，但它是为了学习和理解什么是库以及如何在我们的代码中使用它而创建的。

当您想要创建一个不属于原始 HTML 的组件时，您可以操作 DOM 来显示该组件。该组件可以作为用户执行某个动作、完成表单和/或点击某个按钮的结果而被创建。在我们的示例中，我们将创建一个按钮，作为单击另一个 id 为#add 的按钮的结果。

[![alt text](img/9253a9a5e470ce8f293d2144f54b463b.png "function makeOneNewButton with click event on #add button")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f2Wo1tO1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4bm6atk7yw1ohhvujppc.PNG)

我们的 DOM 最初只有#add 按钮:
[![alt text](img/54a0b35336fdbdc2d69d3674bf772b6c.png "add button on DOM")](https://res.cloudinary.com/practicaldev/image/fetch/s--pfpPXj3c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wtdrtnuaj0fk0u54que5.PNG)

我们点击添加按钮后，我们在页面上成功添加了我们的新按钮:
[![alt text](img/db2d81e3225541598c9a273b95dc6651.png "new button on DOM")](https://res.cloudinary.com/practicaldev/image/fetch/s--XJfpeK55--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hneueabiawd6pf8fzb01.png)

现在，让我们创建一个函数，当我们单击 id 为#myNewButton 的新按钮时运行该函数:
[![alt text](img/196ffa5391ab8f7e1507e89158c2be0d.png "testNewButton function")](https://res.cloudinary.com/practicaldev/image/fetch/s--yjCu-ZtV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kplbudmf67smw37jrb1h.PNG) 
[![alt text](img/7f5bf7648b10a72cd6b677626d56d906.png "associating new function to our new button")](https://res.cloudinary.com/practicaldev/image/fetch/s--5Y5dwEMC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bakrzv290i39x85pnfwx.PNG)

有了新的代码行，我们希望新的按钮在我们点击它的时候能给我们一个消息。然而，控制台中什么也没有显示:
[![alt text](img/5c40a8861d2ecd908a704dfbec9747df.png "DOM after clicking on new button")](https://res.cloudinary.com/practicaldev/image/fetch/s--Tuyc01Lk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/udef1ah8f3z03d3o6926.png)

为什么没有成功？我们的代码编写正确，但我们没有在控制台中看到新消息。这是一个相当难理解的概念，它与动态改变我们的 DOM 以及[冒泡和捕获的概念有关。](https://javascript.info/bubbling-and-capturing)

我认为新按钮不在初始代码中，因此我们的函数没有一个引用点来将我们的侦听器(click 事件)连接到新按钮。换句话说:我们的 testMyNewButton 函数无法绑定到#myNewButton，因为该按钮不是加载的初始 html 的一部分。

因此，让我们把这个功能添加到已经存在的东西上。我们可以将该功能与点击文档、正文或原始 HTML 中的任何其他组件联系起来。对于本例，我们将把单击 div 的功能与。容器类，因为这是我们添加了新按钮的 div。
[![alt text](img/db7d6f9ceded776748a09bc5ae2a1d19.png "associating function to .container")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--3ojh1zXZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eo476aa1rdkeu5fyyqcs.png)

现在，如果我们刷新 DOM 并单击 new 按钮，我们会在控制台中得到一条新消息:
[![alt text](img/2cbf73898be098f78f413063cf9d7515.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c446qSn1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xjawlon09dd1e7tzmb7d.png)

好像很管用！但是如果我点击我们新按钮的右边会怎么样呢？
[![alt text](img/669f2d28a9e303d3b579a36f33c4ba78.png "all of the .container div")](https://res.cloudinary.com/practicaldev/image/fetch/s--NrEjQf5I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1aikck851htfwnchfv1g.png) 
我们的控制台也在这里记录了另一条消息。这符合我们的准则。记住，我们将函数与整个。因此，我们希望函数在单击。容器:
[![alt text](img/db9205440d5293234a23a54724d7fc47.png "clicked anywhere in .container")](https://res.cloudinary.com/practicaldev/image/fetch/s--d-ZlYuoW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f3h8rlr6gmtptwaxwivv.png)

为了微调并单独将点击事件与我们的新按钮相关联，让我们向我们的函数添加一些条件，以便检查用户点击的元素的 id。首先，让我们创建一个变量来存储我们将要点击的元素的 id。为此，我们将使用初始的“submittedOrClickedEvent”参数并抓取目标。目标将是我们的点击事件起源的最内部的元素。如果该元素有一个 id，我们将希望存储它:
[![alt text](img/6c6f43349ea6899730ce045937baa8a5.png "id of our target element")](https://res.cloudinary.com/practicaldev/image/fetch/s--sGWWmk4K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k6e1i7qxn90xae35vf72.PNG)

现在我们有了一个具有元素 id 的变量，我们可以用它来比较我们的新按钮#myNewButton 的 id:
[![alt text](img/26d6fac67ad614c7635834ef4f26feb8.png "conditions when clicking on .container")](https://res.cloudinary.com/practicaldev/image/fetch/s--aVRre4Qi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jdkndbogmf7cdwfzjr2.PNG)

一旦我们刷新我们的 DOM 并点击 new 按钮，我们将看到我们的函数运行并打印到控制台:
[![alt text](img/92165117a18a21e2c3f1026356013d2e.png "clicked on new button")](https://res.cloudinary.com/practicaldev/image/fetch/s--Lp9MJ2hi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/73ri045oddzbj8anh9gz.png)