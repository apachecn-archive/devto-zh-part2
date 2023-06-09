# Vue SPA，登录后带重定向的路由器防护

> 原文：<https://dev.to/mygnu/vue-spa-router-guard-with-redirect-after-login-2anj>

#### 原发表于我的[博客](https://hgill.io)

在制作[让我们组织](https://letsorganise.uk) SPA(单页应用)时，我们碰到了当用户访问需要认证的路线时正确检查认证和重定向的问题。如果用户通过验证，则转到请求的页面或重定向到`/login`页面。用户成功登录后，将他们重定向回请求的页面。

Vue 路由器有一个叫做[的导航卫士](https://router.vuejs.org/en/advanced/navigation-guards.html)来救援，你可以在每条路线或选定的路线前使用它作为钩子。导航警卫只是一个普通的功能，它的工作方式如下。

```
function guard(to, from, next){
    if(store.state.auth.loggedIn) {
        // or however you store your logged in state
        next(); // allow to enter route
    } else{
        next('/login'); // go to '/login';
    }
}
...
// later in the guarded routes
export default [{
    path: '/membership',
    beforeEnter: guard, // Using guard before entering the route
    component: require('layouts/membershipLayout').default,
    children: [
      { path: '', component: require('pages/membership').default },
      ...
    ]
  }...
  ] 
```

上面的代码在进入钩子之前调用了`guard`函数，并做了一个基本的检查和重定向。但是你真正需要的是路由器记住用户从哪里开始路由，并且检查用户是否已经被认证(可能在另一个标签中)。

我们的应用程序使用 JWT(JSON Web Token)来认证用户。JWT 不是存储在客户端 JavaScript 中，而是存储在安全的 https cookie 中。这意味着应用程序 JavaScript 无法判断该令牌是否有效，甚至无法判断它是否存在。这种设计需要至少一次到服务器的往返行程来验证令牌。

如果用户已经在另一个选项卡中进行了身份验证，重定向到登录页面并不是一个很好的用户体验。所以你可以把水流想象成。

### 路由器流量逻辑

[![alt text](img/9bef66230d3fcbc21a5565912441181d.png "Logic flow of guard Function")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1cqfJKw8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hgill.img/code/vuex-flowchart.svg)

我们希望在访问每个受保护的路线之前执行上面的逻辑流。我们有一个占位符变量`entryUrl`来跟踪用户进入网站的 url，初始设置为`null`。然后我们检查我们是否登录了，我们的应用程序保持一个变量在`Vuex`状态，如果登录了，然后我们检查`entryUrl`变量是否没有被设置，然后简单地像平常一样转到下一个路由。否则，我们重定向到`entryUrl`，并再次将变量设置为`null`。

如果 Vuex 登录变量没有设置为 true，(如果用户只是访问了一个 url 而没有通过登录页面)，那么我们调用服务器来检查用户是否已经被认证。
然后检查身份验证，如果是，则转到该路线。如果每次检查都失败，那么我们将用户访问的初始 url 存储在`entryUrl`变量中，并将用户重定向到“登录页面”。

上述模型的相应代码如下所示。

```
// routes.js

import store from '../store';

let entryUrl = null;

const guard = async (to, from, next) => {
  if (store.state.auth.loggedIn) {
    if (entryUrl) {
      const url = entryUrl;
      entryUrl = null;
      return next(url); // goto stored url
    } else {
      return next(); // all is fine
    }
  }

  await store.dispatch('checkAuth'); 
  // we use await as this async request has to finish 
  // before we can be sure

  if (store.state.auth.loggedIn) {
    next();
  } else {
    entryUrl = to.path; // store entry url before redirect
    next('/login');
  }
}; 
```

如果你喜欢，可以在推特上打招呼