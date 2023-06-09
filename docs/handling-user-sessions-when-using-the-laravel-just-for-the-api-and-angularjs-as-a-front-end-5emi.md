# 在使用 laravel 时处理用户会话，仅将 API 和 angularJS 用作前端

> 原文：<https://dev.to/nikolaof/handling-user-sessions-when-using-the-laravel-just-for-the-api-and-angularjs-as-a-front-end-5emi>

你好。

我正在开发一个注册用户和访客都可以使用的应用程序。那些用户能做的，就是上传一些文件。不同的是客人的档案会被临时保存。所以我试图设计一种方法来处理用户会话。

正如你在我下面的图片中看到的，注册用户登录后，JWT 将被存储在浏览器的本地存储器中，当用户的下一个请求是列出他/她的文件时，JWT 中会有一个变量告诉服务器在哪里寻找用户的文件(哪个路径)。

现在，对于来宾，我认为我应该做一些类似的事情，但是没有登录步骤。例如，当访客进入站点时，javascript 将检查本地存储上是否有未过期的令牌。如果这是真的，那么将从服务器创建一个指向临时目录的随机 JWT，并将其存储到浏览器中。但我不知道这种方法是否合理，也不知道是否有其他更好的方法来做到这一点。

这是我想象中的草图。

[https://sketch . io/render/sk-0 e 12 e 9 FCA BF 7 f 97 a 14449 EC 02679 ad 73 . JPEG](https://sketch.io/render/sk-0e12e9fcabf7f97a14449ec02679ad73.jpeg)

你怎么想呢?任何想法或建议都会有帮助。谢了。