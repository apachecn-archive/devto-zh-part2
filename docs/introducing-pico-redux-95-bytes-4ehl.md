# 介绍 Pico Redux -更快更小的 Redux！

> 原文：<https://dev.to/joelnet/introducing-pico-redux-95-bytes-4ehl>

出于需要和好奇，我创建了 Redux 的最小可能实现，只有 95 字节(缩小)。

我经常需要一个没有那些华而不实的精简版。

Redux 本身已经是一个相当小的 1.6k (gzipped)的库，但是我真的在寻找一个只有单一函数重量的东西。于是 Pico Redux 诞生了。

核心 Redux 功能本身非常少。这就是您重新创建`createStore` :
所需的全部内容

```
const createStore = (reducer, state = reducer(void 0, { type: '@@init' })) => ({
  dispatch: action => (state = reducer(state, action), action),
  getState: () => state
}) 
```

Enter fullscreen mode Exit fullscreen mode

我通常不需要中间件(除非我在 React-land)。我也很少需要订阅功能。一般只要基本的店就够好了。

但是因为我确实偶尔会使用这些特性，所以我用模块化设计创建了 Pico Redux，所以仍然可以添加这些特性...但是只有在必要的时候，你才需要为你使用的东西付费。

```
// basic store
const store = createStore(reducer)

// store with subscribe
const store = withSubscribe(createStore)(reducer)

// store with middleware
const store = withMiddleware(createStore)(reducer)

// kitchen sink
const store = withMiddleware(withSubscribe(createStore))(reducer) 
```

Enter fullscreen mode Exit fullscreen mode

该接口与 Redux 保持一致，因此您应该已经熟悉如何使用它:

```
import { createStore } from 'pico-redux'

const init = 0

const reducer = (state = init, { type, value }) =>
  type === 'INC' ? state + value : state

const store = createStore(reducer)

store.dispatch({ type: 'INC', value: 100 })
store.getState() //=> 100 
```

Enter fullscreen mode Exit fullscreen mode

尝试一下，给它一个旋转，让我知道你的想法！

github:[https://github.com/joelnet/pico-redux](https://github.com/joelnet/pico-redux)

灵感来源: [Redux:从零开始实现商店——丹·阿布拉莫夫](https://egghead.io/lessons/react-redux-implementing-store-from-scratch)

我目前可以兼职合同工作(C#，JavaScript，反应)。在推特或 T2【LinkedIn】上给我打电话联系我。

我的文章展示了对函数式 JavaScript 的热爱。如果你需要更多的 FP，在这里或者 Twitter 上关注我 [@joelnet](https://twitter.com/joelnet) ！

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)