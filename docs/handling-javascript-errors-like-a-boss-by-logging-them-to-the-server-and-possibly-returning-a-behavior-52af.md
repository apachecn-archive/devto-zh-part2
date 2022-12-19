# 像老板一样处理 JavaScript 错误，将它们记录到服务器，并可能返回一个行为

> 原文：<https://dev.to/mckabue/handling-javascript-errors-like-a-boss-by-logging-them-to-the-server-and-possibly-returning-a-behavior-52af>

我们向您的服务器发出请求，就好像您正在加载一个 JavaScript 文件。`url`将包含您想要传递给服务器的查询参数。
这种方法有两个优点:

1.  发送请求的代码很简单，因为我们只是创建/更改脚本标签`src`
2.  我们可以将一些行为从服务器返回到浏览器，可能会将用户重定向到其他位置