# NodeJS 中的回调函数

> 原文：<https://dev.to/martinnrdstrm/callback-functions-innodejs--2607>

[![a lot of code!](../Images/650c7367bd7e07e80579d3cadf9e94de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DVJvseKb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/djcef43bckrb6yijykao.jpeg)

### node . js 是什么？

NodeJS 是服务器端“Java 脚本”的运行时。你可能已经知道，我们在客户端(浏览器)有 Javascript，它几乎支持我们在网上看到的一切。有许多不同的客户端框架运行在 Javascript 上，如 React、Angular、Vue 等。但是 NodeJS 让我们做的是在服务器端运行它。NodeJS 也是一个异步平台，它不会等待事情结束，它是非阻塞的。但是它是怎么做到的呢？复试！
回调是在任何给定任务完成时调用的函数。因此，如果我告诉 Node 去做某件事，一旦任务完成，我们就可以有一个回调函数来做其他事情。它基本上允许其他代码同时运行。
所以我想展示一下它的功能和外观。

### 代码

我们可以从引入文件系统包开始，因为我想处理磁盘上的一些文件。我已经在与我的`app.js`相同的目录中预写了一个名为 helloWorld.txt 的文件。

`const fs = require('fs')`

现在我们要做一个匿名函数:

```
const fs = require('fs')
let results = (path) => {
  fs.readFile(path, 'utf8', function(err, contents) {
    console.log(contents)
  })
}
results('./helloworld.txt') 
```

Enter fullscreen mode Exit fullscreen mode

我们做的第一件事是通过小路。然后我们想异步读入一个文件，所以我们给它一个路径，一个编码 utf8，最后我们传入一个回调函数(我没有使用箭头函数，因为如果你看到关键字函数会更容易)。一旦读取文件*完成*，该功能将执行。

这是命令:

*   `readFile()`会跑。
*   `function(err, contents)`将在`readFile()`完成后运行。

在我们的回调函数中，我们传递一个错误，不是因为我们会得到一个错误，而是因为我们遵循了标准的回调模式。我们还传入从读取文件中返回的内容。

到目前为止，我们已经创建了一个非常标准的匿名函数(我们还没有给它起名字),它接受一个路径，并将它存储在 let *results* 中。

最后，我们可以通过调用带有`()`的函数并传入一个路径来调用`reader`。

点击`CMD + S`或`Ctrl + S`，调出你的控制台，然后输入`node app.js`(或者你给你的文件起的名字)。您的输出应该是文本文件中的内容。

你完了。