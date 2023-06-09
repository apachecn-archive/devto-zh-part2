# 使用包裹和 GitHub 页面构建和部署 Vue/React/Vanilla JS 应用程序

> 原文：<https://dev.to/hugo__df/build-and-deploy-a-vuereactvanilla-js-app-with-parcel-and-github-pages-48a4>

pages 允许我们在没有配置的情况下捆绑前端应用程序，GitHub pages 为我们提供了一种免费部署它们的方式。

下面介绍如何结合使用这两个工具来部署 JavaScript 应用程序。

回购部署的完整示例如下:[https://github.com/HugoDF/js-graphql-client-example](https://github.com/HugoDF/js-graphql-client-example)，参见[https://codewithhugo.com/js-graphql-client-example/](https://codewithhugo.com/js-graphql-client-example/)。

或者回购与演示:[https://github.com/HugoDF/parcel-gh-pages-deploy](https://github.com/HugoDF/parcel-gh-pages-deploy)见[https://codewithhugo.com/parcel-gh-pages-deploy/](https://codewithhugo.com/parcel-gh-pages-deploy/)。

*   [设置包裹📦](#setting-up-parcel)
*   [免费部署💸](#deploying-for-free)

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

## 设置包裹📦

`npm install --save-dev parcel`

假设你有一个`index.html`和`client.js`在你的根:`index.html` :

```
<!doctype html>
<html class="no-js" lang="en">

  <head>
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="ie=edge">
    Your site title
    <meta name="description" content="Your site meta description">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  </head>

  <body>
    <div id="app">
    <script src="./client.js"></script>
  </body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

`client.js` :

```
const $app = document.querySelector('#app');
$app.innerText = 'Hello world'; 
```

Enter fullscreen mode Exit fullscreen mode

假设您不需要捆绑这个，但是假设我们需要。

使用 npm 5+可以运行:`npx parcel index.html`(在旧 npm 上，`./node_modules/.bin/parcel index.html`)。

转到 [http://localhost:1234](http://localhost:1234) 或运行带有`--open`选项(`npx parcel index.html --open`)的命令，您应该会看到以下内容:

> 注意相对于`index.html`位置的`./client.js`路径。

[![Hello world Parcel](img/23caadf311fb50a7688ae5bd97f59448.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gbM-qTQj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_E9E094C7512204BB25ABDCD9DE0C74A2094E37B7E71EFEFC5F9B77F1E4873835_1533922372858_Screen%2BShot%2B2018-08-10%2Bat%2B18.32.24.png)

React 和 Vue 单文件组件等也分别与简单的`.babelrc` include 和 npm install 的 [`babel-preset-react`](https://babeljs.io/docs/en/babel-preset-react.html) 或 [`babel-preset-vue`](https://github.com/vuejs/babel-preset-vue) 一起工作。

我们可以把脚本放在`package.json` :

```
{  "scripts":  {  "start":  "parcel index.html"  },  "devDependencies":  {  "parcel":  "^1.9.7"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后用`npm start`运行它，这将和我们之前用`npx`做的一样。

## 免费调配💸

`npm install --save-dev gh-pages`

在`package.json`中:如果你正在建立一个不属于`USERNAME.github.io`的回购协议:

```
{  "scripts":  {  "start":  "parcel index.html",  "predeploy":  "rm -rf dist && parcel build index.html --public-url YOUR_REPO_NAME",  "deploy":  "gh-pages -d dist"  },  "devDependencies":  {  "gh-pages":  "^1.2.0",  "parcel":  "^1.9.7"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的例子，这意味着:

```
{  "name":  "parcel-gh-pages-deploy",  "description":  "Deploy a parcel app to GitHub pages",  "scripts":  {  "start":  "parcel index.html",  "predeploy":  "rm -rf dist && parcel build index.html --public--url /parcel-gh-pages-deploy",  "deploy":  "gh-pages -d dist"  },  "devDependencies":  {  "gh-pages":  "^1.2.0",  "parcel":  "^1.9.7"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你*是*建筑`USERNAME.github.io`，用下面的`"` `predeploy` `"`代替:

```
"predeploy":  "rm -rf dist && parcel build index.html", 
```

Enter fullscreen mode Exit fullscreen mode

❤ GitHub 页面和包裹。

如有任何问题，请在 Twitter [@hugo__df](https://twitter.com/hugo__df) 告诉我。

佩皮·斯托扬诺夫斯基