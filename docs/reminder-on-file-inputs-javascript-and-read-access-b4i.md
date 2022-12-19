# 关于文件输入、JavaScript 和读取权限的提醒

> 原文：<https://dev.to/raymondcamden/reminder-on-file-inputs-javascript-and-read-access-b4i>

让我先声明一下，我今天要讲的内容其实并不新鲜。这是我以前在这里讨论过的东西。但在通过电子邮件与一位读者交谈后，我不得不写一份快速测试来亲自证实这一点。我不认为这是一个安全问题，但我有点惊讶，因此我认为最好是快速写一篇博文。

让我们从一些基础开始。我假设您知道浏览器中运行的 JavaScript 不能访问您的文件系统。那真是一件非常非常好的事情。Chrome 曾经支持一个文件系统 API(它可能仍然支持它，但它肯定会被否决),该 API 允许您访问沙盒文件系统，但它肯定不允许接触用户的主文件系统。既然 IndexedDB 中的二进制支持已经得到很好的支持，那么就没有必要让*将*文件写入磁盘。

然而，JavaScript 可以*读取用户通过文件类型输入字段选择的*文件。您可以在下面看到一个简单的演示:

[https://codepen.io/cfjedimaster/embed/ZxrqEz/?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/ZxrqEz/?height=600&default-tab=result&embed-version=2)

请确保只选择文本文件，但是也可以读取二进制数据。(代码只需要针对它进行调整。)还有，我为没有用 Vue 道歉。我感觉很糟。；)

这就是有趣的小花絮出现的地方。在我之前的一个演示中，我展示了选择图片和预览。它也支持多重选择。所以你可以选择一张图片。那就再选一个。诸如此类。

这个演示展示的，也是我没有真正注意到的是，一旦用户选择了一个文件，你就可以读取它，即使在他们选择了另一个文件之后。正如我说过的，我知道为什么会这样，这本身并不是一个安全问题。我是说，用户确实选择了文件。但是让我有点惊讶的是，在我清除了我的选择之后，我仍然可以阅读它。这个 CodePen 演示了这一点，但效果不太好(我稍后会解释原因):

[https://codepen.io/cfjedimaster/embed/MVrLNo/?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/MVrLNo/?height=600&default-tab=result&embed-version=2)

这个演示让你选择一个文件，然后更多，然后更多(等等。)，最后全部上传到邮差。Postman 似乎没有很好地处理结果，但从我在 DevTools 中看到的情况来看，所有文件肯定都在上传。

我想这就是我要说的。有人感到惊讶吗？还是只有我感到惊讶？