# 我怎么把拉勒维尔和卢蒙绑在一起？

> 原文：<https://dev.to/ppshobi/how-do-i-tie-laravel-and-lumen-together-4cod>

> 我在 SO 里问了这个问题，但是 20 个小时后没有回应，如果你们中的任何人可以在这里回答，或者在 SO 里投赞成票，以便它得到更多的关注。[原始堆栈溢出问题](https://stackoverflow.com/questions/53043684/how-do-i-tie-laravel-and-lumen-together)

我正在从头开始构建一个应用程序，它有两个主要部分

1.  一个 Laravel 服务器——它将通过查询一个 API(最好没有数据库交互)来提供 HTML 页面
2.  一个 Lumen 服务器——它将是被查询的 API(这里是数据库交互)

现在我需要登录一个用户。

我可以从 laravel 服务器向 lumen API 发送用户名和密码，并获得一个令牌(我认为是这样，如果我错了，请纠正我)。

*   **之后我该怎么做才能让用户登录的*Laravel 服务器？***
*   用某个密钥保持令牌会话？
*   **我如何利用 Laravel 服务器中的`Auth::user()`功能实现这一点？**
*   或者有什么好的方法来解决这个问题？