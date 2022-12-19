# 什么是 Ajax？

> 原文：<https://dev.to/gyi2521/what-is-ajax-34c2>

什么是 Ajax？

在我的编码训练营课上，当老师问“什么是 Ajax”时，有人马上回答说，“一个清理解决方案！”。我们都笑了，但这是计算机世界之外人们的回答。那么，Ajax 在计算机界是什么？

AJAX 代表异步 JavaScript 和 XML。它是一种用于创建交互式 web 应用程序的 web 开发技术。AJAX 允许通过在后台与 web 服务器交换数据来异步更新网页。这意味着无需重新加载整个页面就可以更新部分网页。

一个很好的例子就是你在谷歌搜索框中看到的谷歌建议列表。当你开始在谷歌搜索框中输入内容时，下拉框中的列表会发生变化。页面上的内容在不刷新页面的情况下动态变化。多酷啊！

[![alt text](../Images/675ae09ae7320bbefa6bda49733925c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ebAlB6I7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2t54lkf8pujkui7aknnc.png)

那么 Ajax 实际上是如何工作的呢？我从 w3schools 得到了下面的图和步骤。

[![alt text](../Images/9045ebe25b70ee11cc3f7caf1e75f302.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fIBFPBvJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r3vai97x0f4zockefp0y.gif)

AJAX 如何工作

1.  网页中发生了一个事件(加载了页面，单击了按钮)
2.  HTTP 请求对象是由 JavaScript 创建的
3.  HTTP 请求对象向 web 服务器发送请求
4.  服务器处理该请求
5.  服务器将响应发送回网页
6.  JavaScript 读取响应
7.  JavaScript 执行适当的操作(如页面更新)

我将在下一篇博客中解释如何创建、添加、更新和删除，敬请关注。