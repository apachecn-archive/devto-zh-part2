# 使用 React 路由器(或客户端路由器如何工作)刷新时修复“无法获取/URL”错误

> 原文：<https://dev.to/tylermcginnis/fixing-the-cannot-get-url-error-on-refresh-with-react-router-or-how-client-side-routers-work-4ho9>

开发人员在使用 React Router 构建(特别是刷新)应用时遇到的一个非常常见的错误是“无法获取/url”。在这篇文章中，我们将看看为什么会发生这种错误，以及如何处理它。

你一直在努力开发一款使用 React 路由器的 React 应用。事情进展得很顺利。你提前完成了最后期限，你可能会提前下班去享受周末的额外几个小时。你决定在起飞前最后检查一下你的应用程序。"那个按钮应该有多一点的边框半径."你认为。你改变它，点击刷新，你的应用程序崩溃。这不是你的典型错误。如果是的话，你就不会低声发誓了。`Cannot read property 'state' of undefined`，无忧无虑。你已经看过很多次了。这个不一样。这甚至不是应用程序崩溃，而是更深层次的问题。你透过手指盯着你的显示器。就是这样。你只能得到这些。你的整个应用程序在刷新时中断，你得到的只是三个字。

> 无法获取/仪表板

“可能是热模块更换问题。只是一个异常”——你乐观地说服自己。为了验证你的假设，你重启应用程序。“主页看起来不错。导航工作正常。让我们再次尝试刷新。”

> 无法获取/设置

失败。没有其他词语能如此完美地描述它。你的长周末泡汤了。甚至可能会花掉你整个周末的时间，因为你根本不知道会发生什么。幸运的是，你找到了这篇文章。Meta 对吧？

首先，让我们确定您遇到的问题。为此，我们需要讨论浏览器和客户端路由器是如何工作的。

在过去，事情很简单。如果您想获取`/dashboard`的内容，浏览器会向您的服务器发出 get 请求，通过检查 URL 的路径部分，服务器会判断出用户正在请求`/dashboard`页面。然后，它会抓取该页面并作为响应发送回浏览器。然后这些被称为客户端路由器(CSR)的东西出现了。有了 CSR(就像 React 路由器一样)，您不再需要每次更改路由时都向服务器发出请求。相反，您的 CSR 只是在本地浏览器上为您处理。因此，当您访问`/dashboard`时，您的 CSR 不是向您的服务器发出 GET 请求，而是使用一个名为`history.pushState`的浏览器 API 来手动更改 URL，然后它会呈现该特定路线的视图——所有这些都不会导致页面刷新。

让我们更深入地看看这个过程。

用户第一次加载你的应用程序(例如，访问你的网站)时，他们没有加载任何 JavaScript。这意味着没有 React，也没有 React 路由器——所以第一个请求总是发给你的服务器。然后，假设有一个成功的 GET 请求，所有的 JavaScript 加载和 React Router 自信地劫持您的路由。从现在开始，应用程序中的任何其他路由更改都将由 React Router 处理。

注意到问题了吗？React 路由器只能在第一个成功的 GET 请求发送到你的服务器后加载(或者`/`)。可怕的`Cannot GET /*`错误的原因是，如果你在`/dashboard`然后点击刷新，浏览器将向`/dashboard`发出 GET 请求，这将失败，因为你的服务器上没有处理该请求的逻辑(因为 React 路由器应该这样做)。

如果这个问题仍然模糊不清，这里还有一个例子。说你真的为你正在开发的应用程序感到骄傲，你想和你妈妈分享它。该应用程序是井字游戏，有三条路线，`/`、`/play`和`leaderboard`。你把链接`https://tictactyler.com/play`发给你妈妈，因为你想和她一起玩。当她在浏览器中输入网址并点击回车时，会发生什么呢？此时，她没有 JavaScript，没有 React，也没有 React 路由器。浏览器向`/play`发出 GET 请求，由于你依赖 React 路由器来处理所有路由逻辑(但她还没有 React 路由器)，应用程序崩溃，她得到`Cannot GET /play`。

> “好吧，好吧，好吧。”马修·麦康纳

现在最大的问题是，我们如何解决这个问题？

问题的根源在于，您完全依赖客户端路由，而没有设置任何逻辑来处理服务器端路由。解决这个问题有两个主要思路。首先，设置客户端和服务器端路由。第二，将所有服务器请求重定向到`/index.html`,它将下载所有 JS 资源，并允许 React Router 从那里获取资源。我们将看到的大多数解决方案都涉及后者，因为后者更简单。

* * *

### 哈希历史

TBH，这是一种黑客。你见过那些带`#`的网址吗？他们使用哈希历史。这个想法是通过添加一个`#`到你的 URL 的根的末尾，在这个`#`之后的任何东西都不会被发送到服务器。因此，如果 URL 是`https://tm.io/#/courses`，浏览器将向`https://tm.io`发出 GET 请求，取回所有的 JavaScript，然后 React Router 将加载，见`/courses`，并显示该路由的正确视图。React Router 提供了一个 [HashRouter](https://reacttraining.com/react-router/web/api/HashRouter) 组件，你可以使用它来获得基于散列的路由，但是老实说，除非你真的需要它，否则还有更好的选择。

* * *

### 包罗万象

如果您已经有一台正在使用的服务器，这可能是您的最佳选择。这里的主要思想是将所有服务器请求重定向到`/index.html`。结果类似于哈希历史。对您的服务器的任何请求都将使用索引页面进行响应(然后获取您需要的任何 JS 资源)，React Router 将接管并加载适当的视图。实际的代码根据您拥有的类型而有所不同。这里有一些例子

##### 快递

```
app.get('/*', function(req, res) {
  res.sendFile(path.join(__dirname, 'path/to/your/index.html'), function(err) {
    if (err) {
      res.status(500).send(err)
    }
  })
}) 
```

#### Appache。htaccess

```
RewriteBase /
RewriteRule ^index\.html$ - [L]
 RewriteCond %{REQUEST_FILENAME} !-f
 RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.html [L] 
```

#### 基尼系数。conf 文件

```
location / {
  if (!-e $request_filename){
    rewrite ^(.*)$ /index.html break;
  }
} 
```

* * *

### 没有服务器

对于那些不必担心管理服务器的受祝福的开发者来说，也有一些选项给你，它们(通常)被嵌入到你正在使用的托管服务中。这里显然有很多不同的变体，但是你需要找到一个支持客户端路由器的服务。例如，如果你用 Firebase 做主机，有一个问题会问你

> 配置为单页 app(将所有 URL 重写为/index.html)？

Netlify 也支持客户端路由，你只需要用下面的规则创建一个`/_redirects`文件

```
/*  /index.html  200 
```

正如您可能猜到的，这告诉 Netlify 将所有请求重定向到`.index.html`。

* * *

### Webpack /开发

这一节是为在使用`webpack-dev-server.`开发中遇到这个问题的每个人准备的。就像上面一样，我们需要做的是告诉 Webpack Dev Sever 将所有服务器请求重定向到`/index.html`。在您的 webpack 配置中，只有两个属性需要设置，即`publicPath`和`historyApiFallback`。

```
publicPath: '/',
historyApiFallback: true, 
```

`publicPath`允许您指定应用程序中所有资产的基本路径。`historyAPIFallback`会将 404s 重定向到`/index.html`。

下面是一个基本的 webpack 配置文件的例子，如果你需要的话，可以使用这两个选项。

```
var path = require('path');
var HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './app/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'index_bundle.js',
    publicPath: '/'
  },
  module: {
    rules: [
      { test: /\.(js)$/, use: 'babel-loader' },
      { test: /\.css$/, use: [ 'style-loader', 'css-loader' ]}
    ]
  },
  devServer: {
    historyApiFallback: true,
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: 'app/index.html'
    })
  ]
}; 
```

就是这样。现在去享受你的周末吧🍻。

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们 T2 反应路由器课程的一部分。