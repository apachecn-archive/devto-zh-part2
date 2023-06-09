# Bootstrap a Reason-React 项目包

> 原文：<https://dev.to/scastiel/bootstrap-a-reason-react-project-with-parcel--13fj>

不久前，我经常使用 Create-React-App 来快速启动我的 React 项目。这就是为什么当我想使用 Reason-React 时，我很自然地使用了 [Reason 脚本](https://github.com/reasonml-community/reason-scripts)。但是，即使我仍然理解 CRA 和推理脚本方法的优点，我最近发现了[包](https://parceljs.org/)，一个非常简约的 web 项目捆绑器。

已经有很多文章解释了 Parcel 的优点，例如如何与 React 一起使用。我在这里想做的是向您展示如何使用它开始一个新项目，首先使用[原因](https://reasonml.github.io/)，然后添加[原因-反应](https://github.com/reasonml/reason-react)来编写反应组件。

*TL；DR:我在 GitHub 上创建了一个[项目，包含了这篇文章的最终代码，如果你只是想看看最终结果的话。](https://github.com/scastiel/parcel-reason-react)*

## 开始一个基本的宗地项目

首先，让我们用 Yarn 初始化一个新项目(NPM 应该也工作得很好)并添加包:

```
$ mkdir reason-parcel && cd reason-parcel
$ yarn init -y
$ yarn add --dev parcel-bundler 
```

Enter fullscreen mode Exit fullscreen mode

让我们编辑生成的 *package.json* 来添加`start`脚本:

```
{  "name":  "reason-parcel",  "version":  "1.0.0",  "main":  "index.js",  "license":  "MIT",  "scripts":  {  "start":  "parcel public/index.html"  },  "devDependencies":  {  "parcel-bundler":  "^1.6.2"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们创建我们的 *public/index.html* 文件:

```
<html>
<body>
  <script src="../src/index.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们添加一个`src/index.js`文件，这样我们就可以测试我们的第一个版本:

```
console.log("Hello from JavaScript!"); 
```

Enter fullscreen mode Exit fullscreen mode

要启动项目，让我们运行`yarn start`:

<center>![Hello from JavaScript!](img/4cccac12a3702636ff2c305544c5aacc.png)</center>

## 添加原因

好了，这基本上是开始一个包裹项目的教程，但是原因在哪里呢？让我们从向项目添加`bs-platform`依赖项开始:

```
$ yarn add bs-platform 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个`bsconfig.json`文件来告诉 BuckleScript 该做什么:

```
{  "name":  "reason-parcel",  "refmt":  3,  "sources":  "src",  "dependencies":  [],  "package-specs":  {  "module":  "commonjs",  "in-source":  true  },  "suffix":  ".bs.js"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们是告诉 BuckleScript 把 Reason 文件编译成 JavaScript 放在同一个目录下，用`.bs.js`，所以我们的 *src/index.re* 会编译成 *src/index.bs.js* 。这样，package 将能够在本地处理它们。

让我们创建这个 *src/index.re* 文件(通过将 *src/index.js* 文件重命名为 *src/index.re* )并将其内容设置为:

```
Js.log("Hello from Reason!"); 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要用主文件的新路径更新我们的 *public/index.html* ，这个路径现在写成了 Reason:

```
<script src="../src/index.re"></script> 
```

Enter fullscreen mode Exit fullscreen mode

让我们现在开始应用程序:

<center>![Hello from Reason!](img/fb7ba5f3872a1c28f529e7a0b0b12eed.png)</center>

在那里！只有一个命令来运行项目，包括 JavaScript transpilation 的原因！😁

## 添加原因-反应

现在的最后一步:添加 Reason-React 到项目中，这样你就可以用 Reason 编写你的组件了。首先我们需要添加依赖项:

```
yarn add reason-react 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要更新 *bsconfig.json* 来告诉 BuckleScript 我们将使用 Reason-React 和 JSX:

```
{  "name":  "reason-parcel",  "refmt":  3,  "sources":  "src",  "bs-dependencies":  ["reason-react"],  "reason":  {  "react-jsx":  2  },  "dependencies":  [],  "package-specs":  {  "module":  "commonjs",  "in-source":  true  },  "suffix":  ".bs.js"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在 *src/Greeting.re* :
中创建一个简单的组件

```
let component = ReasonReact.statelessComponent("Greeting");

let make = (~name, _children) => {
  ...component,
  render: (_self) =>
    <div>
      (ReasonReact.stringToElement("Hello "))
      <strong> (ReasonReact.stringToElement(name)) </strong>
      (ReasonReact.stringToElement("!"))
    </div>
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们用在 *src/index.re* :

```
ReactDOMRe.renderToElementWithId(<Greeting name="Sebastien" />, "root"); 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们添加一个`root` div 到我们的 *public/index.html* 来呈现我们的组件:

```
<html>
<body>
  <div id="root"></div>
  <script src="../src/index.re"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

哇就是它！简单对吗？下面是您应该在浏览器中看到的内容:

<center>![Hello from Reason!](img/732a298e850317071fc2c741d5d2a51d.png)</center>

现在，您已经准备好使用 React 来构建强大且可维护的组件，同时享受理性的可能性。当然，这不是唯一的方法，但是我喜欢 Parcel 的优雅。

我很想知道你是否看到了这个工作流程的改进。也许使用包裹打包器或者 T2 插件可以让它更干净？😉

一些资源:

*   [Reason](https://reasonml.github.io) 、 [Reason-React](https://reasonml.github.io/reason-react/) 、 [Reason-Scripts](https://github.com/reasonml-community/reason-scripts) 网站将向你展示如何以“官方”的方式创建 Reason-React app。
*   [包裹](https://parceljs.org)的网站有[一个展示如何用它](https://parceljs.org/recipes.html)反应的页面，并推荐了一篇[的详细文章](http://blog.jakoblind.no/react-parcel/)。
*   您还可以在包裹网站上找到[文档，以便使用它](https://parceljs.org/transforms.html#reasonml/bucklescript)。

*这篇文章最初发表在我的博客上。*