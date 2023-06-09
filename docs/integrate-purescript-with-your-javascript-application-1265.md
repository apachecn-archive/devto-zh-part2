# 将 PureScript 与您的 JavaScript 应用程序集成

> 原文：<https://dev.to/maciejsmolinski/integrate-purescript-with-your-javascript-application-1265>

PureScript 是一种编译成 JavaScript 的强类型函数式编程语言。这意味着我们不仅可以在新的应用程序中受益于类型安全，而且可以在现有的应用程序中受益。

PureScript 有一个[顶级的 FFI(外来函数接口)](https://leanpub.com/purescript/read#leanpub-auto-the-foreign-function-interface)允许我们从 PureScript 内部调用 JavaScript 函数。不仅如此，我们还可以使用 JavaScript 中的 PureScript 模块。

## [T1】安装 PureScript](#installing-purescript)

首先，我们需要安装全局依赖项 PureScript 编译器、包管理器和构建工具:`yarn global add purescript@0.11.7 psc-package pulp`。

[https://www.youtube.com/embed/6swGCTT4fEg](https://www.youtube.com/embed/6swGCTT4fEg)

## 生成项目结构

构建工具 Pulp 允许我们通过运行一个命令来生成一个基本的项目结构:`pulp --psc-package init`。

它将创建`src`和`test`目录以及包含依赖项列表的`psc-package.json`。创建完成后，pulp 会将 PureScript 包安装到`.psc-package`目录中。

您现在可以通过键入`pulp --watch run`来编译和运行`src/Main.purs`。修改完`src/Main.purs`的内容后，纸浆会自动重新编译并运行该模块。

[https://www.youtube.com/embed/RqaBznBQkG4](https://www.youtube.com/embed/RqaBznBQkG4)

## 安装代码捆绑器

由于我们想要构建一个与 PureScript 集成的 JavaScript 应用程序，代码捆绑器将会派上用场。

[包](https://parceljs.org/)有助于毫不费力地传输 ES6 代码，捆绑模块，并在浏览器中自动重新加载代码，无需额外配置。您可以使用`yarn add parcel`安装软件包。

### 定义 npm 脚本并运行捆绑器

一旦安装完毕，向`package.json`文件添加一个脚本通常是一个好的做法，这样我们就可以轻松地运行捆绑器。我们将定义`dev`脚本，在终端中运行`yarn run dev`之后，该脚本将捆绑代码并在端口`1234`上为应用程序提供服务。

```
// package.json
"scripts": {
  "dev": "parcel serve src/index.html"
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们创建`src/index.html`

```
<!-- src/index.html -->
<html>
  <head>
    PureScript Application
  </head>

  <body>
    <script src="./index.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

和`src/index.js`

```
// src/index.js
console.log('Hello from JavaScript'); 
```

Enter fullscreen mode Exit fullscreen mode

现在，在执行了`yarn run dev`之后，一个非常基本的 JavaScript 应用程序已经启动并运行在 [http://localhost:1234/](http://localhost:1234/) 上。

[https://www.youtube.com/embed/KzXsgVKFALQ](https://www.youtube.com/embed/KzXsgVKFALQ)

## 从 JavaScript 调用 PureScript

现在，最后一步。我们希望在我们的 JavaScript 应用程序中执行来自`src/Main.purs`的 PureScript 代码。我们仍然希望`yarn run dev`在后台运行。

然而，我们不是运行`pulp --watch run`并执行 PureScript 代码，而是运行`pulp --watch build`来构建代码并跳过执行部分。

### pure script 模块

现在，当两个命令都在后台运行时，我们可以看看我们的`src/Main.purs`模块。

```
module Main where

import Prelude
import Control.Monad.Eff (Eff)
import Control.Monad.Eff.Console (CONSOLE, log)

main :: forall e. Eff (console :: CONSOLE | e) Unit
main = do
  log "Hello from PureScript!" 
```

Enter fullscreen mode Exit fullscreen mode

本质上，我们可以知道这个模块名为`Main`，它只有一个名为`main`的方法。为了告诉编译器`main`函数是有效的，它引入了一堆其他模块，特别是 JavaScript 控制台。它还导入了`log`函数，该函数接受一个字符串并在 JavaScript 控制台中打印出来。`main`函数不产生任何值，因此在类型定义中有`Unit`。

### 导入模块

接下来，在我们理解了 PureScript 模块之后，我们可以从 JavaScript 文件中导入编译后的输出。

```
// src/index.js
const Main = require('../output/Main/index');

console.log('Hello from JavaScript');

Main.main(); 
```

Enter fullscreen mode Exit fullscreen mode

再次打开浏览器窗口后，我们可以看到我们手工编写的 JavaScript 代码和由 PureScript 编译器生成的 JavaScript 代码都被执行并打印到 JavaScript 控制台。

太棒了。我们只是用 PureScript 集成了现有的 JavaScript 代码。现在我们可以自由地用两种语言写作了。随着语言的学习，我们也可以在我们代码库的某些区域逐步引入 PureScript。

[https://www.youtube.com/embed/RPsyNpeyBp4](https://www.youtube.com/embed/RPsyNpeyBp4)

### 下一步

我们了解到将 PureScript 代码与 JavaScript 集成并不一定是一项困难的任务。包裹和纸浆使它成为一个简单的过程。package 的文档还解释了[如何捆绑我们的生产应用](https://parceljs.org/production.html)。

说到学习 PureScript，我强烈推荐该语言的创造者 Phil Freeman 的[《pure script by Example》一书。这是学习语言和函数式编程概念的绝佳资源！](https://leanpub.com/purescript)

祝你好运！