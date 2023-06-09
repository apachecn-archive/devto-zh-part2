# 网站缓存控制设置的最佳实践

> 原文：<https://dev.to/alex_barashkov/best-practices-for-cache-control-settings-for-your-website-1obf>

你可能已经注意到，有时，当第二次访问一个网站时，它看起来不像预期的那样，一些风格被破坏，使一切看起来很奇怪。通常，这个问题的原因是定义不完善的缓存控制策略，这些策略阻止您在最近的部署之后接收最新的更改。在本文中，我将向您展示正确的缓存设置和技术，帮助您在每次部署后为所有用户更新您的网站。

对于那些只想获得最佳实践并开始使用它们的人，请点击本文末尾的链接。

### 缓存如何在幕后工作？

您的浏览器在每次请求网站/资源时，都会通过从本地内存读取缓存信息来尝试加载尽可能少的数据。只有我们为浏览器提供足够的说明，解释它需要保留什么资源以及保留多长时间，这才是可能的。

这些指令起到指令的作用；要告诉您的浏览器，您必须将它们添加到响应 HTTP 头信息中。缓存过程中最常见的指令有**、【缓存控制】、**、【过期】、**、【Etag】、**、【最后修改】、**。******

默认情况下，几乎每个 web 服务器在头响应中都有一些缓存设置，但是不清楚如果没有缓存策略，我们会得到什么。

如果没有缓存控制设置，浏览器会针对每个资源请求访问 web 服务器，并从中读取信息。这增加了受影响网站的加载时间，在传输信息时增加了 web 服务器的额外负载，并增加了对后端的调用次数。

[![Requests flow with no cache settings](img/6d5f1d63ef2b609a17e40e77b004a164.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ekD9gmUF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AlQnGue9Sakhrrp4jTAOZow%402x.jpeg) *无缓存设置的请求流*

由浏览器决定做什么以及如何缓存信息，而无需来自服务器的指令。目前 Chrome 和 Safari 每次都是从后端下载数据，没有缓存指令。然而，这可能会改变并导致不同的行为，尤其是在其他平台上。

为了清楚地定义如何处理某些文件，让我们深入学习缓存控制指令，一步一步地将它们添加到响应头中，并观察结果。

**Etag(实体标签)**

Etag 是缓存设置之一。这个 HTTP 头背后的主要思想是允许你的浏览器知道对相关资源的修改，而不用下载完整的文件。服务器可以用每个文件的散列和进行类似的计算，然后将这个散列和发送给客户机。下次客户端试图访问这个资源时，浏览器不会下载文件，而是在 HTTP 头中发送类似这样的内容:**If-None-Match:W/" 1d2e 7–1648 e 509289 "**。然后，服务器将对照当前文件的哈希和检查该哈希和，如果有差异，则强制客户端下载新文件。否则，将通知客户端应该使用缓存版本。

[![Requests flow with Etag — 1st load](img/aa9e00d4d01f007d175375966c895c8f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s----bTgUwA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2An3A-uFOdl_oU6LhSGpA-Kg%402x.jpeg) *带 Etag 的请求流—第一次加载*

[![Requests flow with Etag — 2nd load](img/90c50e86c8f04055744c835e30048cb1.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--cuUIfW7t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AuM5Nver6fFIe6dwvsBXIsA%402x.jpeg) *带 Etag 的请求流—第二次加载*

在 Etag 缓存策略打开的情况下，我们总是去服务器检查文件的哈希和，只有在这之后，浏览器才会决定从缓存中取出它或完全加载它。当资源未被修改时，无论您请求的是 10KB 还是 10MB 文件，都只需 80–100 字节即可验证。

**最后修改时间**

另一个缓存控制设置是“最后修改”HTTP 头。主要思想与 Etag 非常相似，但是浏览器的行为有点不同。服务器有每个文件的最后修改日期的时间戳；在第一次加载文件之后，客户端能够询问服务器，自从客户端最后一次访问文件的特定时刻以来，资源是否已经被修改。为此，浏览器在 HTTP 头中发送 **If-Modified-Since: Fri，2018 年 7 月 13 日 10:49:23 GMT** 。如果资源已经被修改，浏览器必须下载一个新文件，否则它使用缓存的版本。

实际上，浏览器有自己的内部缓存策略，可以自己决定是否从缓存中获取资源或下载新的副本。

> Last-Modified 是一个弱缓存头，因为浏览器应用启发式方法来确定是否从缓存中获取项目。和启发式算法因浏览器而异。
> **谷歌缓存最佳实践指南**

[![Requests flow with Last-Modified — 1st load](img/3f569cfbcde37cc573c9b0c6a287303a.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Dl5o8EC0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AF3RwCxY65xWkb0zjzWmqAg%402x.jpeg) *最后修改—第一次加载的请求流*

[![Requests flow with Last-Modified — 2nd load (Perfect Scenario)](img/c1ea2b8232a019b756c16dbbe84fcc33.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--IQoCw_3D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AzKRwT_36r_j4H2QOJ75bQQ%402x.png) *最后一次修改的请求流—第二次加载(完美场景)*

[![Requests flow with Last-Modified — 2nd load (Common case)](img/fe00d08fa6c121a31120dbfeefd6ceb6.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--EeyvKxKo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A_uPJ65NH43nvWwkF_qr6Hg%402x.jpeg) *最后一次修改的请求流—第二次加载(常见情况)*

因此，我们不能只依赖于最后修改，所以我宁愿完全删除它从我的服务器设置，以减少流量，即使它只是几个字节。

**缓存控制最大年龄**

这个指令允许我们告诉浏览器，从第一次加载开始，文件应该在缓存中保存多长时间。浏览器应该将文件保存在缓存中的时间应该以秒为单位定义，通常表示为 Cache-Control: max-age=31536000。有了这个策略，浏览器完全跳过了向服务器发出请求的过程，打开文件的速度非常快。但是我们怎么能确定文件在这么长时间内不会改变呢？我们没有。

因此，为了迫使浏览器下载所需文件的新版本，我们使用了由许多资产构建工具实现的技术，如 Webpack 或 Gulp。每个文件都将在服务器上预编译，哈希和将添加到文件名中，例如“app-72420c47cc.css”。即使对文件的微小更改也会反映在散列值中，这保证了它会被识别为不同的。因此，在下一次部署之后，您将获得该文件的新版本。这适用于我们所有的 css、js 和 images 文件(max-age = 31536000)；在我们改变了一些东西之后，浏览器将会请求一个新的文件，这个文件有一个新的散列和，然后它会缓存这个散列和。

**无缓存**
上述技术的棘手之处在于你不能忘记你的 html 文件；如果您也将该设置应用于这些文件，您将永远不会获得 css、js 或图像文件的新链接，直到您强制重新加载。

我建议您对 html 文件应用 Cache-Control: no-cache。应用“无缓存”并不意味着根本没有缓存，它只是告诉浏览器在使用缓存中的资源之前验证服务器上的资源。这就是为什么我们需要将它与 Etag 一起使用，因此浏览器将发送一个简单的请求并加载额外的 80 个字节来验证文件的状态。

### 最终设置

*   使用 Gulp、Webpack 或类似的工具为您的 css、js 和图像文件(如 app-67ce7f3483.css)添加唯一的哈希数字；
*   对于 js、css 和图像文件，设置 Cache-Control: public，max-age=31536000，无 Etag，无最后修改的设置。
*   对于 html 文件，使用 Cache-Control: no-cache 和 Etag。

因此，正如我们所看到的，即使是显而易见和常见的事情，如缓存静态文件，如果我们深入研究，可能也不明显。好的研究可以防止你犯错误，减少服务器上的流量，减少站点速度和加载时间。

如果你觉得这篇文章有用，点击❤️按钮！

[原帖链接](https://medium.com/pixelpoint/best-practices-for-cache-control-settings-for-your-website-ff262b38c5a2)
[我在媒体上的公司博客](https://medium.com/pixelpoint)