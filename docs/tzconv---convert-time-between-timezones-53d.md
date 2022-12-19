# tzconv -转换时区之间的时间

> 原文：<https://dev.to/dzeban/tzconv---convert-time-between-timezones-53d>

我做了一个漂亮的小东西，叫做`tzconv`–[https://github.com/alexdzyoba/tzconv](https://github.com/alexdzyoba/tzconv)。这是一个 CLI 工具，可以转换时区之间的时间，当您调查完成的事件并需要匹配时间时，它很有用(至少对我来说)。

想象一下，您在当地时间 11:45 发生了一起事故，但您在 ELK 或 Splunk 中的日志是 UTC。那么，世界协调时是几点 11:45？

```
$ tzconv utc 11:45
08:45 
```

Enter fullscreen mode Exit fullscreen mode

嘣！你猜对了！

您可以添加第三个参数来转换特定时区的时间，而不是本地时区的时间。例如，您的警报系统向您发送了一封带有中欧时间的电子邮件，而您的服务器日志时间戳是东部时间。

```
$ tzconv neyork 20:20 cet
14:20 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我打错了纽约，但它仍然工作。这是因为位置不是完全匹配的，而是模糊搜索的！

您可以在[项目自述文件](https://github.com/alexdzyoba/tzconv/blob/master/README.md#examples)中找到更多示例。请随意投稿，我有几件事希望能实现——查看[问题页面](https://github.com/alexdzyoba/tzconv/issues)。这个工具本身是用 Go 编写的，非常简单却很有用。

暂时就这样吧，下次再说！