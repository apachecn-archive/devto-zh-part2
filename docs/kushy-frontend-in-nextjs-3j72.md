# NextJS 中的 Kushy 前端

> 原文：<https://dev.to/whoisryosuke/kushy-frontend-in-nextjs-3j72>

最近，我决定开始尝试用 [NextJS](https://nextjs.org/) 来为 [Kushy](http://kushy.net) 实现一个 React 前端。目前，Kushy 直接运行 Laravel，而不是作为一个单独的应用程序使用 Laravel API。我一直在寻找深入的 React with Kushy，但很难找到一个适当扩展的框架，我一直避免从头开始做这件事(制作我自己的 Webpack 配置、路由和 CSS 分割，*所有这些爵士乐*)。

这个实验的目标是使用 [Kushy API](http://kushy.net/developers/) 为 Kushy 创建一个 NextJS 应用程序，并发现粗略创建 Kushy 的一些基本功能(通过 slug 浏览单个企业)需要什么。

## 举例

[![A screenshot of the Kushy frontend app running on Heroku](img/890f5f158c8c0b0ecfab3104c17045f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x4DykelC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/kushy-frontend-nextjs-screenshot-from-heroku-3e327eae8d5018fd9cc0730cbe38abd7-ca02f.png)

您可以查看部署在 **Heroku** 上的项目，或者浏览 Github 上的源代码:

*   [Heroku——现场测试现场，展示商店 Slug](https://kushy-frontend-next.herokuapp.com/shop/chronic-pain-relief-center)
*   [Kushy 上用于比较的实时页面](https://kushy.net/shops/chronic-pain-relief-center)
*   [Github 回购](https://github.com/whoisryosuke/kushy-frontend-next)

## 问题

在尝试使用 NextJS 时，我立即遇到了几个问题。幸运的是只有几个。

### 动态路由(/posts/{slug})

NextJS 没有这种开箱即用的。您必须用您的动态路由创建一个 NodeJS 服务器(在本例中是 Express)。

> 参见:[自定义服务器/路由上的 NextJS 文档](https://github.com/zeit/next.js/#custom-server-and-routing)、[本条](https://medium.com/@diamondgfx/nextjs-lessons-learned-part-2-f1781237cf5c)、[本插件](https://github.com/fridays/next-routes)，

最终使用插件([https://www.npmjs.com/package/next-routes](https://www.npmjs.com/package/next-routes))来“轻松”完成。不过将来我将只使用 Express API。

### 部署

NextJS 可以是 Jekyll 或者其他静态站点生成器。每次代码更改时，您都要运行一个构建过程，然后重新启动服务器。

`npm run build`
T1】

使得 NextJS 非常适合像 Heroku 这样处理这个过程的公司。如果他们没有代理/多个实例，也可能没有——因为 NextJS 在集群化时性能最好。

> 可以运行像 GatsbyJS 这样的静态构建过程，它可以运行在像 Github Pages 或 Netlify 这样的 CDN/host 上。但是您失去了 Node 和 Express 提供的动态路由和 SSR。这就是 GatsbyJS 的闪光点，它拉所有内容静态直播，而 NextJS 拉点播。

### 用户登录/授权

如果您需要在登录认证后锁定某些页面，NextJS 没有内置任何“受保护的”路由功能。幸运的是，NextJS 在服务器端和客户端的工作方式并不太难。

为了让用户登录，我使用了一个使用 OAuth2.0 流的 API，所以我将用户重定向到 API 的登录。一旦用户登录并批准了应用程序，他们就会被重定向回应用程序上的回调页面。这个页面向 API 发出最后一次请求(使用 API 发送给回调的秘密令牌)，API 向用户发出最后一次访问令牌或 JWT (JSON Web 令牌)响应。

这就是 NextJS 的神奇之处。我们将令牌存储在服务器端的 cookie 中，这允许我们从任何地方(服务器或客户端)获取它。当我们需要令牌时，我们从服务器端头解析它(通过`getInitialProps()`方法传递)——或者使用像`js-cookie`这样的库来获取客户端的 cookies。

要创建一个受保护的路由，您需要在`getInitialProps()`和`componentDidMount()`生命周期中检查 cookie。如果您找到了令牌的 cookie，HOC 将加载页面组件。但是如果它找不到 cookie，它会将用户重定向到一个公共页面(通常是登录)。

## 结论

就像任何好的框架一样，一旦你知道自己在做什么——并且弄清楚它是如何处理某些事情的——创建你想要的东西就会变得毫不费力。我离开了 NextJS 的工作，脑子里嗡嗡响着几个应用程序，现在我可以应用它们了。虽然我不确定这将是我们用于 Kushy 前端的框架，但它绝对是一个很好的竞争者。

坎派🍻
良

* * *

**参考文献**:

*   [Github 上的 kushy-frontend-next](https://github.com/whoisryosuke/kushy-frontend-next)
*   [NextJS -部署指南](https://nextjs.org/learn/basics/deploying-a-nextjs-app)
*   [NextJS -如何向页面发送请求参数(如 URL 中的 post slug)](https://github.com/zeit/next.js/issues/929)
*   [next-routes-next js 的动态路由](https://www.npmjs.com/package/next-routes)
*   [NextJS -如何使用多个插件/配置](https://github.com/zeit/next-plugins/issues/7)
*   [在 Next.js pt2 - Brandon Richey 中吸取经验教训](https://medium.com/@diamondgfx/nextjs-lessons-learned-part-2-f1781237cf5c)