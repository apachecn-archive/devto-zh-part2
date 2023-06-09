# 使用 OAuth2 和 JWT 的 NextJS 和认证

> 原文：<https://dev.to/whoisryosuke/nextjs-and-authentication-using-oauth2-and-jwt-3gc6>

我们将使用 OAuth2 过程通过我们的 Laravel API 来认证我们的 NextJS 应用程序用户(感谢 Passport)。如果你没有使用 Laravel，这个过程在概念上应该与 Twitter 等第三方 API 非常相似。

> 本指南假设您对 React 有所了解，知道什么是 HOC 以及如何使用它，并且了解诸如 NextJS、Express 或 OAuth2 之类的基础知识。

## 工作原理

假设你需要一个应用程序，用户可以登录并访问注册用户的私人页面。您可以创建一个接受用户名和密码的登录表单，并将其发送给 API。但是对于每个你想要认证的小应用程序来说，要写很多代码。这就是 OAuth2 的用武之地。

使用 OAuth2，我们可以让用户点击一个登录链接，比如“用 Twitter 登录”按钮。这会将用户重定向到 Twitter，在那里他们使用自己的 Twitter 帐户登录，并接受您的应用程序。Twitter 随后会向您发送一个特殊的“访问令牌”，允许您代表用户使用 Twitter API。

现在代替 Twitter，想象它是你自己的 API。用户被发送到您创建的登录服务器，您将获得一个适用于您自己的 API 的访问令牌。这样，你可以允许开发者以用户身份创建使用你的 API 的应用，而不是直接访问。这样，您可以根据您设置的用户权限来限制 API 访问。

## 认证 API

首先，我们需要一个认证 API 来使用，这样我们就可以在我们的前端 NextJS 应用程序中使用它。API 将使用 OAuth2 协议，并为每个授权用户返回一个 JSON Web 令牌(或 JWT)。

### oauth 2 流程

OAuth2 初看起来可能令人生畏，但由于每个主要平台(Twitter、脸书、谷歌和无数其他平台)都使用它来访问 API 的认证部分，您很快就会了解它。通过您的应用程序登录用户的过程非常简单:

[![OAuth 2.0 Authentication Process with NextJS App](img/71810041b0fba55f27625bc8eccc2ef3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gCngy_Ol--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/OAuth2-Process.min-cad1af9f2e2fcbae4bb95cd96a966a6f-e2462.png)

1.  您登录到想要用于身份验证的 API，并使用回调 URL 创建一个新的 **"client"** 。这个回调 URL 是应用程序上的页面，API 在用户登录后重定向用户。在 Twitter 上，这是“应用”页面吗？

> 创建**“客户端”**会生成一个 API 客户端 ID 和密码，您可以用它来通过 API 验证您自己的应用程序。留着以后用。ID 是公开的(任何人都能看到)，而秘密是嗯，秘密。你永远不会让用户看到或访问你的秘密，它存在于服务器端代码中。

1.  您可以在应用程序中创建一个按钮，或者将您的用户重定向到一个特定的 URL，该 URL 包含带有您的客户端 ID、回调 URL 和响应类型(默认为“code”)的查询参数。应该是这样的:

[http://127 . 0 . 0 . 1/oauth/authorize/？* * client _ id **= 2&* * redirect _ uri * * = http://127 . 0 . 0 . 1:3000/token&* * response _ type * * =代码&* *范围* * =访问用户帐户](http://127.0.0.1/oauth/authorize/?**client_id**=2&**redirect_uri**=http://127.0.0.1:3000/token&**response_type**=code&**scope**=access-user-account)

> 有时，URL 的查询参数中会添加范围，这些参数是给你额外 API 特权的关键字符串——比如访问用户电子邮件。

1.  用户被重定向到 API，如果他们没有登录，就会看到一个登录屏幕。一旦他们登录，就会看到您的应用程序的授权请求。该请求是一个窗口，显示您之前输入的应用程序名称，并询问用户是否批准它代表他们访问 API。如果你曾经使用 Twitter/脸书/etc 登录过一个网站，你会看到类似这样的东西。

2.  如果用户同意，他们将被重定向回您之前创建应用程序时使用的回调 URL。当用户被重定向时，他们会被发送一个特殊的代码。在应用程序的回调页面上，您获取这段代码并向 API 发出另一个请求，请求中包含这段代码和您的秘密。

3.  如果 API 批准了您的请求，它会发回一个 JSON 响应，其中包含用户的令牌和其他相关数据。通常您会从响应中获取令牌，并将其存储在会话或 cookie 中以备后用(因为您会经常用到它！).

## 创建 API

既然您已经了解了我们正在创建什么样的 API，我们实际上就可以实现它了。

我有一个关于使用 Laravel 和 Passport 包创建快速认证 API 的指南。尝试旋转那个项目，并与这个项目一起使用。它将创建一个单独的服务器，您可以通过 API 访问登录用户并取回令牌。

一旦 API 服务器启动并运行，运行以下命令创建一个新的应用程序:

`php artisan passport:client`

> 如果你在 Docker 容器中(像我的项目使用的)，确保使用 docker-compose `exec`命令:`docker-compose exec workspace php artisan`

CLI 将要求一个应用程序名称，并为您生成一个客户端 ID 和密码，存储这些供以后使用。

> 理想情况下，您应该创建一个前端 UI 来生成这些 API 凭证，如果您需要的话，Laravel 确实在 Vue 中提供了一个示例版本。但是对于测试或更简单的目的，CLI 工作得很好。

### 拉腊维尔的替代品

我知道 PHP 或 Laravel 不是每个人的囊中之物，或者你没有时间在 Heroku - **上运行服务器，你只是想要一个有效的 API。**

你可以利用 Twitter 或 Slack 这样的免费平台，使用他们的“登录……”服务。然而，如果您想要获取自己的 API，您可能仍然需要使用自己的认证服务器。否则，使用 Twitter 登录的唯一原因是通过您的应用程序代表用户访问 Twitter API。

> 除了创建自己的或使用社交媒体平台，您还可以使用第三方服务，如 [Auth0](http://auth0.com) 来利用他们基于云的认证 API。像 [Netlify](http://netlify.com) 这样的平台也为你的项目提供 JWT 支持。

## next js App

这里没有什么特别的，只是一个新项目，并让一些模块。当我们使用它们时，我会更深入地解释它们:

`npm install react react-dom next express cookie-parser csurf dotenv isomorphic-unfetch js-cookie --save`

### App 结构

应用程序的结构非常简单，我将向您展示几种不同的技术来授权您的应用程序(服务器端和客户端)。

1.  将用户重定向到 API 登录的登录链接或页面。
2.  回调页面接受 API 的响应代码，发送带有我们的秘密的请求，接收访问令牌(JWT)，并将其存储在我们的应用程序中。
3.  一个用于私有页面的 React 页面包装器(或 HOC ),它将检查 cookies 中的令牌，如果不检查，它们将被重定向到公共页面(通常是登录)。

### 登录链接

我们需要在应用程序的任何地方创建一个链接，作为应用程序的“登录”按钮。这将是一个简单的`<a>`元素，链接到我们的 Laravel API 和/oauth/authorize/ endpoint(假设是 localhost):

`http://localhost/oauth/authorize/?client_id=4&redirect_uri=http://localhost:3000/token&response_type=code`

这个链接包含我们的**客户端 id** 、**重定向 uri** 或回调 URL，以及一个**响应类型**，它应该被设置为“代码”。当我们使用 Laravel API 创建一个新的 OAuth 应用程序时，**客户端 id** 和 **redirect_uri** 会被提供给我们。通常你会设置一个前端表单，像 Twitter 的开发者部分和他们的表单来创建新的“应用”来交换 API 密钥。

> 如果你想使用 JS，甚至 PHP Artisan CLI 创建一个应用程序，查看 Laravel Passport 文档以获取更多信息。

### 创建回调 URL

使用 Express 或者 NextJS 的定制服务器设置，我们需要设置一个回调路由，API 可以查询这个路由来验证我们的凭证。这是一个相当简单的过程:

1.  创建一个获取路径，并随意命名。
2.  用我们的凭证和 API 服务器发送的请求代码向 API 发出一个`fetch()`请求。
3.  使用 cookie-parser 和 Express ( `res.cookies`)将令牌存储在 cookie 中。

下面是`server.js` :
中的最后一段代码

```
// Callback for OAuth2 API
server.get('/token', (req, res) => {
    const callback = {
    grant_type: 'authorization_code',
    client_id: process.env.API_CLIENT_ID,
    client_secret: process.env.API_CLIENT_SECRET,
    redirect_uri: process.env.API_REDIRECT_URI,
    code: req.query.code
    }

    // Query API for token
    fetch('http://localhost/oauth/token', {
        method: 'post',
        headers: {
        'Content-Type': 'application/json'
        },
        body: JSON.stringify(callback)
    })
    .then(r => r.json())
    .then(data => jsonErrorCheck(data))
    .then(data => {
        // Store JWT from response in cookies
        res.cookie('kushyFToken', data.access_token, {
        maxAge: 900000,
        httpOnly: true
        });

        // store object in session (with express-session)
        // req.session.token = data.access_token

        return res.redirect('/dashboard')
    }); 
```

Enter fullscreen mode Exit fullscreen mode

> 不要忘记使用`fetch()`的同构版本，因为 Node 没有安装它。

### 保护路线+从 cookies 中获取我们的令牌

为了访问每个路由中的令牌，我们将创建一个多用途的自组织网络。这个特设将包装我们的组件，只有当我们有一个令牌时才返回它们，否则它将重定向用户。HOC 的工作方式是从 cookie(服务器或浏览器 cookie)中获取我们的令牌，如果令牌被检索到，我们包装的组件就会被加载。

> 在生产设置中，您应该在需要授权页面时随时验证令牌。您永远不知道用户是否伪造了他们的 cookie，所以仅仅接受令牌是不够的——令牌需要工作。用令牌向我们的 API 发出一个快速的`fetch()`请求(通常在用户概要端点上，这样我们就不会浪费 API 调用和获取相关数据)。如果请求有效，则用户被批准。检查后，您可以设置一个服务器端 cookie(用户 ID 的加密哈希+当前时间+应用程序密码)，让应用程序知道用户最近获得了授权。

我们还将利用 React 的上下文 API，用包含我们令牌的提供者来包装我们授权的页面组件。这样，您可以将任何需要访问经过身份验证的 API 路由的组件包装在消费者中。这个消费者将令牌传递给 props 中的组件。

#### 但是生产呢？

在生产中，最佳实践是永远不要向任何人展示访问令牌(JWT)。但是由于 OAuth2 相当安全的特性，以及运行经过认证的客户端请求的需要，开发人员经常在 cookies 中抛出未加密的 JWT，或者更糟的是，`localStorage`。

理想情况下，您应该将 JWT 存储在服务器端会话中，其中加密的 ID 保存在 cookie 中，而 JWT 存储在服务器端数据存储中(如 Redis 或 Memcached)。每当你需要令牌的时候，你可以用加密的 cookie ID 和 CSRF 令牌(由服务器生成，以确保它来自服务器创建的`<form>`-而不是随机的 POST 请求)向应用的 Express 服务器发送 POST 请求。Express 服务器检查数据存储中 cookie 的 ID，获取令牌，并使用它来发出 API 请求，然后返回 API 的响应。

您可以看到应用程序的服务器充当了“中间人”的角色，以确保客户端和 API 之间的安全传输。这样，没有人可以通过创建带有令牌的假 cookies 来尝试登录。令牌必须存储在服务器端，并且它们需要适当的密钥来访问数据存储中当前的任何令牌。如果你感兴趣，我在下面介绍了这个方法。

### 授权特设

对于 HOC，我们在`getInitialProps()`中加载包含令牌的道具，令牌是从 cookies 中获取的。然后当组件挂载时(`componentDidMount()`)，我们运行一个检查来查看令牌是否在 props 中。如果没有，我们使用 Next 的`Router`重定向到登录页面。如果我们有一个令牌，我们将加载状态设置为 false，这将激活受保护路由的组件(我们包装 auth HOC 的组件)。

> 您可以随意用真正的加载组件替换“加载”文本，或者用加载器替换页面。

现在我们通常使用 cookies 来获取令牌，但是我将向您展示如何在服务器端也这样做，这样您就可以看到为什么它不是最佳选择。

#### 服务器端

创建一个中间件抓取 cookie 并存储在`res.local.cookies`中，然后在渲染每条路线时传入 app:`return app.render(req, res, '/dashboard', { token: res.locals.token })`。

server.js

```
// A JSON error checking function since fetch()
// won't let us know if it failed or not
function jsonErrorCheck(data) {
  if('error' in data)
  {
    return error
  }
  return data
}

// The middleware
function getUser(req, res, next) {
  if (req.cookies['kushyFToken']) {
    const credentials = {
      method: 'get',
      headers: {
        'Authorization': 'Bearer ' + req.cookies['kushyFToken'],
        'Content-Type': 'application/x-www-form-urlencoded'
      }
    }

    const fetchUser = async () => {
      await fetch('http://localhost/api/user/', credentials)
        .then(r => r.json())
        .then(data => jsonErrorCheck(data))
        .then(data => {
          res.locals.token = req.cookies['kushyFToken']
          res.locals.user = data
          next()        
        });
      }
      fetchUser();
  } else {
    next()
  }
}

// .... some other code

// Apply middleware to your route
// You basically stack middleware after the page string
// and before your page render 
// otherwise the middleware would never get called
server.get('/dashboard', getUser, (req, res) => {
    return app.render(req, res, '/dashboard', { token: res.locals.token, user: res.locals.user })
}) 
```

Enter fullscreen mode Exit fullscreen mode

```
import React, {Component} from 'react'
import Router from 'next/router'

export default function withAuth(AuthComponent) {
    return class Authenticated extends Component {

      static async getInitialProps(ctx) {
        // Check if Page has a `getInitialProps`; if so, call it.
        const pageProps = AuthComponent.getInitialProps && await AuthComponent.getInitialProps(ctx);
        // Return props.
        return { 
          ...pageProps,
          token: ctx.query.token
          user: ctx.query.user
        }
      }

      constructor(props) {
        super(props)
        this.state = {
          isLoading: true,
        };
      }

      componentDidMount () {
        // Console logs for convenience
        // console.log('protected page, did we get token?:');
        // console.log(this.props.token);
        if (!this.props.token) {
          Router.push('/login')
        }
        this.setState({ isLoading: false })
      }

      render() {
        return (
          <div>
          {this.state.isLoading ? (
              <div>LOADING....</div>
            ) : (
                // We don't need to explicitly pass token or user as props here
                // because {...this.props} includes them (ES6 destructuring ftw)
              <AuthComponent {...this.props}  auth={Auth} />
            )}
          </div>
        )
      }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这很繁琐，因为它要求您将令牌放在每个请求中。如果一个页面加载客户端，而不发出服务器请求，我们就无法访问包含我们令牌的`ctx`变量。

所以**我们被迫使用客户端数据存储**，最好是像 **cookies** 或 IndexDB 这样的安全存储。

#### 使用 Cookies

因为我们使用了用于 Express 的 [cookie-parser](https://www.npmjs.com/package/cookie-parser) 中间件，所以我们可以使用处于`getInitialProps`状态的`req`变量:`ctx.req.headers.cookie`来访问 cookie 服务器端。这将返回一个巨大的字符串，其中包含由&符号分隔的所有 cookies(比如查询参数)。如果我们解析字符串并通过键名找到 cookie，我们就有了 cookie。

或者，如果我们在浏览器中(就像用户在应用程序中点击一个内部的`<Link />`，我们可以使用 [js-cookie](https://github.com/js-cookie/js-cookie) 库从客户端获取 cookie(`cookie.get('token')`)。

> 我使用助手函数使访问 cookies 变得更容易(从 Github 上的@carlos-peru 偷来的[)。它基本上是 js-cookie 的包装器，检查用户是服务器端还是客户端。如果它们是服务器端的，它会在`req`变量中搜索我们的 cookie 名称。如果是客户端，就用 js-cookie。](https://github.com/carlos-peru/next-with-api/blob/master/lib/session.js)

utils/Cookies.js

```
import cookie from "js-cookie";

export const setCookie = (key, value) => {
    if (process.browser) {
        cookie.set(key, value, {
            expires: 1,
            path: "/"
        });
    }
};

export const removeCookie = key => {
    if (process.browser) {
        cookie.remove(key, {
            expires: 1
        });
    }
};

export const getCookie = (key, req) => {
    return process.browser ?
        getCookieFromBrowser(key) :
        getCookieFromServer(key, req);
};

const getCookieFromBrowser = key => {
    console.log('grabbing key from browser')
    return cookie.get(key);
};

const getCookieFromServer = (key, req) => {
    console.log('grabbing key from server')
    if (!req.headers.cookie) {
        return undefined;
    }
    const rawCookie = req.headers.cookie
        .split(";")
        .find(c => c.trim().startsWith(`${key}=`));
    if (!rawCookie) {
        return undefined;
    }
    return rawCookie.split("=")[1];
}; 
```

Enter fullscreen mode Exit fullscreen mode

withAuth.js

```
import React, {Component} from 'react'
import Router from 'next/router'
import TokenContext from '../context/TokenContext'
import { getCookie } from '../utils/Cookies'

export default function withAuth(AuthComponent) {
    return class Authenticated extends Component {

      static async getInitialProps(ctx) {
        const token = getCookie('kushyFToken', ctx.req)
        // Check if Page has a `getInitialProps`; if so, call it.
        const pageProps = AuthComponent.getInitialProps && await AuthComponent.getInitialProps(ctx);
        // Return props.
        return { ...pageProps, token }
      }

      constructor(props) {
        super(props)
        this.state = {
          isLoading: true
        };
      }

      componentDidMount () {
        console.log('checking auth')
        if (!this.props.token) {
          Router.push('/')
        }
        this.setState({ isLoading: false })
      }

      render() {
        return (
          <div>
          {this.state.isLoading ? (
              <div>LOADING....</div>
            ) : (
              <TokenContext.Provider value={this.props.token}>
                <AuthComponent {...this.props} />
              </TokenContext.Provider>
            )}
          </div>
        )
      }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个更好的方法，因为您获得了服务器和客户端 cookies 的安全性。

> Protip:您可能希望利用上下文 API，将授权组件包装在带有令牌数据的提供者中，这样应用程序的任何部分都可以轻松访问令牌，而无需进行任何道具演练！您的另一个选择是使用 Cookies 助手/实用程序函数，并在您需要的任何组件中再次获取 cookie，如上所示。*或者你知道，Redux。*

### 秘密第三选项:API 端点

既然我们使用 Express 来创建路由，并且我们可以访问服务器来运行 Javascript，那么我们没有理由不能创建一个动态路由来充当外部 API 的中间人。您可以从动态路由(`app.render('/api/:endpoint', callback)`)中获取 API 端点，并使用它(`req.params.endpoint`)发送一个经过身份验证的 API 请求，其中包含我们之前获得的 cookie(`req.cookies['seshToken']`)。

```
// Route for sending POST requests
server.post('/api/:endpoint', (req, res) => {

    // Query protected API endpoint with token
    fetch(`http://localhost/api/${req.params.endpoint}`, {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
        'Authorization': `Bearer ${req.cookies['seshToken']}`
    },
    // Grabs request body (ideally Form data), converts to JSON, and sends it as POST
    body: JSON.stringify(req.query)
    })
});

// Route for sending authenticated GET requests
server.get('/api/:endpoint', (req, res) => {

    // Query protected API endpoint with token
    fetch(`http://localhost/api/${req.params.endpoint}`, {
    method: 'GET',
    headers: {
        'Content-Type': 'application/json'
        'Authorization': `Bearer ${req.cookies['seshToken']}`
    }
    })
}); 
```

Enter fullscreen mode Exit fullscreen mode

这样，客户机就永远不会暴露给源代码中的令牌(只有当它们检查它们的 cookies 时)。这是与会话(例如快速会话)配对时的**最佳情况场景**。尽管它限制了您在服务器端使用 NextJS，并且**要求您在产品中有一个数据存储(像 Redis)** 。

## HOC vs 函数式编程

特设看起来像过度杀伤吗？你想要更实用的东西吗？您可以在页面的`getInitialProps()`中添加一种“中间件”,检查 cookies 中的令牌，并在 false 时重定向，而不是像我们之前那样将组件包装在 auth HOC 中。

您的页面组件:

```
static async getInitialProps(ctx) { 
  // If it does not exist session, it gets redirected
  if (redirectIfNotAuthenticated(ctx)) {
    return {}; 
  } 
```

Enter fullscreen mode Exit fullscreen mode

以及“中间件”背后的魔力:

```
export const getJwt = ctx => {
  return getCookie("jwt", ctx.req);
};

export const isAuthenticated = ctx => !!getJwt(ctx);

export const redirectIfAuthenticated = ctx => {
  if (isAuthenticated(ctx)) {
    redirect("/user", ctx);
    return true;
  }
  return false;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这基本上就是我们在 HOC 中所做的事情，但是被分解成不同的功能。更像是同一解决方案的功能方法。

## 要不要 Redux？

**从技术上来说，你不需要 Redux** ，因为你可以使用 js-cookie 从客户端访问 cookie，或者将你的应用包装在一个令牌提供者中。但是我发现，当我需要开始管理我的应用程序中更复杂的状态时，我开始使用 Redux，特别是**持久状态**。你可以整天使用上下文 API，但是如果你不从`localStorage`写一个回退和检索系统，你的应用程序的状态将在每次刷新时被清除。这需要动态数据请求再次运行，*再次运行*、**、*再次运行*、**。

### 数据消失

使用 Redux 获取数据并将结果存储在应用程序的状态中允许更多的数据持久性，特别是在像 NextJS 这样的通用应用程序中，它运行频繁的服务器和客户端请求。如果你的应用只是客户端的(比如 SPA)，Redux 可能是微不足道的，因为你的用户很少会“硬”重新加载页面。但是由于一些 NextJS 路由加载服务器端，它刷新你的应用程序的状态，要求你的上下文提供者再次访问 API。

但是就像我说的，您可以使用上下文提供者将您的状态持久化到`localStorage`，或者另一个本地数据存储，并使用消费者在您再次需要它时获取它。

### 复杂数据管理

想象一个应用程序，其中的组件需要访问来自提供商的 3 个(或更多)不同的值。每次创建一个组件，你都需要在组件周围包装 3 个以上的消费者，并适当地向下钻取道具(或者[创建一个 HOC 来将它们组合成道具](https://www.npmjs.com/package/react-context-consumer-hoc))。

```
// Impractical code that doesn't work
const SomeComponent = () => (
    <ThemeContext.Consumer>
        { theme => 
            <TokenContext.Consumer theme={theme}>
                { token => 
                    ...etc
                }
            </TokenContext.Consumer>
        }
    </ThemeContext.Consumer> ) 
```

Enter fullscreen mode Exit fullscreen mode

使用 Redux，您可以为组件创建一个容器组件。容器将组件连接到 Redux 存储，并发送您需要的任何状态值作为道具。一旦连接上，组件就可以访问您定义的存储中的任何内容。

### 如此...Redux？

没有 Redux、*和*你会惊讶自己能走多远，这一切都是为了让*的旅程更平稳*。做你认为最适合你的应用的事情。[*Redux 创建者的强制链接，表示你可能不需要它*](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)

## 就这么简单

由于 NextJS 能够执行服务器端任务——甚至可以利用 Express 之类的其他框架——这使得通过 OAuth2 用必要的路径扩展前端应用程序变得非常简单。本教程使用 Laravel API 作为示例，但是实现其他基于 OAuth2 的 API(如 Twitter)的代码非常相似。

如果我的指南令人困惑，我建议看一下来自 NextJS 会议的视频[,它在 15 分钟内解释了 NextJS 用户认证过程。](https://www.youtube.com/watch?v=bo4BbGwZsWo)

[在这里下载 Github 上的项目](https://github.com/whoisryosuke/nextjs-oauth2-cookie-auth)

坎派🍻
良

* * *

**参考文献**:

*   [Prosper Otemuyiwa:next . js 中的认证和授权](https://www.youtube.com/watch?v=bo4BbGwZsWo)
*   [加密您的 cookie](http://blog.teamtreehouse.com/encrypting-cookies-in-the-browser)
*   [js-cookie](https://github.com/js-cookie/js-cookie)
*   [Cookies 助手功能](https://github.com/carlos-peru/next-with-api/blob/master/lib/session.js)
*   [为 getInitialProps 创建认证中间件](https://medium.com/@positivecarlos/authentication-on-universal-react-with-next-js-b441ef458046)
*   [OAuth 2.0 -官方](https://oauth.net/2/)