# 使用 webpack、babel 和 eslint 建立一个 ES6 javascript 项目

> 原文：<https://dev.to/brijesh/setup-a-es6-javascript-project-using-webpack-babel-and-eslint-1ek3>

目录-

1.  设置
2.  式样
3.  林挺

每当有人学习一门新的编程语言时，有 99%的可能性他们的第一个程序会是一个 **Hello World** 程序。在这个众所周知的程序中，他们应该在屏幕/控制台上打印`Hello World`。根据语言的不同，为了打印这个 **Hello World** ，它可以从一行程序到多行程序不等。

在 Javascript 中，在过去(4-5 年前)，人们只需用这个内容创建一个 HTML 文件，并在浏览器中打开它，就可以在浏览器窗口(以及浏览器控制台)中看到打印的 **Hello World** 。

```
<!DOCTYPE html>
<html>
  <head>
    Hello World
  </head>
  <body>
    <p>Hello World</p>
    <script>
      console.log('Hello World');
    </script>
  </body>
</script> 
```

但是随着 javascript 生态系统的成熟，这个过程变得有点复杂(越来越复杂)。在本教程中，您将了解如何设置这种类型的项目。

### 假设

*   你知道 Javascript(最好也知道一些 es6)。
*   你的系统上已经安装了`nodejs`和`npm`([教程](https://docs.npmjs.com/getting-started/installing-node))。

完整的代码可以在 https://github.com/brijeshb42/hello-world-tutorial 获得。

## 第一部分

打开您的终端应用程序或命令提示符并`cd`到您想要创建这个项目的目录。让我们假设在您的磁盘上的某个目录中，项目文件夹名为`hello-world`。现在输入这些命令-

1.  `cd hello-world`
2.  `npm init --y`

这将在`hello-world`目录下创建一个`package.json`文件。`package.json`是项目中的一个文件，被`nodejs`和`npm`用来跟踪已安装的包和项目的元数据。你的`package.json`可能看起来像这样-

```
{  "name":  "hello-world",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "keywords":  [],  "author":  "",  "license":  "ISC"  } 
```

现在添加 webpack 和开发服务器-

```
npm install webpack@3.11.1 webpack-dev-server --save-dev 
```

在写这篇文章的时候，安装的 webpack 版本是`3.11.1`。

> 编辑:由于本教程已经发布，`webpack`版本 4 已经发布，其配置略有不同，可能会破坏本教程的设置，因此添加了该版本。

在项目文件夹中创建一个`src`目录，然后在其中创建`index.js`文件。

1.  `mkdir src`
2.  `echo "console.log('Hello world');" > src/index.js`

这是我们的 hello world 程序，运行时将在浏览器控制台中打印`Hello world`。

此时，您可以从编写一个 webpack 配置文件开始，以捆绑您的文件供浏览器加载。

在您的项目文件夹中创建一个包含以下内容的`webpack.config.js`文件。webpack 使用这个文件来读取您的配置并相应地构建项目。

```
const path = require('path');

module.exports = {
  entry: {
    bundle: './src/index.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
   }
}; 
```

现在，您可以为 webpack 运行这个命令来加载条目文件，并在项目目录的根目录下的`dist`目录中创建一个捆绑的 js 文件。

```
./node_modules/.bin/webpack 
```

这是一个构建命令，它将捆绑所有依赖项并创建一个在 webpack 配置文件的`output`选项中指定的`bundle.js`文件。运行这个命令后，您可以在`dist`中看到一个`bundle.js`文件。您还不能加载这个 js 文件，因为您首先必须有一个 html 文件。浏览器将加载该文件，该文件又将加载 js 文件。
您可以在`dist`中手动创建一个包含此内容的`index.html`文件。

```
<script src="./bundle.js"></script> 
```

这是加载和运行我们的捆绑 js 所需的最小 html 量。现在你可以双击这个 html 文件，在浏览器中打开它。您可以使用`CMD/CTRL` `+` `SHIFT` `+` `I`打开浏览器控制台查看输出。让我们来看看一个更好的方法，通过它你不必写 html 文件。

`npm install html-webpack-plugin --save-dev`

这是一个 webpack 插件，它自动生成`dist`中的`index.html`文件，并适当引用所有生成的 javascript 文件。要使用这个插件，请用这个更新你的`webpack.config.js`

```
 const path = require('path');
+ const HtmlWebpackPlugin = require('html-webpack-plugin'); 
 module.exports = {
   entry: {
    bundle: './src/index.js'
  },
   output: {
     path: path.resolve(__dirname, 'dist'),
      filename: 'bundle.js'
-   } +  },
+  plugins: [
+    new HtmlWebpackPlugin()
+  ]
 }; 
```

在这之后，您可以再次运行构建命令-

```
./node_modules/.bin/webpack 
```

这将在`dist`目录下创建一个额外的`index.html`文件，并带有适当的脚本标签来包含`bundle.js`。这现在可以直接在浏览器中打开，它会像以前一样工作，除了你不必自己创建它。

为了使构建命令更短，让我们在`package.json`中创建一个别名，这样您只需键入`npm run build`来捆绑您的文件。更新你的`package.json`—

```
 {
   "name": "hello-world",
   "version": "1.0.0",
    "description": "",
   "main": "index.js",
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "keywords": [],
   "author": "",
   "license": "ISC",
   "devDependencies": {
     "html-webpack-plugin": "^2.30.1",
     "webpack": "^3.11.0",
     "webpack-dev-server": "^2.11.1"
-  } +  },
+  "scripts": {
+    "build": "webpack"
+  }
 } 
```

到目前为止，webpack 将文件打包并退出。当您只想捆绑并部署到您的本地或远程服务器时，这很好。但是在开发过程中，这很快就会变得令人沮丧。为了克服这种挫败感，您将使用`webpack-dev-server`,它不断地观察您的文件的变化，并立即刷新浏览器中的页面。它还在`dist`中启动了一个开发服务器，所以 html 文件是从一个服务器而不是一个文件系统中加载的(以防万一你在 js 中使用 ajax，当从文件系统中打开时它不工作)。安装时使用-

`npm install webpack-dev-server`

这将启动以`dist`作为基本目录的开发服务器。默认的网址是`http://localhost:8080`。在浏览器中打开这个 url 将会在控制台中加载`index.html`文件和日志`Hello World`。现在，如果您在`src/index.js`内将控制台日志从`Hello World`更新到`Hi World`，`webpack-dev-server`将自动重新加载浏览器，您将能够看到新的输出。

`./node_modules/.bin/webpack-dev-server --content-base dist`

让我们将它也作为别名添加到`package.json` -

```
 {
   "name": "hello-world",
   "version": "1.0.0",
   "description": "",
   "main": "index.js",
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1"
   },
   "keywords": [],
   "author": "",
   "license": "ISC",
   "devDependencies": {
     "html-webpack-plugin": "^2.30.1",
     "webpack": "^3.11.0",
     "webpack-dev-server": "^2.11.1"
   },
   "scripts": {
     "build": "webpack",
+    "dev": "webpack-dev-server --content-base dist"
   }
 } 
```

现在运行`npm run dev`将启动`webpack-dev-server`并自动重新加载更改。

此时，您还不能在 js 代码中使用 es6 语法。让我们加上这种支持。这将通过使用`babel`来完成。要在构建过程中添加 babel 支持，让我们首先安装它。`babel-loader`将要求安装`babel-core`。为了支持 es6/7/8/*语法，您将添加`babel-preset-env`。在你的终端的项目文件夹中运行这个-

`npm install babel-core babel-loader babel-preset-env --save-dev`

首先在项目目录中创建一个`.babelrc`文件，以便 babel 可以加载它的配置。把这个加到文件里-

```
{  "presets":  [[  "env",  {  "targets":  {  "browsers":  ["Chrome >= 55"]  }  }  ]]  } 
```

这种配置是特意使用的，以便您可以看到`dist`目录中捆绑的 js 文件，并了解您的 es6 代码是如何被编译的。随着浏览器开始支持越来越多的 es6 特性，`babel`不再盲目地传输所有代码，而是智能地识别哪些特性是本机支持的，并且不传输那些部分。这减小了整体束尺寸。

代替上述配置的最简单配置(如果您不关心浏览器版本)应该是-

```
{  "presets":  ["env"]  } 
```

现在让我们先指导`webpack`使用 babel 来传输`js`文件。

```
 const path = require('path');
+ const HtmlWebpackPlugin = require('html-webpack-plugin'); 
 module.exports = {
   entry: `{
    bundle: './src/index.js'
  },
   output: {
     path: path.resolve(__dirname, 'dist'),
      filename: 'bundle.js'
    },
   plugins: [
     new HtmlWebpackPlugin()
-  ] +  ],
+  module: {
+    rules: [{
+      test: /\.js$/,
+     exclude: /node_modules/,
+     use: 'babel-loader'
+   }]
+ }
 }; 
```

创建一个新文件`src/message.js`并添加这个-

```
export default "Hello World"; 
```

现在修改`src/index.js`来使用最简单的 es6 特性导入-

```
import message from './message';

console.log(message); 
```

在上面的代码中，使用了 es6 模块语法。现在运行`npm run dev`将创建一个更新的包(尽管输出是相同的),您可以在浏览器控制台中测试它。

这总结了本教程的第一部分，其中您已经设置了最简单(非常简单)的 javascript 项目，使用 webpack 与 babel integration 进行捆绑，将 es6 转换为 es5。

* * *

## 第二部分

现在，让我们进入教程的第二部分，我们将设置 webpack 来导入`css`文件。这样，您可以直接在 javascript 文件中包含样式。

首先，让我们修改`src/index.js`以在页面上显示一些文本，而不仅仅是登录到控制台。

```
 import message from './message';

-console.log(message); +const paragraph = document.createElement('p');
+paragraph.innerHTML = message;
+
+document.body.prepend(paragraph); 
```

这将使用导入的`message`作为 html 创建一个`p`标签，并将其添加到页面中。

现在，让我们使用 css 样式化这个`p`标签。这就需要`css-loader`和`style-loader`。安装时使用-

`npm install css-loader style-loader --save-dev`

为了支持`css`文件导入，让我们用一个新规则来更新我们的`webpack.config.js`,该规则测试导入的文件是否有`css`扩展名，并使用`style-loader`和`css-loader`来解析它

```
 const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');

  module.exports = {
   entry: {
     bundle: './src/index.js '
   },
   output: {
     path: path.resolve(__dirname, 'dist'),
      filename: 'bundle.js'
    },
   plugins: [
     new HtmlWebpackPlugin()
   ],
   module: {
     rules: [{ 
        test: /\.js$/,
        exclude: /node_modules/,
       use: 'babel-loader'
+    }, {
+      test: /\.css$/,
+      exclude: /node_modules/,
+      use: [
+        {loader: 'style-loader'},
+        {loader: 'css-loader'}
+       ]
+     }]
   }
 }; 
```

现在创建一个 css 文件`src/index.css`并对`p`标签进行样式化

```
p {
   color: red;
} 
```

在`src/index.css`中导入这个 css 文件

```
 import message from './message';
+import './index.css'; 
 const paragraph = document.createElement('p');
 paragraph.innerHTML = message;

 document.body.prepend(paragraph); 
```

现在，使用`npm run dev`重启开发服务器。您将能够看到页面现在以红色显示`Hello World`。如果您在`index.css`中将颜色从红色更改为蓝色，页面将重新加载，新的样式将可见。要查看新的样式而不重新加载实际页面，请修改`package.json`中的 dev server 命令

```
 {
  "name": "hello-world",
  "version": "1.0.0", 
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "webpack",
-    "dev": "webpack-dev-server --content-base dist" +    "dev": "webpack-dev-server --content-base dist --hot"
  },
  "keywords": [],
  "author": "" ,
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-env": "^1.6.1",
    "css-loader": "^0.28.9",
    "html-webpack-plugin": "^2.30.1",
    "style-loader": "^0.20.2",
    "webpack": "^3.11.0",
    "webpack-de v-server": "^2.11.1"
  }
 } 
```

这支持 webpack 中的热模块替换，它显示代码中的新变化(在`css`或`js`或任何文件中，只要 webpack 知道如何加载它),而无需完全重新加载页面。用`npm run dev`重启服务器，尝试改变 css 中`p`的颜色。您会注意到页面中的颜色发生了变化，而实际上并没有重新加载页面。

如果您尝试在`dist`目录中运行构建命令`npm run build`，您会注意到没有构建任何 css 文件。这是因为 webpack 将 javascript 包中的样式作为字符串添加，并通过创建`style`标签在页面中应用这些样式。当你在发展的时候，这是好的。但是在部署过程中，将 css 文件包含在`head`标签中总是一个好的做法，这样在加载 javascript 时页面外观不会受到影响。为了解决这个问题，我们将使用`extract-text-webpack-plugin`，它在构建过程中将所有导入的 css 提取到自己的文件中。在此之前，我们先设置一下 webpack，了解一下`development`和`production`模式。

```
 const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin');

+ const env = process.env.NODE_ENV || 'development';
+ const isDev = env === 'development';
+ const isProd = env === 'production'; 
  module.exports = {
    entry: {
      bundle: './src/index.js'
    },
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'bundle.js'
    },
    plugins: [
      new HtmlWebpackPlugin()
    ],
    module: {
      rules: [{
        test: /\.js$/,
        exclude: /node_modules/,
       use: 'babel-loader'
      }, {
        test: /\.css$/,
        exclude: /node_modules/,
        use: [
          {loader: 'style-loader'},
          {loader: 'css-loader'}
        ]
      }]
    }
  }; 
```

并修改`package.json`以在生产模式下运行构建命令，在开发模式下运行开发服务器。

```
 { 
   "name": "hello-world",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
-    "build": "webpack",
-    "dev": "webpack-dev-server --content-base dist --hot" +    "build": "NODE_ENV=production webpack",
+    "dev": "NODE_ENV=development webpack-dev-server --content-base dist --hot"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-env": "^1.6.1",
    "css-loader": "^0.28.9",
    "extract-text-webpack-plugin": "^3.0.2",
    "html-webpack-plugin": "^2.30.1",
    "style-loader": "^0.20.2",
    "webpack": "^3.11.0",
    "webpack-dev-server": "^2.11.1"
  }
 } 
```

现在使用以下命令安装`extract-text-webpack-plugin`

`npm install extract-text-webpack-plugin --save-dev`

并更新`webpack.config.js`

```
 const path = require('path');
 const HtmlWebpackPlugin = require('html-webpack-plugin');
+const ExtractTextPlugin = require('extract-text-webpack-plugin'); 
 const env = process.env.NODE_ENV || 'development';
 const isDev = env === 'development';
 const isProd = env === 'production';

+const extractCss = new ExtractTextPlugin({
+  filename: 'index.css',
+  disable: isDev
+}); 
 module.exports = {
   entry: {
     bundle: './src/index.js'
   },
    output: {
     path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
   },
   plugins: [
-    new HtmlWebpackPlugin() +    new HtmlWebpackPlugin(),
+    extractCss
   ],
   module: {
     rules: [{
       test: /\.js$/,
       exclude: /node_modules/,
       use: 'babel-loader'
     }, {
       test: /\.css$/,
       exclude: /node_modules/,
-      use: [
-         {loader: 'style-loader'},
-         {loader: 'css-loader'}
-      ] +      use: extractCss.extract({
+        use:[
+          {loader: 'css-loader'}
+        ],
+        fallback: 'style-loader'
+     })
     }]
   }
 }; 
```

这将禁用开发模式中的`extractCss`，在这种情况下，`style`标签用于应用 css。在生产模式下，`extractCss`插件将`js`包中的所有`css`提取到各自的文件中，这些文件是根据声明`extractCss`时使用的`filename`的值命名的。

现在运行`npm run build`会在`dist` - `bundle.js`、`index.css`、`index.html`中创建 3 个文件。

#### 更新-添加 scss 支持

让我们给 webpack 配置文件添加`scss`解析支持。为此，你需要`sass-loader`，而 T1 又需要`node-sass`。安装这些，使用-

`npm install node-sass sass-loader --save-dev`

现在，更新`webpack.config.js`以便 webpack 知道如何处理导入的 scss 文件-

```
 const path = require('path');
 const HtmlWebpackPlugin = require('html-webpack-plugin');
 const ExtractTextPlugin = require('extract-text-webpack-plugin');

 const env = process.env.NODE_ENV || 'development';
 const isDev = env === 'development';
 const isProd = env === 'production';

-const extractCss = new ExtractTextPlugin({ +const extractScss = new ExtractTextPlugin({
   filename: 'index.css',
   disable: isDev
 });

 module.exports = {
   entry: {
     bundle: './src/index.js'
   },
   output: {
     path: path.resolve(__dirname, 'dist'),
     filename: 'bundle.js'
   },
   plugins: [
     new HtmlWebpackPlugin(),
-    extractCss +    extractScss
   ],
   module: {
     rules: [{
       test: /\.js$/,
       exclude: /node_modules/,
       use: 'babel-loader'
     }, {
-      test: /\.css$/, +      test: /(\.css|\.scss)$/,
       exclude: /node_modules/,
-      use: extractCss.extract({ +      use: extractScss.extract({
         use:[
-          {loader: 'css-loader'} +          {loader: 'css-loader'},
+          {loader: 'sass-loader'}
         ],
         fallback: 'style-loader'
       })
     }]
   }
 }; 
```

现在为了测试这一点，将`index.css`重命名为`index.scss`,并用基本的 scss 嵌套更新其内容

```
body {
  p {
    color: red;
  }
} 
```

在`index.js`中更新导入-

```
 import message from './message';
-import './index.css'; +import './index.scss'; 
 const paragraph = document.createElement('p');
 paragraph.innerHTML = message;

 document.body.prepend(paragraph); 
```

通过运行`npm run dev`并在浏览器中打开 url 进行测试。

这部分总结了在`js`中导入`css`和`scss`文件的用法。

* * *

## 第三部分

随着项目代码库规模的增加，如果在早期没有注意到，维护一个严格的编码指南会变得很困难。此外，随着越来越多的人开始为单个项目做贡献，他们可能会带来自己的编码风格，这可能会导致各种文件中的代码看起来不同，新开发人员很难决定遵循哪种风格。这个问题可以通过使用棉绒来解决。它们有助于遵循编写代码的单一严格准则。javascript 中的 Linters 显示了许多有用的信息，如未使用的变量、缺少分号(这在某些项目中可能不是问题)、代码超过最大允许长度等。让我们更新我们的项目，使用`eslint`在没有遵循特定准则时抛出错误。为此，我们需要`eslint`和`eslint-loader`。安装它们使用-

`npm install eslint eslint-loader --save-dev`

现在更新`webpack.config.js`，通知 webpack 在通过`babel-loader`之前使用`eslint-loader`

```
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

const env = process.env.NODE_ENV || 'development';
const isDev = env === 'development';
const isProd = env === 'production';

const extractScss = new ExtractTextPlugin({
  filename: 'index.css',
  disable: isDev
});

 module.exports = {
   entry: {
     bundle: './src/index.js'
   },
   output: {
     path: path.resolve(__dirname, 'dist'),
     filename: 'bundle.js'
   },
   plugins: [
     new HtmlWebpackPlugin(),
     extractScss
   ],
   module: {
     rules: [{
+      enforce: 'pre',
+      test: /\.js$/,
+      exclude: /node_modules/,
+      use: 'eslint-loader'
+    }, {
       test: /\.js$/,
       exclude: /node_modules/,
       use: 'babel-loader'
     }, {
       test: /(\.css|\.scss)$/,
       exclude: /node_modules/,
       use: extractScss.extract({
         use:[
           {loader: 'css-loader'},
           {loader: 'sass-loader'}
         ],
         fallback: 'style-loader'
       })
     }]
   }
 }; 
```

在项目的顶层创建一个新文件`.eslintrc`(在`package.json`旁边)。在这个文件中，您可以定义自己的自定义规则和解析器，以便`eslint`遵循。

```
{  "parserOptions":  {  "ecmaVersion":  6,  "sourceType":  "module"  },  "extends":  "eslint:recommended"  } 
```

`ecmaVersion`允许 eslint 识别 ES6 特性，`sourceType: module`允许使用`import`和`export`关键字。默认情况下，没有为`eslint`设置规则。所以`"extends": "eslint:recommended"`告诉`eslint`使用默认的推荐规则。

此时，可以运行`npm run dev`。在控制台中，您会看到有两个相同类型的错误-

```
4:19  error  'document' is not defined  no-undef
7:1   error  'document' is not defined  no-undef 
```

这表明变量`document`还没有在任何地方定义(`no-undef`)，但仍在使用。这可以通过两种方式解决。要解决这个问题，你需要使用`.eslintrc`中的`globals`键。更新你的`.eslintrc`—

```
 {
   "parserOptions": {
     "ecmaVersion": 6,
     "sourceType": "module"
   },
-  "extends": "eslint:recommended" +  "extends": "eslint:recommended",
+. "globals": {
      "document": true
    }
 } 
```

这告诉`eslint`变量`document`是全局的，将由 JS 环境(在本例中是 browser)提供。现在你可以运行`npm run dev`没有任何错误。您还可以向`package.json`添加一个林挺命令，独立于 webpack 查看 lint 错误。更新`package.json`

```
{
  "name": "hello-world",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "NODE_ENV=production webpack",
-   "dev": "NODE_ENV=development webpack-dev-server --content-base dist --hot" +   "dev": "NODE_ENV=development webpack-dev-server --content-base dist --hot",
+   "lint": "eslint ./src --ext .js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-preset-env": "^1.6.1",
    "css-loader": "^0.28.9",
    "eslint": "^4.18.1",
    "eslint-loader": "^2.0.0",
    "extract-text-webpack-plugin": "^3.0.2",
    "html-webpack-plugin": "^2.30.1",
    "node-sass": "^4.7.2",
    "sass-loader": "^6.0.6",
    "style-loader": "^0.20.2",
    "webpack": "^3.11.0",
    "webpack-dev-server": "^2.11.1"
  }
} 
```

现在你可以在你的控制台中运行`npm run lint`并检查任何林挺错误，不管你是否捆绑了这个项目。这也可以在 git 预提交钩子中使用，以便在`eslint`抛出任何错误时不允许提交。`eslint ./src --ext .js`告诉`eslint`检查`src`目录下扩展名为`js`的所有文件中的错误。您还可以在该命令中添加一个可选的`--fix`选项，它会自动尝试修复错误，这样您就不必这么做了。

您也可以根据需要在`.eslintrc`文件中添加自己的规则。`eslint:recommended`选项不允许您在代码中使用`console.log`(推荐的方式是使用日志模块)。您可以添加一个规则来告诉`eslint`在`console.log`语句中显示警告而不是错误。更新`.eslintrc`文件-

```
 {
   "parserOptions": {
     "ecmaVersion": 6,
     "sourceType": "module"
   },
   "extends": "eslint:recommended",
   "globals": {
-    "document": true +    "document": true,
+    "console": true -  } +  },
+  "rules": {
+    "no-console": 1
+  }
 } 
```

`"no-console": 1`告诉`eslint`显示警告而不是错误。其他值有`0`(关闭此规则的`eslint`)和`2`(如果违反此规则，抛出错误)。有一些很多公司使用的标准 javascript 风格指南(而不是默认的`eslint:recommended`)。其中之一是 airbnb 的 [javascript 风格指南](https://github.com/airbnb/javascript)，它增加了许多广为接受的林挺规则。可以用这个代替现在的。让我们将它添加到配置中。它需要安装一个额外的`eslint-plugin-import`依赖项。使用以下命令安装`eslint-config-airbnb-base`及其依赖项

`npx install-peerdeps --dev eslint-config-airbnb-base`

现在更新`.eslintrc`—

```
 {
-  "parserOptions": {
-   "ecmaVersion": 6,
-   "sourceType": "module"
-  },
-  "extends": "eslint:recommended", +  "extends": "airbnb-base",
   "globals": {
     "document": true,
     "console": true
   },
   "rules": {
     "no-console": 1
   }
 } 
```

`airbnb-base`内部有`parserOptions`。所以已经撤了。现在，当你运行`npm run dev`，你会得到一个错误-

```
...hello-world/src/message.js
1:16  error  Strings must use singlequote  quotes 
```

那是因为`airbnb-base`有一个规则，对字符串使用单引号，而不是双引号。用`--fix`选项运行`npm run lint`会自动将`src/message.js`中的`"`变为`'`。

这就结束了在您的代码中使用`eslint`来加强代码质量。

> 第 4 部分将更新这个项目，开始开发 ReactJS 应用程序。

这篇文章最初发表在的 [bitwiser.in](http://bitwiser.in/2018/02/22/setup-javascript-webpack-project.html)