# ES6 模块入门

> 原文：<https://dev.to/anku/getting-started-with-es6-modules-7bk>

一个模块只是一个 javascript 文件，带有一堆变量或函数，可以被导入并在其他 javascript 文件中使用。有了 ES6，使用模块变得有趣了。让我告诉你怎么做。

在 ES6 模块之前，我们曾经在我们的`html`文件中有`script`标签来导入和使用一个库。让我们看看下面的例子:

```
<!-- index.html -->
<html>
  <head>
    Before ES6 Modules
  <head>
  <body>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.min.js"></script>
  <script src="./app.js"></script>
  </body>
</html> 
```

```
// app.js

const numbers = [1, 2, 2, 3, 4, 3];

const uniqNumbers = _.uniq(numbers); // _.uniq is coming from lodash

console.log(uniqNumbers); // [1, 2, 3, 4] 
```

上面，我们将`lodash`包含在`index.html`文件中。我们使用`lodash`提供的`uniq`函数来寻找唯一的数字。这里，`_`没有明确定义，只看`app.js`文件的人不知道`_`来自哪里。那么，有没有更好的办法呢？嗯，是啊！

### NPM -节点包管理器

NPM 是所有 javascript 库的托管地。如果你想使用它，你必须从这里安装它。您必须在系统中安装 [NodeJS](https://nodejs.org/en/) 才能使用它。NPM 和 NodeJS 一起来的。

让我们创建一个名为`es6Modules`的目录，并将`cd`放入其中。在目录中创建一个`index.html`和一个`app.js`文件。现在，在你的终端的这个目录下运行`npm init`命令。它会问一些关于你的项目的问题。回答完所有与项目相关的问题后。它将生成一个`package.json`文件，其中包含您的项目的详细信息以及您从 npm 安装的库。

目录结构如下所示:

```
├── app.js
├── index.html
└── package.json 
```

要从 NPM 安装一个软件包，你只需要在你的终端键入命令`npm install <package-name>`。
安装`lodash`，则为`npm install lodash`。运行这个命令后，您将看到一个名为`node_modules`的文件夹出现在您的目录中。它将包含您已经安装的所有库或包。另外，在您的`package.json`文件中，`lodash`将被添加到 dependencies 部分。

现在，那个`lodash`安装好了。让我们使用它。

```
// app.js
import _ from 'lodash'; // this is how you import an es6 module

const numbers = [1, 2, 2, 3, 4, 3];

const uniqNumbers = _.uniq(numbers);

console.log(uniqNumbers); 
```

现在，让我们引用`index.html`中的`app.js`文件来运行它。

```
<!-- index.html -->
<html>
  <head>
    ES6 Modules
  <head>
  <body>
  <script src="./app.js"></script>
  </body>
</html> 
```

现在，在浏览器中打开`index.html`文件并检查你的控制台。您看到的不是唯一的数字，而是如下所示的错误:

[![](../Images/0fbf842b901705298e3481aaddbc7a67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1yq2HHJv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pht0cxvj8dzirz2r4huf.png) 

<figure>

<figcaption>浏览器不支持 ES6 模块</figcaption>

</figure>

嗯，原来浏览器还不支持 ES6 模块。但这并不意味着我们不能使用它。我们可以使用像 webpack 或 parcel 这样的包捆绑器将我们的 ES6 模块语法编译成我们的浏览器能够理解的东西。

对于本教程，我将使用 webpack。要安装 webpack，请执行下面给出的命令:

`npm install webpack webpack-cli --save-dev`

现在，在根目录下创建一个`webpack.config.js`文件来配置 webpack。

```
// webpack.config.js

const path = require('path');

module.exports = {
  entry: './app.js', // entry point of our project
  output: {
    filename: 'bundle.js', // The name of the output file
    path: path.resolve(__dirname, 'dist') // The folder where output file will be generated
  }
}; 
```

现在，我们需要向我们的`package.json`文件添加一个脚本来运行 webpack 并捆绑我们的代码。打开`package.json`文件和如下所示的脚本:

```
//  package.json  "scripts":  {  "build":  "webpack --config webpack.config.js"  } 
```

这里`--config`用来指定我们的配置文件`webpack.config.js`。

现在，通过在终端中运行`npm run build`来运行构建脚本。运行这个脚本后，您会看到 webpack 在`dist`文件夹中生成了一个名为`bundle.js`的文件。
这个文件包含了我们在`app.js`中编写的代码，但是以我们的浏览器能够理解的方式。所以，我们现在不得不在`index.html`中引用这个文件

```
<!-- index.html -->
<html>
  <head>
    ES6 Modules
  <head>
  <body>
    <script src="./dist/bundle.js"></script>
  </body>
</html> 
```

如果您在浏览器中打开`index.html`文件，您将看到如下唯一的数字:

[![](../Images/5ca8800909f4187d53230ca5dbb97d93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v-7YBc0F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lh4k8j3l4td6f17lds67.png) 

<figure>

<figcaption>由 webpack 捆绑的代码在浏览器中运行良好</figcaption>

</figure>

这都是为了这个帖子。在下一篇文章中，我将讨论如何编写和使用我们自己的 ES6 模块。在此之前，请阅读 [webpack](https://webpack.js.org/guides/) 的文档，以了解更多关于它所做的令人敬畏的事情。