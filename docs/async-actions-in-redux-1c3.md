# redux 中的异步操作

> 原文：<https://dev.to/hemanth/async-actions-in-redux-1c3>

我在 redux 介绍后听到的最常见的问题是:“我如何在动作中获取一些数据？”

他们中的大多数会遇到这样的障碍:`Actions must be plain objects. Use custom middleware for async actions.`这是因为动作应该是普通的 JavaScript 对象，并且必须有一个`type`属性来指示正在执行的动作的类型。

让我们看一个简单的例子，让一个 API 请求说这个 [xkcd](http://xkcd-imgs.herokuapp.com/) comic API。

由于在处理异步动作方面还没有达成共识，而且有许多的库，这将使处理异步动作变得更容易，但是在下面的例子中，我们将采用普通的方法。

让我们从一个初始状态开始，看起来像:

```
const initialState = {
  loading: false,
  error: false,
  comic: null
} 
```

Enter fullscreen mode Exit fullscreen mode

一个`reducer`处理动作的`fetching`、`fetched`和`failed`状态。

```
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case 'FETCHING_COMIC':
      return {
        ...state,
        comic: action.comic
      }
    case 'FETCH_COMIC_SUCCESS':
      return {
        ...state,
        comic: action.comic
      }
    case 'FETCH_COMIC_FAILED':
      return {
        ...state,
        error: action.error
      }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

a `store`和`dispatch`基于流程:

```
 const store = Redux.createStore(reducer);

store.dispatch({
  type: 'FETCHING_COMIC'
})

fetch('https://xkcd-imgs.herokuapp.com/')
  .then(response => response.json())
  .then(comic => {
    store.dispatch({
      type: 'FETCH_COMIC_SUCCESS',
      comic
    })
  })
  .catch(error => store.dispatch({
    type: 'FETCH_COMIC_FAILED',
    error
  })) 
```

Enter fullscreen mode Exit fullscreen mode

一些强制性的`render`方法(这次没有反应过来；))

```
const render = function(state) {
    let xkcd = document.querySelector('#xkcd');
    xkcd.src = state.comic.url;
    xkcd.alt = state.comic.title;
  } 
```

Enter fullscreen mode Exit fullscreen mode

**工作代码:**

[https://repl.it/@hemanth1/async-actions-redux?lite=true](https://repl.it/@hemanth1/async-actions-redux?lite=true)

**一些有趣的讨论:**

> ![unknown tweet media content](img/d5131aab6baeda7907a65a71f3480d81.png)![Hemanth.HM profile image](img/89041879804205d5b4e6e7e6fa6a2f55.png)赫曼思。HM@ gnumanth![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)那么，向朋友解释 Redux 中的异步动作，你有什么建议？
> 
> 0。谈谈 Redux 的 Thunk/Promise 中间件？
> 1。像下面这样做一些更香草的东西？
> 2。中间件是强制性的情况？2018 年 2 月 15 日上午 09:35[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=964070760443604993)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=964070760443604993)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=964070760443604993)

页（page 的缩写）感谢`<GreenJello>`的快速回顾。

页（page 的缩写）附:这是来自 h3manth.com 的转贴