# domcurl: curl + JavaScript

> 原文：<https://dev.to/paul_kinlan/domcurl-curl--javascript-1nkp>

很长一段时间以来，我一直在思考，当我们超越传统的网络浏览器时，网络的未来会是什么样子。我称之为无头网络，我想回答的是“如果一切都由‘网络’驱动，但你从未见过浏览器，那会怎样？”。具体来说，我认为，如果你可以使用完整的浏览器，但看不到“chrome ”,那么一系列新服务将有巨大的机会。

> 使用浏览器作为服务是一个不可思议的机会。它允许我们将声明性 HTML 与开发人员定义的 JavaScript 程序执行相结合，并对内容进行深入分析。
> 
> …
> 
> 在服务器上运行浏览器将允许我们更容易地构建解析动态生成的数据的服务，它将允许我们更容易地针对页面中的逻辑运行我们自己的逻辑(以表单填充为例),并且我相信它将提供针对页面上嵌入的数据更有效地运行操作的能力。

这花了一段时间，但我想我们快到了。

我被[木偶师](https://developers.google.com/web/tools/puppeteer/)迷住了。Puppeteer 是一个 JavaScript 库，位于 Chrome Dev Tools 协议之上，它允许您自动化和编写 Chrome 浏览器。

我的日常工作包括调试 web 服务器和确保。像许多开发人员一样，我使用`curl`向 web 服务器发出请求并检查响应。这是一个令人惊奇的工具，然而在当今世界，许多开发人员正在构建使用大量 JavaScript 构建的站点，这使得不可能检查完整的响应。

我决定创建一个类似 cUrl 的工具，用于获取资源并在名为 [`domcurl`](https://www.npmjs.com/package/domcurl) 的页面上运行 JavaScript。

`domcurl`是一个[小 NodeJS 应用程序](https://github.com/PaulKinlan/domcurl)，它使用了木偶师，可以通过发出下面的命令来安装:`npm
i domcurl`。像`curl`命令一样，您可以发出一个简单的`domcurl [url]`来获取资源并在页面上运行 JS。

它没有复制所有的`curl`，但是它有以下几个特点。

*   指定要提取的 url。即`domcurl [url]`
*   用`-v`检查响应头。即`domcurl -v [url]`
*   用`-b`设置 cookies，即`domcurl [url] -b "test=hello; Domain=airhorner.com; HttpOnly;" -b "hello=world; Domain=airhorner.com; HttpOnly;"`
*   使用`-H`参数添加自定义标题。
*   用`-o`手动设置 STDOUT，用`--stderr`手动设置 STDERR

我发现它非常有用，尽管它不能像`curl`那样传输结果，因为它必须等待 CSS 和 JS 被下载和执行。

我还冒昧地添加了几个特定于 JavaScript 和 Chrome 的额外特性。

*   输出一个 Chrome 开发工具跟踪文件(包括屏幕截图。)`domcurl --url https://example.com --trace test.json`
*   如果您需要将它集成到任何现有的应用程序中，可以将它作为 JavaScript 模块包含进来。

```
const {domcurl} = require('domcurl');
domcurl(`https://paul.kinlan.me/`, {}); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这个工具更多的是一个演示，而不是一个完整的服务，但我认为无头网络正在成熟，像木偶师和其他人这样的工具将帮助我们实现网络的持续力量。我们只需要为它而建。