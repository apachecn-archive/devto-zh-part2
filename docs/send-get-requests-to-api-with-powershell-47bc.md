# 使用 Powershell 向 API 发送 GET 请求

> 原文：<https://dev.to/mrpowerscripts/send-get-requests-to-api-with-powershell-47bc>

当我开始学习 Powershell 时，web 技术对我来说还是一个谜。我是一名简单的系统管理员，试图通过自动化让我的生活变得更简单。当然，我知道如何用一些 CSS 创建一个基本的 HTML 站点，但即使是 javascript 也远远超出了我的理解范围。虽然随着时间的推移，我对 web 开发产生了兴趣，这促使我了解 Powershell 如何与我的计算机或本地网络之外的事物进行交互。

[获取 Powershell 教程视频请求](https://www.youtube.com/watch?v=7mEmQgGowMY)

如何用 Powershell 刮一个网站？如何访问 API？如何将数据从我的计算机发送到 web 上的服务？很长一段时间我都有很多疑问。

[发布 Powershell 教程视频请求](https://www.youtube.com/watch?v=9piyM38it_8)

我发现了 API 的概念，并发现它非常神奇。为运行不同程序、使用不同代码的不同操作系统的不同计算机建立一个标准方法听起来很酷。我不需要浏览器来和这个网站互动？太神奇了！想想所有自动化的可能性！

最终，我发现了 RESTful 或者 REST API 设计。通过学习 REST APIs 的过程，我发现了 JSON 数据格式。作为一个必须为配置数据和数据传输处理大量冗长的 XML 文件的人，这看起来是一个惊人的选择。一旦我进一步了解了什么是 REST API 以及如何将数据格式化为 JSON，它就打开了通过 web 与其他系统通信的新世界。

我能够在 Powershell 中使用 Invoke-WebRequest 从各种 API 资源中获取或发送数据。我在网上到处寻找各种 API，看看我能用它们做些什么。这鼓励我学习如何制作自己的 API 接口。我最终成功地使用了像 Flask with Python 和 ExpressJS with NodeJS 这样的工具。

Powershell cmdlet Invoke-WebRequest 自动将 JSON 对象从 GET 请求转换为 Powershell 对象，这使得从命令行检查和处理数据变得非常容易。这使得测试和处理我通过自己制作的 API 传递的数据变得非常容易。

最终，像 Postman 和失眠症这样的请求工具变得流行起来，我通常在开发时避免使用 Windows。当我所有的网络工作开始时，Powershell 一直在那里帮助我，我仍然认为它是一个在命令行快速编写脚本和处理数据的非常棒的工具。物品很棒。

我在上面链接了我制作的关于使用 Invoke-WebRequest 进行 Get 和 Post 请求的两个视频。这两个是你开始使用 Powershell 的 API 和 web 自动化的有趣世界所需要的。快乐脚本！