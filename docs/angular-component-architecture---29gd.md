# 角组件架构

> 原文：<https://dev.to/candidj/angular-component-architecture---29gd>

我目前正在开发 Angular 6 项目，在这个项目中，我在单个模块或路径中有很多动作项目。

以下菜单项的最佳组件架构设计是什么:

1.  客户服务请求
    a .服务 A
    b .服务 B
    c .服务 C

2.  客户付款请求
    a .付款 A
    b .付款 B
    c .付款 C

所有服务/支付都需要 3 个屏幕:

1.  客户输入/表格
2.  确认页面
3.  成功页面。

目前，我有以下想法:

1.  一个请求模块，获取所有列表(如帐户列表)并将其作为@Input 传递给子组件。
2.  每个操作项(如服务 A)都有自己的组件(如服务组件)。服务组件还有一个用于确认和成功页面的子组件。(ServiceAConfirmationComponent)b . ServiceAConfirmationComponent 从其父组件(作为@Input)接收客户输入，并在成功时进行服务调用+显示/隐藏确认页面。它还为 cancel 发送(@Output)事件。

这是正确的方法吗？

欢迎任何帮助/评论。感谢阅读！！