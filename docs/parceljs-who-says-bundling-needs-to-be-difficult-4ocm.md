# Parcel.js:谁说捆绑需要困难？

> 原文：<https://dev.to/iam_timsmith/parceljs-who-says-bundling-needs-to-be-difficult-4ocm>

如果你在使用 React，你可能会遇到一些构建工具，比如 [Webpack](https://webpack.js.org/) 、 [Grunt](https://gruntjs.com/) 或 [Gulp](https://gulpjs.com/) 。

这些工具非常酷，但同时也可能成为进入的障碍，因为使用它们需要配置。有一种更简单的方法来捆绑和构建我们的项目: [Parcel.js](https://parceljs.org/) 。

我将向您展示如何使用 Parcel 建立一个项目来构建 React 应用程序。它只需要大约 5 分钟就可以启动并运行(如果你已经做了几次就更少了)！

### 什么是 Parcel.js？

根据 Parcel.js 网站，它是:

> ...一个 web 应用捆绑器，以其开发者体验而与众不同。它利用多核处理提供极快的性能，并且不需要任何配置。

#### 为什么这个对我们有用？

没有什么比试图开始一个项目并在设置构建工具时迷失在众所周知的杂草中更糟糕的了。Parcel.js 消除了对配置的需求，这意味着我们可以跳过这一部分，直接进入我们的项目。有什么不喜欢的？

它还利用多核处理和缓存来帮助加快构建速度。在我们可以查看我们的项目之前，不再需要 30 秒的等待。准备好开始并看看设置我们的项目有多简单了吗？太好了！

### 使用 Parcel.js 设置我们的项目

#### 1。创建目录并输入

任何项目的第一步都是创建存放文件的目录。为此，导航到将包含我们的项目文件夹的文件夹，并在我们的终端中使用下面的代码行。

```
mkdir parcel-setup && $_ 
```

Enter fullscreen mode Exit fullscreen mode

#### 2。初始化 NPM 或纱线

现在我们有了项目目录，我们应该初始化 NPM 或 Yarn 来创建一个 package.json 文件。我将提供两者的代码，但你可以只遵循你喜欢的一个。要初始化我们的项目，使用下面的代码:

```
npm init -y

or 

yarn init -y 
```

Enter fullscreen mode Exit fullscreen mode

init 命令初始化项目，`-y`标志采用默认设置。我们也可以不使用`-y`标志，手动设置 package.json 文件。

#### 3。初始化 Git repo 并添加。gitignore

在我们的项目中使用 git 总是一个好主意。Git 是一个版本控制工具，它允许我们获取代码的“快照”并将其保存在本地或类似于 [Github](https://github.com/) 的网站上。要将 git 添加到我们的项目中，我们需要用下面的命令初始化它:

```
git init 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们添加了 git，我们应该添加一个. gitignore 文件。这个文件的目的是告诉我们的计算机在提交或快照时忽略列出的文件或目录。下面的代码行将创建文件并打开它供我们编辑。

```
touch .gitignore && open $_ 
```

Enter fullscreen mode Exit fullscreen mode

打开文件后，我们需要添加不想添加的文件和文件夹。在这种情况下，它只是我们的 node_modules 文件夹，这是我们的依赖项存储的地方。我们的。gitignore 文件应该是这样的:

```
node_modules 
```

Enter fullscreen mode Exit fullscreen mode

#### 4。创建一个 index.html 文件

我们差不多完成了一半。很快，对吧？

为了创建我们的 index.html 文件，我们可以回到终端并使用下面的代码行:

```
touch index.html 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在文本编辑器中打开这个文件。我们将用下面的代码填充它。(提示:如果你在 Emmet 上使用文本编辑器，你可以输入`html:5`并点击 tab 键。它应该为您完成大部分工作！)

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Parcel Setup
</head>

<body>
  <div id="app"></div>
  <script src="./app.js"></script>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

#### 5。安装依赖项

设置项目的下一步是安装项目的依赖项。像以前一样，我提供了 NPM 和纱的代码，所以只要使用你在你的项目中使用。

```
npm install react react-dom parcel babel-preset-env babel-preset-react --save-dev

or

yarn add react react-dom parcel babel-preset-env babel-preset-react 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们的软件包安装完成，我们就可以完成我们的项目设置！

#### 6。添加 app.js

为了实际创建我们的应用程序，我们将把它放在一个 app.js 文件中，所以你能猜到接下来是什么吗？没错。我们需要创建并打开文件。

```
touch app.js && open $_ 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 app.js 文件中，我们将创建一个 React 组件，并使用 React DOM 来呈现它。如果您不确定如何创建一个 React 组件，您应该阅读[这篇文章来快速了解一下](https://www.iamtimsmith.com/blog/how-to-create-a-component)。下面是我们在 app.js 文件中创建 React 应用程序所需的代码:

```
import React from 'react';
import ReactDOM from 'react-dom';

class App extends React.Component {
    render() {
        return (
            <div>
                <h1>Hello World!</h1>
            </div>
        )
    }
}

ReactDOM.render(<App />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们文件的顶部是导入这个文件所需的依赖项。我们在步骤 5 中安装了它们。接下来，我们将创建我们的应用程序组件，它将返回一个带有文本“Hello World！”的 H1 标签。最后一行显示了我们在第 4 步的 HTML 文档中创建的#app 元素中的应用程序。

#### 7。创建一个. babelrc 文件，告诉它如何编译 JavaScript

我们快完成了！由于 React 使用 ES6+ JavaScript，我们需要设置一个. babelrc 文件来告诉它如何编译我们的代码。Babel 基本上采用了最现代的语法(ES6、ES7 等)，并将其转化为所有浏览器都可以阅读的代码，无论它们是否支持 ES6+。很酷，对吧？让我们创造我们的。babelrc 文件！

```
touch .babelrc && open $_ 
```

Enter fullscreen mode Exit fullscreen mode

在我们的文件中，我们将放入以下代码。这是一个非常基本的设置，但它将完成我们今天项目的工作。

```
{
  "presets": ["env", "react"]
} 
```

Enter fullscreen mode Exit fullscreen mode

厉害！再走一步，我们就完成了！

#### 8。向我们的 package.json 文件添加脚本

运行应用程序之前的最后一步是向 package.json 文件添加一些脚本命令。让我们打开它。

```
open package.json 
```

Enter fullscreen mode Exit fullscreen mode

应该是这样的:

```
{
  "name": "parcel-setup",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "babel-preset-env": "^1.7.0",
    "babel-preset-react": "^6.24.1",
    "parcel": "^1.9.7",
    "react": "^16.4.2",
    "react-dom": "^16.4.2"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 快速注意:您的依赖项的版本号可能与我的不同。在撰写本文时，这些是最新的版本。

我们将添加以下代码。它说当我们键入`npm run start`或`yarn start`时，它应该使用 Parcel 来构建我们的应用程序并提供 index.html 文件。

```
"scripts": {
    "start": "parcel index.html"
}, 
```

Enter fullscreen mode Exit fullscreen mode

我们完整的 package.json 文件应该是这样的:

```
{
  "name": "parcel-setup",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "start": "parcel index.html"
  },
  "dependencies": {
    "babel-preset-env": "^1.7.0",
    "babel-preset-react": "^6.24.1",
    "parcel": "^1.9.7",
    "react": "^16.4.2",
    "react-dom": "^16.4.2"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 全部设置完毕

我们的包裹设置现在完成了！要构建我们的应用程序，回到您的终端并运行下面的命令:

```
npm run start

or

yarn start 
```

Enter fullscreen mode Exit fullscreen mode

你的终端现在显示`Server running at http://localhost:1234`。让我们打开浏览器，进入 [http://localhost:1234](http://localhost:1234) 查看我们的项目。

### 结论

我们现在已经看到了带着包裹开始工作是多么容易。虽然 Webpack 等工具为企业级应用程序提供了更多的定制，但 Parcel 非常适合小型或新的应用程序以及原型开发。我强烈建议你下次开始一个新项目的时候去拿包裹。