# 用 Vue.js 认证的懒惰开发者指南

> 原文：<https://dev.to/oktadev/the-lazy-developers-guide-to-authentication-with-vuejs-1m8f>

我很高兴地承认，和你们许多人一样，我是一个懒惰的开发人员。我倾向于在尝试自己构建解决方案之前，先寻找别人已经构建好的解决方案。在 21 世纪初的“旧时光”中，我常常花更多的时间自己编写解决方案。但是今天许多编码问题的解决方案只是一个关键字搜索。本着这种精神，我使用关键字来学习本教程——首先寻找现有的解决方案，然后在此基础上找到我最喜欢的解决方案。我想你会喜欢这个结果的！

当我开始写这篇文章时，我想向您展示如何向 Vue.js 应用程序添加身份验证。我在四月份开始学习 Vue.js，当时我正在创作我的[面向后端开发者的前端开发演示文稿](https://speakerdeck.com/mraible/front-ends-for-back-end-developers-spring-io-2017)。我买了 Egghead.io 的专业版订阅，看着[用](https://egghead.io/lessons/vue-build-vue-js-apps-with-the-vue-cli-and-nuxt-js) [@johnlindquist](https://twitter.com/johnlindquist) 的 Vue-CLI 和 Nuxt.js 构建 Vue.js 应用。

## CLI+pwa 视图！

每个有移动用户的网络应用程序都应该增加 PWA 支持，这样应用程序可以更快地加载和离线工作。Vue.js 有[优秀的 PWA 支持](https://github.com/vuejs-templates/pwa)。如果你读过我的[渐进式网络应用终极指南](https://developer.okta.com/blog/2017/07/20/the-ultimate-guide-to-progressive-web-applications)，你可能会认出我是 PWAs 的粉丝。

我装了 [Vue CLI](https://github.com/vuejs/vue-cli) 就上班了。

```
npm install -g vue-cli
vue init pwa okta-vue-auth-example
cd okta-vue-auth-example
npm i 
```

[![Vue Init](../Images/a2fc072cf44985499a400a0de75a5716.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LZdBSqry--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/vue-init-b68441585a73522592020e6fdb58e0e527a5c82d541a3c0852fcdb60f111fce5.png)

我在 [IntelliJ IDEA](https://www.jetbrains.com/idea/) (免责声明:我最喜欢的 IDE)中打开这个项目，惊讶地发现 ESLint 规则更喜欢以*结尾，而不用*分号。JavaScript 中的分号之争愈演愈烈！

我为 IntelliJ 安装了 [Vue 插件，所以`*.vue`文件以正确的语法高亮显示。](https://plugins.jetbrains.com/plugin/8057-vue-js)

我谷歌了一下，找到了来自`vue-router`的 [`auth-flow`](https://github.com/vuejs/vue-router/blob/dev/examples/auth-flow) 例子。我开始从`auth-flow`项目复制一些文件到我的项目中。我创建了一个任何人都可以看到的页面(`/about`)和另一个只有认证用户才能看到的页面(`/dashboard`)。

## 添加授权流示例

如果你想继续，像我上面做的那样用`vue init`创建项目，然后用下面的 HTML 创建`src/components/About.vue`。

```
<template>
  <div>
    <h2>About</h2>
  </div>
</template> 
```

创建`src/components/Dashboard.vue`。只有经过认证的人才能看到这个页面。

```
<template>
  <div>
    <h2>Dashboard</h2>
    <p>Yay you made it!</p>
  </div>
</template> 
```

将 [`Login.vue`](https://github.com/vuejs/vue-router/blob/dev/examples/auth-flow/components/Login.vue) 从`auth-flow`复制到`src/components/Login.vue`。

```
<template>
  <div>
    <h2>Login</h2>
    <p v-if="$route.query.redirect">
      You need to login first.
    </p>
    <form @submit.prevent="login">
      <label><input v-model="email" placeholder="email"></label>
      <label><input v-model="pass" placeholder="password" type="password"></label> (hint: password1)<br>
      <button type="submit">login</button>
      <p v-if="error" class="error">Bad login information</p>
    </form>
  </div>
</template>

<script>
  import auth from '../auth'
  export default {
    data () {
      return {
        email: 'joe@example.com',
        pass: '',
        error: false
      }
    },
    methods: {
      login () {
        auth.login(this.email, this.pass, loggedIn => {
          if (!loggedIn) {
            this.error = true
          } else {
            this.$router.replace(this.$route.query.redirect || '/')
          }
        })
      }
    }
  }
</script>

<style>
  .error {
    color: red;
  }
</style> 
```

该组件依靠 [`auth.js`](https://github.com/vuejs/vue-router/blob/dev/examples/auth-flow/auth.js) ，伪造认证。创建`src/auth.js`并粘贴假的 auth 实现(目前)。

```
/* globals localStorage */

export default {
  login (email, pass, cb) {
    cb = arguments[arguments.length - 1]
    if (localStorage.token) {
      if (cb) cb(true)
      this.onChange(true)
      return
    }
    pretendRequest(email, pass, (res) => {
      if (res.authenticated) {
        localStorage.token = res.token
        if (cb) cb(true)
        this.onChange(true)
      } else {
        if (cb) cb(false)
        this.onChange(false)
      }
    })
  },

  getToken () {
    return localStorage.token
  },

  logout (cb) {
    delete localStorage.token
    if (cb) cb()
    this.onChange(false)
  },

  loggedIn () {
    return !!localStorage.token
  },

  onChange () {}
}

function pretendRequest (email, pass, cb) {
  setTimeout(() => {
    if (email === 'joe@example.com' && pass === 'password1') {
      cb({
        authenticated: true,
        token: Math.random().toString(36).substring(7)
      })
    } else {
      cb({ authenticated: false })
    }
  }, 0)
} 
```

将 [`App.vue`](https://github.com/vuejs/vue-router/blob/dev/examples/auth-flow/components/App.vue) (或下面的代码)从`auth-flow`复制到`src/App.vue`:

```
<template>
  <div id="app">
    <h1>Auth Flow</h1>
    <ul>
      <li>
        <router-link v-if="loggedIn" to="/logout">Log out</router-link>
        <router-link v-if="!loggedIn" to="/login">Log in</router-link>
      </li>
      <li>
        <router-link to="/about">About</router-link>
      </li>
      <li>
        <router-link to="/dashboard">Dashboard</router-link>
        (authenticated)
      </li>
    </ul>
    <template v-if="$route.matched.length">
      <router-view></router-view>
    </template>
    <template v-else>
      <p>You are logged {{ loggedIn ? 'in' : 'out' }}</p>
    </template>
  </div>
</template>

<script>
import auth from './auth'
export default {
  data () {
    return {
      loggedIn: auth.loggedIn()
    }
  },
  created () {
    auth.onChange = loggedIn => {
      this.loggedIn = loggedIn
    }
  }
}
</script> 
```

将下面的 HTML/CSS 添加到`src/App.vue`的底部，赋予它与`auth-flow`项目相同的样式。

```
<style>
  html, body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
    color: #2c3e50;
  }

  #app {
    padding: 0 20px;
  }

  ul {
    line-height: 1.5em;
    padding-left: 1.5em;
  }

  a {
    color: #7f8c8d;
    text-decoration: none;
  }

  a:hover {
    color: #4fc08d;
  }
</style> 
```

用以下代码替换`src/router/index.js`。这为应用程序设置了路由，保护了仪表板，并且看起来很直观！

```
import Vue from 'vue'
import Router from 'vue-router'
import auth from '@/auth'
import About from '@/components/About.vue'
import Dashboard from '@/components/Dashboard.vue'
import Login from '@/components/Login.vue'

Vue.use(Router)

export default new Router({
  mode: 'history',
  base: __dirname,
  routes: [
    { path: '/about', component: About },
    { path: '/dashboard', component: Dashboard, beforeEnter: requireAuth },
    { path: '/login', component: Login },
    { path: '/logout',
      beforeEnter (to, from, next) {
        auth.logout()
        next('/')
      }
    }
  ]
})

function requireAuth (to, from, next) {
  if (!auth.loggedIn()) {
    next({
      path: '/login',
      query: { redirect: to.fullPath }
    })
  } else {
    next()
  }
} 
```

完成这些更改后，您应该能够运行`npm start`，并使用来自`auth.js`(【joe@example.com】/密码 1】)的硬编码值进行身份验证。

如果你打开浏览器到`http://localhost:8080`，你应该会看到一个类似下面的屏幕。

[![Auth Flow Home](../Images/01c9b80c1caf67fc211dc3efa03c2566.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LqyXkA6L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/auth-flow-home-2b30ac35dc8e3a69e401f2195c02a9152c3dbcfe92edfd90c4f29dcd349399d2.png)

点击**仪表板**链接，系统会提示您登录。

[![Auth Flow Dashboard](../Images/0ad6f6782b6f5f5d9409691cb17eeb83.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nsAZbjOp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/auth-flow-login-caca6caf64d088b7251f66afc02ba89553311f2cc8e236922cb59574138c0b69.png)

输入`joe@example/password1`将进入下一步。

[![Auth Flow Dashboard](../Images/3bbd63c267b7a0df549d0e0b41981e7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--962MTWQK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/auth-flow-dashboard-4fabdf55f45050327c748464c9631146c7ea7a2f7dd6ac6380b6a1c46a14a30b.png)

如果你打开 Chrome 开发者工具，你会看到一条消息，建议安装 [Vue Devtools 扩展](https://github.com/vuejs/vue-devtools)以获得更好的开发体验。

## 添加 Okta 进行身份验证

要替换`src/auth.js`中伪造的硬编码认证，首先安装 [Okta Auth SDK](https://developer.okta.com/code/javascript/okta_auth_sdk)

```
npm install @okta/okta-auth-js --save 
```

用下面的代码替换`auth.js`中的代码，该代码使用 Auth SDK 登录并将会话令牌保存为令牌。如果你没有 Okta 开发者帐号，[创建一个](https://developer.okta.com/signup/)。然后用您的信息替换下面代码中的`{yourOktaDomain}`(例如，`dev-123456.oktapreview`)。

```
/* globals localStorage */
const OktaAuth = require('@okta/okta-auth-js')
const authClient = new OktaAuth({url: 'https://{yourOktaDomain}.com', issuer: 'default'})

export default {
  login (email, pass, cb) {
    cb = arguments[arguments.length - 1]
    if (localStorage.token) {
      if (cb) cb(true)
      this.onChange(true)
      return
    }
    return authClient.signIn({
      username: email,
      password: pass
    }).then(response => {
      if (response.status === 'SUCCESS') {
        localStorage.token = response.token
        if (cb) cb(true)
        this.onChange(true)
      }
    }).fail(err => {
      console.error(err.message)
      if (cb) cb(false)
      this.onChange(false)
    })
  },

  getToken () {
    return localStorage.token
  },

  logout (cb) {
    delete localStorage.token
    if (cb) cb()
    this.onChange(false)
    return authClient.signOut()
  },

  loggedIn () {
    return !!localStorage.token
  },

  onChange () {
  }
} 
```

为了能够从 [http://localhost:8080](http://localhost:8080) 与 Okta 对话，你需要将其添加为可信来源。你可以登录你的 Okta 仪表盘，导航到 **API** > **可信来源**来完成。添加`http://localhost:8080`并选择 CORS 和重定向复选框。

[![Add Origin](../Images/96f48aaab8cdfb4f02117be2a4377cac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LaU3v7qc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/add-origin-8580085d33e7d7efd626c61b44adcfc195986ae46a1dab49a4f4775e48e86c21.png)

做了这个更改后，一切都应该和以前一样工作，但是您必须使用 Okta 凭证登录。

### 提高安全性

您可以通过关闭`src/components/Login.vue`中的自动完成功能、将焦点添加到`email`字段并将其默认值初始化为空来提高应用程序的安全性。用下面的替换你现在的`Login.vue`。

```
<template>
  <div>
    <h2>Login</h2>
    <p v-if="$route.query.redirect">
      You need to login first.
    </p>
    <form @submit.prevent="login" autocomplete="off">
      <label><input v-model="email" placeholder="email" v-focus></label>
      <label><input v-model="pass" placeholder="password" type="password"></label><br>
      <button type="submit">login</button>
      <p v-if="error" class="error">Bad login information</p>
    </form>
  </div>
</template>

<script>
  import auth from '../auth'
  export default {
    data () {
      return {
        email: '',
        pass: '',
        error: false
      }
    },
    methods: {
      login () {
        auth.login(this.email, this.pass, loggedIn => {
          if (!loggedIn) {
            this.error = true
          } else {
            this.$router.replace(this.$route.query.redirect || '/')
          }
        })
      }
    }
  }
</script>

<style>
  .error {
    color: red;
  }
</style> 
```

为了使电子邮件字段上的`v-focus`指令工作，将以下内容添加到`src/main.js`(在`new Vue({...})`之前)。

```
Vue.directive('focus', {
  // When the bound element is inserted into the DOM...
  inserted: function (el) {
    // Focus the element
    el.focus()
  }
}) 
```

## 显示用户信息

根据云 API 认证用户很酷，但你知道什么更酷吗！？获取访问令牌并向您的用户展示您知道他们是谁！为此，您可以使用 Okta Auth SDK 的 OpenID Connect (OIDC)特性。您也可以获取一个访问令牌，而不是将会话令牌存储在`localStorage`中。这在调用充当资源服务器的 API 时非常有用。

> 您可以在[用 Kotlin、TypeScript 和 Okta](https://scotch.io/tutorials/build-a-secure-notes-application-with-kotlin-typescript-and-okta) 构建一个安全的 Notes 应用程序中看到如何使用 Spring Boot 和 Okta 创建一个资源服务器。

Okta Auth SDK 支持将您的会话令牌交换为访问令牌。将`authClient.signIn()`(在`src/auth.js`中)的成功回调替换为以下内容。

```
return authClient.signIn({
  username: email,
  password: pass
}).then(response => {
  if (response.status === 'SUCCESS') {
    return authClient.token.getWithoutPrompt({
      clientId: `{clientId}`,
      responseType: ['id_token', 'token'],
      scopes: ['openid', 'email', 'profile'],
      sessionToken: response.sessionToken,
      redirectUri: 'http://localhost:8080'
    }).then(tokens => {
      localStorage.token = tokens[1].accessToken
      localStorage.idToken = tokens[0].idToken
      if (cb) cb(true)
      this.onChange(true)
    })
  } 
```

你需要在俄克拉荷马州创建一个 OIDC 应用程序来获得`{clientId}`。为此，请登录您的 Okta 开发者账户，导航至**应用** > **添加应用**。点击 **SPA** 并点击**下一个**按钮。给这个应用取一个你会记住的名字，并指定`http://localhost:8080`为基本 URI，登录重定向 URI。

[![OIDC Settings](../Images/0094fffa65b7fd421cbfda07fbe457de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wGUmGcyA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/oidc-settings-d4d35063103b8a2f5dff3290a8b7bc6190854f07c78b192019b9ca1143c25e98.png)

点击**完成**，你会看到一个屏幕，屏幕下方显示这些信息和一个客户 ID。将客户端 ID 复制到`src/auth.js`中。

您会注意到新的回调将访问令牌保存为`localStorage`中的`token`。它还保存了`idToken`,这样它就可以解析 JWT 并检索用户名。您必须将`profile`添加到作用域列表中(默认为`[‘openid’, ‘email’]`),以获得认证用户的名称。

一旦有了`idToken`，就可以从中获取用户名。更好的是，你可以使用 [JWT 检查器](https://jwtinspector.io/) Chrome 插件来查看 JWT 值并将它们打印到 JavaScript 控制台。

要看到这一点，将以下两个方法添加到`src/auth.js`中，从解码的 JWT 中获得一个人的名字。感谢 JavaScript 代码中的[解码 jwt 的堆栈溢出。](https://stackoverflow.com/a/38552302/65681)

```
getName () {
  const claims = this.parseJwt(localStorage.idToken)
  console.jwt(localStorage.idToken)
  return claims['name']
 },

parseJwt (token) {
  const base64Url = token.split('.')[1]
  const base64 = base64Url.replace('-', '+').replace('_', '/')
  return JSON.parse(window.atob(base64))
}, 
```

**警告:**上面的代码使用了`console.jwt()`。这只适用于当你安装了 JWT 检查器。调试完代码后，移除或注释掉这一行。

现在 ID 令牌将为您提供用户名，您可以在仪表板上显示它！将`src/components/Dashboard.vue`修改为以下代码。

```
<template>
  <div>
    <h2>Dashboard</h2>
    <p>Yay you made it <b>{{name}}</b>!</p>
  </div>
</template>
<script>
  import auth from '../auth'
  export default {
    data () {
      return {
        name: auth.getName()
      }
    }
  }
</script> 
```

如果您的项目没有运行，用`npm start`启动它并导航到`http://localhost:8080`。如果有效，那就欢呼吧！

[![Welcome Okta](../Images/7d8d1e62747e35a183f0aec89efbf0c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tuA3CVsX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/welcome-okta-0508b4cb13edb8ae7ea2b9bc5cc722057e2473b531cb609e344d5627a931c3bd.png)

## 审核您的渐进式 Web 应用程序

你可以使用 [Lighthouse](https://developers.google.com/web/tools/lighthouse/) 来审核你的申请，看看它是否具备 PWA 所需的所有要素。为此，您必须构建您的项目，然后从`dist`目录运行 web 服务器。例如:

```
npm run build
cd dist
python -m SimpleHTTPServer 8080 
```

如果你在`http://localhost:8080`在你的应用上运行 Lighthouse，你应该会看到和下面截图类似的结果。

[![Lighthouse audits on localhost](../Images/5dc67d09a9c12dbf39b7b7457ef92e85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gsky4y5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/lighthouse-localhost-5539e490b8fc79145e7105b8dac01acdf8119755e5d982c3ab3a4b4b81128af5.png)

因为我喜欢看最大可能得分是多少，所以我将这个应用程序部署到 Pivotal 的 Cloud Foundry。我创建了一个`deploy.sh`脚本来代替`redirectUri`(你也可以删除它来达到同样的效果)，并使用`cf push`进行部署。

为此，您必须更新 Okta，将`https://vue-auth-pwa.cfapps.io`添加为登录重定向 URI(在您的 OIDC 应用程序中)和可信来源( **API** > **可信来源**)。

```
sed -i -e "s|http://localhost:8080|https://vue-auth-pwa.cfapps.io|g" src/auth.js
npm run build
cd dist
touch Staticfile
echo 'pushstate: enabled' > Staticfile
cf push vue-auth-pwa --no-start
cf set-env vue-auth-pwa FORCE_HTTPS true cf start vue-auth-pwa 
```

在[https://vue-auth-pwa . cf apps . io](https://vue-auth-pwa.cfapps.io)上运行 Lighthouse 会产生一些非常好的数字！

[![Lighthouse audits on Cloud Foundry](../Images/5b0970d5c3b06a82c9e2a3e9dfc11326.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NfIrhEYR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/vue-auth-sdk/lighthouse-cloudfoundry-738140e5bbe989753ca16f7fad5b19eedab28bce60b92b1c0bd2ba23c920ed9b.png)

## 在关闭

我希望你喜欢这个非常受欢迎的 Vue.js 的认证之旅。感谢[尤雨溪](https://twitter.com/youyuxi)创建了它，并使它变得又轻又快(非常适合 PWAs！).

如果你对 Vue.js 感兴趣，在 Twitter 上关注 [@vuejs](https://twitter.com/vuejs) 和 [@nuxt_js](https://twitter.com/nuxt_js) 。关注 [@OktaDev](https://twitter.com/oktadev) ，了解我们最新的 Okta 认证冒险。

你可以在 GitHub 上看到这位懒惰的开发者为本文[创建的代码。你也可以看看我在 PWAs 上写的其他一些文章。](https://github.com/oktadeveloper/okta-vue-auth-example)

*   [用 Angular 和 Spring Boot 构建你的第一个渐进式网络应用](/blog/2017/05/09/progressive-web-applications-with-angular-and-spring-boot)
*   [渐进式网络应用的终极指南](/blog/2017/07/20/the-ultimate-guide-to-progressive-web-applications)