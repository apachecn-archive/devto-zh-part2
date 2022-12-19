# (P)在服务器端做出反应？

> 原文：<https://dev.to/aravindballa/preacting-on-the-server-side-304l>

<figure>[![](../Images/425575f7d06183588216d0e3b2ea015a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VO7eKPMS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADGBcDGC6h1AEkd2VCNO4-g.jpeg) 

<figcaption>照片由[马可·拉斯特拉](https://unsplash.com/photos/4hKumOZth9A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/search/photos/quick?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

需要时间来实现服务器端的渲染是惊人的。😁对我来说也一样。你会从中受益匪浅。主要是，网页的**第一次有意义的绘制**很快。它仍然需要下载 js 来进行交互。但是，用户会**而不是**长时间盯着空白页面。

正如您可能知道的， [Preact](https://preactjs.com/) 是 3KB 的替代反应。它比 React 快一点，而且体积小很多。如果您来自 React，那么 API 基本上是相同的，很容易适应。

让我们看看如何使用服务器上的节点来实现这一点。我们将使用`preact-router`在客户端进行路由。

这是我创建的[回购](https://github.com/aravindballa/preact-ssr)，如果你想跟进的话。

## `mkdir`

创建一个空目录，npm 初始化它！

现在，我们安装必要的东西。

```
yarn add preact preact-router preact-render-to-string express

yarn add -D webpack webpack-cli babel-core babel-cli babel-loader 
babel-preset-env babel-plugin-transform-react-jsx babel-register 
```

Enter fullscreen mode Exit fullscreen mode

这里有几个新的软件包可以帮助我们。

这将有助于我们将应用程序渲染为字符串，以便我们可以将它包含在发送给客户端的 HTML 中。

`babel-register` -帮助在服务器上运行时传输 ES6 代码。

## Webpack🔷

看回购，看看项目是怎么架构的。`client`文件夹包含 Preact 代码，webpack 被配置为从该文件夹生成一个构建文件。

```
module.exports = {
  entry: {
    app: "./client/index.js"
  },
  output: {
    path: path.join(__dirname, "dist"),
    filename: "[name].js"
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: "babel-loader",
      }
    ]
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 服务器🚀

这里的主文件是`index.js`,它要求`babel`在运行时出现，并帮助传输代码。

```
require("babel-register")({
  presets: ["env"],
  "plugins": [
    ["transform-react-jsx", { "pragma": "h" }]
  ],
});
require("./server"); 
```

Enter fullscreen mode Exit fullscreen mode

`{"pragma": "h"}`是作为`transform-react-jsx babel`插件的一个选项给出的，因为我们正在处理 Preact，而`createElement()`是其中的`h()`。

一旦你把这个告诉巴别塔，它就会变魔术。✨

现在我们有了呈现逻辑存在的地方。

```
const express = require("express");
const { h } = require("preact");
const renderToString = require("preact-render-to-string");
const path = require("path");
const chalk = require("chalk");

const App = require('./client/App');

const app = express();
const port = 8080;

app.use(express.static(path.join(__dirname, "dist")));

app.listen(port);

app.get("*", (req, res) => {
  const html = renderToString(<App />);

  res.send(`
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Preact SSR
</head>
<body>
  <div id="app">${html}</div>
  <script src="./app.js"></script>
</body>
</html>
    `);
});

console.log(chalk.blue(`Server started at <http://localhost>:${port}`)); 
```

Enter fullscreen mode Exit fullscreen mode

看看我们是如何生成`html`并将其包含在`res.send()`中的。我们将 Webpack 输出`app.js`作为脚本标签。因为我们已经将`express.static`设置为 dist 目录，所以 Express 也将服务于该文件夹。

就是这样。

## 🏃

跑`node index.js`去看魔术。🎉

保重！