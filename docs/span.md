# 一看到 Nuxt.js 的手册就能马上理解 Vuex 的 Store

> 原文::1230【https://dev . to/dala 00/vuexstorenuxtjs-2458】

对于所有最近学习了 JavaScript 框架的人来说，即使马上就能理解 Vue.js 等，有没有因为 Flux 的概念和 Vuex 的 Store 等而变得不懂呢？ 在这种情况下，如果一边触摸 Nuxt.js 一边看 Nuxt.js 的手册，就会非常简洁容易理解。

具体为以下页面。 参考这里进行说明。 如果想先看链接的说明，我想看“模块模式”更容易理解。 本文的内容也以那里为基础进行。

[Vuex ストア - Nuxt.js](https://ja.nuxtjs.org/guide/vuex-store/)

## 到底有什么用呢？

Store 用于管理整个 APP 应用程序的状态。 具体来说，我觉得登录的用户的信息等最容易理解。

## 具体怎么用

Nuxt.js 时，首先在`store/index.js`中进行状态的定义。

```
export const state = () => ({
  user: null
}) 
```

Enter fullscreen mode Exit fullscreen mode

另外，在下面写上用于更新上述状态的 mutation 处理。

```
export const mutations = {
  login(state, user) {
    state.user = user
  },
  logout(state) {
    state.user = null
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果制作上述那样的文件，Nuxt.js 会自动读取。

然后在适当的某个组件内调用上述 mutation 就可以更新状态。

```
this.$store.commit('login', user) 
```

Enter fullscreen mode Exit fullscreen mode

想在组件内参照时如下所示。

```
<span>{{ $store.state.user.name }}</span> 
```

Enter fullscreen mode Exit fullscreen mode

非常简单呢。

## 有什么好处？

我想最大的好处就是可以从 APP 应用程序上的任何地方访问。 在本示例中，您可以访问用户信息，而无需通过属性逐个将用户信息传递给下层组件。

例如，在不使用 Store 的情况下，假设有全体的`layout.vue`，使其中持有用户信息，则必须从那里向`TheHeaderNavi`属性地传递用户信息，进而向其中的`TheLoginButton`、进而向其中的`TheLoginModal`传递用户信息

而且用 TheLoginModal 登录后，必须重复接收用`@logined`这样的事件登录的信息的桶树直到回到最初为止。 而且，如果在其他页面上的画面中也有想显示用户信息的地方，就必须全部同样进行。 非常麻烦，快疯了。

如果是 Store 的话，这些都不需要。 因为开发者的负担完全不同，所以我认为除了超小规模 APP 以外几乎都是必须的。

## Nuxt.js 的情况下文件划分也非常简单

我想不仅仅是用户信息，例如在聊天页面上想把发言一览、在地图页面上想把地图上的标记信息一览等放在 Store 里的地方也会出现。 在这种情况下，在刚才介绍的手册的页面上进行了说明，根据用途分开文件在 Nuxt.js 中也非常简单。

具体怎么办，真的只是把文件分开定义。 例如，如果制作`store/chat.js`，则参照时为

```
<div v-for="post in $store.state.chat.posts" :key="post.id">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这样的感觉，commit 是

```
this.$store.commit('chat/add', post) 
```

Enter fullscreen mode Exit fullscreen mode

这样的感觉，非常简单。

## 与 React 的 Redux 相比？

我想用过 react redux 的人都知道，但是相当麻烦(尽管如此，比起用属性串起来还是容易得多)。 很久以前就写了同样的报道。 虽然在那里只写了概念，但是实际安装起来很麻烦，管理也很麻烦。

[在 React 中组合 Redux 有什么好的呢](https://crieit.net/posts/React-Redux)

与此相比，变成了 Nuxt.js 的手册，我觉得变得很容易理解。 如果是 Redux 的话，其他的 actions、reducer 等文件数量也很多，我觉得这是很麻烦的部分。 Nuxt.js 在刚才的说明中只出现了 state 和 mutations，是最小限度的实现，真是简单明快。

当然，我认为为了这个简单反而有所牺牲的部分，即便如此，个人想做很多东西的时候，这些就足够了，可以很快地推进事情。

## 总结

总之，从这个 Nuxt.js 的 Store 开始进一步理解的话，很简单易懂。 大概读了这里稍微了解一下的话，在 Vuex 和 React 上使用 Store 的时候也会稍微容易理解。

如果有至今为止不太理解的人的话，请一定要趁此机会再确认一次。