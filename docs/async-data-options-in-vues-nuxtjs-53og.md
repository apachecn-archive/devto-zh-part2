# Vue 的 Nuxt.js 中的异步数据选项

> 原文：<https://dev.to/kball/async-data-options-in-vues-nuxtjs-53og>

编写“通用 JavaScript”应用程序有很多好处——这些应用程序在服务器上呈现完整的页面，但在页面加载后“合成”一个单页面应用程序。这些应用程序具有服务器渲染应用程序的所有 SEO 和初始页面加载速度优势，并结合了 SPA 的流动性和强大功能。

为了*实现*预渲染的这些好处，你需要确保你的服务器端渲染在渲染之前有它需要的所有数据。这对于静态页面来说是微不足道的，但是对于依赖 API 调用的动态应用程序来说，您需要确保在呈现页面并从服务器发送之前获取所有关键数据。

今天，我想在 [Nuxt.js](https://nuxtjs.org/) 中分解为这种类型的异步数据获取提供的钩子，这是一个构建在 Vue.js 之上的强大的通用 JavaScript 框架。

### 为什么我们需要特殊的挂钩？

在我们开始之前，让我们快速问一下为什么我们需要特殊的钩子。在典型的现代 SPA 应用程序中，无论是使用 Vue 还是 React 构建，数据都是异步获取的，通常由组件本身的生命周期挂钩触发。页面或组件将以空状态呈现，启动 API 请求以获取数据，然后在数据到达时重新呈现/更新。

这在服务器端呈现环境中的问题是它是不确定的。空状态与非空状态一样有效，因此服务器可以简单地将空状态提供给用户。

这不会破坏用户体验，在他们获得空状态后，组件将在客户端上水合，获取更多数据，并同样呈现。但它否定了服务器端渲染的大部分好处，服务器端渲染的全部目的是在第一次页面加载时发送完整的体验。

### Nuxt.js 钩子用于异步数据

Nuxt.js 有三个不同的钩子，专门为这种类型的“异步”数据获取而设计:

1.  **[【nuxtServerInit】](https://nuxtjs.org/guide/vuex-store#the-nuxtserverinit-action)**:用于预填充 VueX 存储——对任何页面调用
2.  **[fetch](https://nuxtjs.org/guide/vuex-store#the-fetch-method)** :用于用数据预填充 VueX 存储，从页面内调用。
3.  **[asyncData](https://nuxtjs.org/guide/async-data)** :用于用同步数据填充页面的`data`对象。

还可以以异步方式利用[中间件](https://nuxtjs.org/guide/routing#middleware)，这意味着您可以使用它来填充 VueX 存储。

Nuxt 文档提供了这些相互关系的可视化图表:

[![](img/36b4f00d7726c1fe6a6dd2438363acf1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kCGgZL5r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zendev.com/assets/img/posts/nuxt-async-hooks/nuxt-schema.png)

根据这个图，钩子按以下顺序出现:`nuxtServerInit`、`middleware`，然后是`fetch`、`asyncData`。让我们按顺序分解细节。

### nuxtServerInit

这是 Nuxt 插入到它的初始化过程中的一个钩子，用于填充应该一直存在的 VueX 存储数据。它仅在服务器上被调用，用于填充每次页面加载时应该在*上的商店数据。*

它的工作方式是，如果您的主存储索引已经将`nuxtServerInit`定义为一个动作，它将在任何中间件或其他页面初始化之前运行。它可以是同步的，也可以是异步的；如果它返回一个承诺，Nuxt 将等待该承诺解决后再继续。

例如，我们可以用它来填充当前的用户数据:

```
// store/index.js
actions: {
  nuxtServerInit ({ commit}, { req }) {
    if (req.session.user) {
      commit('setUser', req.session.user);
      return axios.get(`/users/${req.session.user}`).then((response) =>{
        commit('currentUserData', response.data);
      })
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:`nuxtServerInit`只在你的主商店中被调用，所以如果你在你的商店中使用模块(如果你有任何稍微复杂的应用程序，你可能就是)，你将需要从那里链接任何设置。例如，如果我想初始化用户模块和新闻模块中的内容，我可能会这样做:

```
// store/index.js
actions: {
  nuxtServerInit ({ dispatch }, context) {
    return Promise.all([
      dispatch('user/nuxtServerInit', context),
      dispatch('news/nuxtServerInit', context)
    ]);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 中间件

中间件允许您定义在呈现一个页面或一组页面之前运行的自定义函数。它可以用来保护页面或布局，例如通过检查用户是否被授权查看它们，但是它也可以用来获取异步数据。它不能直接访问页面，因为该页面还没有呈现，但是它可以填充商店。

中间件的一个好处是它是可重用的——你可以将同一个中间件应用于多个页面，或者共享一个布局的整个组。这使得它成为一个非常好的地方来放置跨一组页面共享的数据预加载，而不是像`nuxtServerInit`那样跨应用程序全局共享。

使用中间件的缺点是，当它运行时，还不能保证页面会呈现出来。后来的中间件或 [validate](https://nuxtjs.org/guide/routing/#validate-route-params) 钩子可能仍然会阻止页面的呈现，所以如果你的 API 调用非常昂贵，你可能想把它们留到生命周期的后期。

作为我们如何使用中间件的一个例子，让我们想象一下，每当用户在他们的“帐户”区域时，我们想要为他们预加载一组设置。这可能看起来像:

```
// layouts/account.vue
export default {
  middleware: ['preload-settings']
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// middleware/preload-settings.js
export default function ({ store }) {
  if (store.state.settings.settings.length === 0) {
    return store.dispatch('settings/loadSettings');
  }
  return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个中间件检查 VueX 存储的设置模块中的`settings`值是否为空。如果是，它会调度一个操作来填充它，如果不是，它会简单地返回 true。

只要该动作返回一个承诺，通过返回调度的结果，我们的中间件也返回一个承诺。Nuxt 将在继续之前等待该承诺得到解决，因此该动作可以在呈现之前填充我们的存储。

### 取

`fetch`钩子也用于在渲染前初始化 VueX 存储，但它不是全局应用于每个页面，而是特定于页面的。如果在布局或子页面组件上定义，则不会调用它，只能在页面内调用。

在一个页面组件上定义的一个`fetch`方法将在所有中间件已经运行并且验证已经清除之后被调用，所以当它运行时，我们确定这个页面将被呈现。这使得它非常适合于获取页面呈现所需的昂贵数据，但您不希望以推测的方式获取。

有一点需要注意:尽管它是在页面组件中定义的，但它是在该组件完全初始化之前被调用的，因此它不能访问该组件的数据、计算属性等。实际上，`this`根本不会指代组件。相反，`fetch`方法被传递给了`context`对象，因此您可以访问商店和其他所需的功能。

使用 fetch 方法将特定产品的信息提取到商店的示例:

```
// pages/products/_id.vue
export default {
  fetch(({ store, params }) {
    if (typeof (store.state.products.byId[params.id]) === 'undefined') {
      return store.dispatch('products/loadProduct', {id: params.id});
    }
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

### asyncData

到目前为止，我们讨论的所有机制都集中在将数据填充到 VueX 存储中。但是有时你不需要(或者不想要)存储，你只想把数据放入你的组件的数据对象。

Nuxt 也用`asyncData`钩子覆盖了这里，至少在页面组件中。这个钩子将在呈现页面之前被调用，它返回的对象将与组件的`data`对象合并。

例如，如果出于某种原因，我们不想使用前面示例中的 VueX 存储来保存产品数据——也许我们想确保它始终是 100%最新的，因此每次查看产品页面时都要重新提取它。我们可以这样实现它:

```
// pages/products/_id.vue
export default {
  asyncData(context) {
    return axios.get(`https://my-api-server/api/products/${params.id}, (response) => {
      return { product: response.data };
    });
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

与`fetch`类似，`asyncData`在组件被完全初始化之前被调用，所以它不能访问组件实例本身，并通过`context`对象来访问它需要的任何应用级信息。

现在我们有了。Nuxt.js 提供了 4 种在渲染前填充异步数据的机制。

* * *

附:如果你对这类话题感兴趣，你或许应该[在 Twitter 上关注我](https://twitter.com/kbal11)或者加入我的邮件列表。我发出一份名为“[周五前端](https://zendev.com/friday-frontend.html)”的每周简讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T4】