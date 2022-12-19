# 将电子视图内容转换为 PDF

> 原文：<https://dev.to/sathish/converting-an-electron-view-content-to-pdf-caa>

# 痛苦！🤖

大家好。我遇到了一个情况，需要做一个[电子 JS](https://electronjs.org/) app。我以前从未在电子 js 工作过。我的主要任务是将视图转换成 pdf。该死的。这是我非常讨厌的。另一个主要问题是视图是动态的。以一份报告为例。

我解决这个问题的第一个方法是检索带有样式的动态 HTML，并将其发送到 main.js 文件，该文件是使用 Ajax 请求在 Node js 中编写的。然后我可以将这些动态内容写入一个新的 HTML 文件。最后一步是使用适当的 pdf 包装器将 HTML 文件转换成 pdf 文件。哇哦。那是相当痛苦的！经过这么多的挖掘，我发现在没有外部包装的情况下，将视图转换成 pdf 格式是非常容易的。多亏了电子。

我现在将解释我遵循的步骤和代码。请从[这里](https://github.com/TheWebDevel/electron-pdf.git)克隆初始代码库。不要忘记运行`npm install`来下载电子依赖项，并运行`npm start`来启动应用程序。最初的应用程序如下所示:
[![Imgur](../Images/efc4055dc53407cb5596e1227498c6ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DNKia3R6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/wU46RKp.png)

# 解👻

我们将处理两个文件- `main.js`和`renderer.js`。

## main.js

下面先导入一下。`Note - Do not edit\delete the existing content of the file`。
[![Imgur](../Images/863cadb4562167e08aae8d282dd77b7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RhzsPpKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mw0vWcD.png)

#### 分解

fs 模块提供了一个 API，用于以一种紧密围绕标准 POSIX 函数的方式与文件系统进行交互。

`const os = require('os');`-OS 模块提供了许多与操作系统相关的实用程序方法。我们用它来创建一个临时位置来存储 pdf 文件。

`const ipc = electron.ipcMain;`-IPC main 模块是 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter) 类的实例。当在主进程中使用时，它处理从呈现器进程(网页)发送的异步和同步消息。从呈现器发送的消息将被发送到此模块。

`const shell = electron.shell;` -外壳模块提供与桌面集成相关的功能。

现在，让我们在文件底部添加一些代码，将 web 内容转换成 PDF。

[![Imgur](../Images/b9e4d152687c800b414876002a6dd144.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v0ZZ5PSf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/zligKTQ.png)

#### 上面的代码发生了什么？

首先，我们创建两个变量，即，`pdfPath` -使用上面导入的`os module`和`win`将 pdf 文件存储在临时位置，以获取窗口的内容。

然后我们使用`contents.printToPDF(options, callback)`将内容转换成 pdf。这将用`Chromium's preview printing custom settings`将窗口的网页打印成 PDF 格式。

完成后将使用`callback(error, data)`调用`callback`。数据是一个包含生成的 PDF 数据的`Buffer`。

然后我们用一个`fs writeFile method`来写 pdf 文件。写完文件后，我们现在发送一个名为`wrote-pdf`的事件，该事件将在`renderer.js`中处理。

哦！！我们忘记了`Convert to Pdf`按钮。打开`index.html`并添加以下代码。
T3![Imgur](../Images/7a92b85e3e6d758ff785a34c9b582799.png)T5】

## renderer.js

在`renderer.js`文件中写下下面的代码，我们稍后会谈到这一点。
[![Imgur](../Images/02ac01936573d4922036f43547727ccb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--38rCczua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1CadB4h.png)

#### 分解

ipcRenderer 模块是 EventEmitter 类的一个实例。它提供了一些方法，因此您可以从呈现进程(web 页面)向主进程发送同步和异步消息。您还可以从主进程接收回复。

然后我们向按钮添加一个`click`事件监听器。现在，当点击按钮时，我们向 main.js 发送一个事件`print-to-pdf`,它将处理转换过程。

转换后，main.js 现在将发送一个事件`wrote-pdf`，该事件用于打印 pdf 文件在电子应用程序中的位置。

# 最终结果

这将是最后的应用:
[![Imgur](../Images/e3ef8f44628e08739f8a79b56a809efa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UlxuXzOh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/S8t5ZrP.png)

点击按钮后，该位置将被写入应用程序视图，并创建一个 pdf 文件。
[![Imgur](../Images/01976008246b1054bcc40c8d35f87652.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--uqVDbx5Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/VF1OuA3.png)

#### PDF 文件

[![Imgur](../Images/ae9c2ffd0ba26184fb22afd4a44f5614.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3A_sElK9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/sYowe9T.png)

请随意查看最终的[代码](https://github.com/TheWebDevel/electron-pdf-final)。