# redux-observable 初学者指南

> 原文：<https://dev.to/bnevilleoneill/a-beginners-guide-to-redux-observable-234e>

[![](../Images/68e3b47073a30a99df05a1d7e1e78800.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pex-cfcY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYWr-aGD5v0_Ca6YyDAlF2A.png)

[Redux-Observable](https://www.npmjs.com/package/redux-observable) 是一个 [Redux 中间件](https://www.codementor.io/vkarpov/beginner-s-guide-to-redux-middleware-du107uyud)，它允许你使用 [RxJS 操作符](https://www.npmjs.com/package/rxjs)过滤和映射动作。像 [filter()](https://www.learnrxjs.io/operators/filtering/filter.html) 和 [map()](https://www.learnrxjs.io/operators/transformation/map.html) 这样的 RxJS 操作符让您转换动作流，就像 [JavaScript 的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)[array . prototype . filter()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)让您转换数组一样。

在本文中，我将向您展示如何使用可以从 Node.js 运行的脚本开始使用 redux-observable。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 你的第一部史诗

在 redux-observable 中，一个 [" **史诗** "](https://redux-observable.js.org/docs/basics/Epics.html) 是一个接受动作流并返回修改后的动作流的函数。你可以把 epic 看作是 redux-observable 应该调度的附加动作的描述。史诗的概念类似于 redux-saga 中的[【传奇】。](http://thecodebarbarian.com/redux-saga-vs-async-await.html)

在你写第一部史诗之前，你需要[安装 redux-observable](https://www.npmjs.com/package/redux-observable#install) 。本文假设您已经安装了 Node.js 和 npm。要安装 redux-observable 以及 [redux](https://www.npmjs.com/package/redux) 和 RxJS，运行下面的命令:

```
install redux@4.x redux-observable@1.x rxjs@6.x 
```

redux-observable API 中最基本的函数是 [createEpicMiddleware()函数](https://redux-observable.js.org/docs/api/createEpicMiddleware.html)。这个函数创建了实际的 Redux 中间件，你应该把它传递给 [Redux 的](https://redux.js.org/api/applymiddleware) [applyMiddleware()函数](https://redux.js.org/api/applymiddleware)。

下面是一个如何创建中间件的例子，该中间件将“CLICK_INCREMENT”类型的动作转换为“INCREMENT”类型的动作:

```
const { createEpicMiddleware } = require('redux-observable');
const { filter, map } = require('rxjs/operators');
const redux = require('redux');

// An 'epic' takes a single parameter, `action$`, which is an RxJS observable
// that represents the stream of all actions going through Redux
const countEpic = action$ => action$.pipe(
  filter(action => action.type === 'CLICK_INCREMENT'),
  map(action => {
    return { type: 'INCREMENT', amount: 1 };
  })
);

const observableMiddleware = createEpicMiddleware();
const store = redux.createStore(reducer, redux.applyMiddleware(observableMiddleware));

// **Must** add the epic to the observable after calling `applyMiddleware()`.
// Otherwise you'll get a warning: "epicMiddleware.run(rootEpic) called before
// the middleware has been setup by redux. Provide the epicMiddleware instance
// to createStore() first"
observableMiddleware.run(countEpic);

// Sample Redux reducer
function reducer(state = 0, action) {
  console.log('Action', action);

  switch (action.type) {
    case 'INCREMENT':
      return state + action.amount;
    default:
      return state;
  }
} 
```

假设您将一个类型为“CLICK_INCREMENT”的动作分派给上述商店，如下所示:

```
store.dispatch({ type: 'CLICK_INCREMENT' }); 
```

您的 filter()和 map()调用将运行，redux-observable 将分派一个“INCREMENT”类型的附加操作。

下面是 reducer()函数中 console.log()语句的输出:

```
{ type: '@@redux/INIT7.2.m.z.p.l' }
Action { type: 'CLICK_INCREMENT' }
Action { type: 'INCREMENT', amount: 1 } 
```

请注意，redux-observable 调度了一个额外的操作。“CLICK_INCREMENT”动作仍然会传递到减速器。默认情况下，史诗将动作添加到流中。

### 异步调度

上面显示的例子是一个简单的介绍，但是并没有抓住为什么你首先要使用 redux-observable。

redux-observable 之所以如此有趣，是因为它能够使用[RxJS’](https://www.learnrxjs.io/operators/transformation/mergemap.html)[merge map()函数](https://www.learnrxjs.io/operators/transformation/mergemap.html)来处理异步函数。换句话说，redux-observable 是 [redux-saga](http://thecodebarbarian.com/redux-saga-vs-async-await.html) 和 [redux-thunk](http://thecodebarbarian.com/async-await-with-react-and-redux-thunk.html) 的可行替代方案。

下面是一个如何通过简单的[异步函数](http://thecodebarbarian.com/common-async-await-design-patterns-in-node.js.html) :
使用 redux-observable 的例子

```
const { createEpicMiddleware } = require('redux-observable');
const { filter, mergeMap } = require('rxjs/operators');
const redux = require('redux');

const startTime = Date.now();

const countEpic = action$ => action$.pipe(
  filter(action => action.type === 'CLICK_INCREMENT'),
  // `mergeMap()` supports functions that return promises, as well as observables
  mergeMap(async (action) => {
    await new Promise(resolve => setTimeout(resolve, 1000));
    return { type: 'INCREMENT', amount: 1 };
  })
);

const observableMiddleware = createEpicMiddleware();
const store = redux.createStore(reducer, redux.applyMiddleware(observableMiddleware));

observableMiddleware.run(countEpic);

// Sample Redux reducer
function reducer(state = 0, action) {
  console.log(`+${Date.now() - startTime}ms`, action);

  switch (action.type) {
    case 'INCREMENT':
      return state + action.amount;
    default:
      return state;
  }
}

store.dispatch({ type: 'CLICK_INCREMENT' }); 
```

countEpic()现在将在分派“增量”动作之前等待大约 1 秒:

```
+1ms { type: '@@redux/INIT7.i.8.v.i.t' }
+7ms { type: 'CLICK_INCREMENT' }
+1012ms { type: 'INCREMENT', amount: 1 } 
```

如果你读过 [*掌握异步/等待*](http://thecodebarbarian.com/new-ebook-mastering-async-await) ，你就知道这不是支持异步/等待的全部。如果你的异步函数出错了会怎么样？下面的 countEpic()会崩溃:

```
const countEpic = action$ => action$.pipe(
  filter(action => action.type === 'CLICK_INCREMENT'),
  mergeMap(async () => {
    throw new Error('Oops!');
  })
); 
```

为了处理错误，你应该总是在你的 epic 的末尾加上一个 [RxJS](https://www.learnrxjs.io/operators/error_handling/catch.html) [catchError()](https://www.learnrxjs.io/operators/error_handling/catch.html) ，如下所示:

```
const { createEpicMiddleware } = require('redux-observable');
const { catchError, filter, mergeMap } = require('rxjs/operators');
const redux = require('redux');

const startTime = Date.now();

const countEpic = action$ => action$.pipe(
  filter(action => action.type === 'CLICK_INCREMENT'),
  mergeMap(async () => {
    throw new Error('Oops!');
  }),
  catchError(err => Promise.resolve({ type: 'Error', message: err.message }))
); 
```

countEpic()现在将分派一个“错误”类型的操作，并显示错误消息:

```
+1ms { type: '@@redux/INIT0.a.g.q.3.o' }
+6ms { type: 'CLICK_INCREMENT' }
+8ms { type: 'Error', message: 'Oops!' } 
```

### 发出 HTTP 请求

上面的例子很简单，但是不太现实。让我们将 redux-observable 用于一个更现实的用例:使用 [node-fetch](https://www.npmjs.com/package/node-fetch) 发出一个 HTTP 请求，从 [IEX API](https://iextrading.com/developer/) 获取当前的 MongoDB 股票价格。要获得股票价格，您需要向以下 URL 发出 get 请求:

```
://api.iextrading.com/1.0/stock/MDB/price 
```

因为可以将 async/await 与 mergeMap()一起使用，所以使用 redux-observable 发出 HTTP 请求类似于异步调度示例。 [Node-fetch 返回一个承诺](https://www.npmjs.com/package/node-fetch#usage)，所以您可以等待一个 HTTP 请求，然后用请求的结果分派一个新的动作。

在下面的代码中，每当“FETCH_STOCK_PRICE”类型的操作通过系统时，fetchEpic()向 IEX API 发出一个 GET 请求。如果请求成功，fetchEpic()将调度一个新的“FETCH_STOCK_PRICE_SUCCESS”类型的操作，股票价格:

```
const fetch = require('node-fetch');

// ...

const fetchEpic = action$ => action$.pipe(
  filter(action => action.type === 'FETCH_STOCK_PRICE'),
  mergeMap(async (action) => {
    const url = `https://api.iextrading.com/1.0/stock/${action.symbol}/price`;
    const price = await fetch(url).then(res => res.text());
    return Object.assign({}, action, { type: 'FETCH_STOCK_PRICE_SUCCESS', price });
  }),
  catchError(err => Promise.resolve({ type: 'FETCH_STOCK_PRICE_ERROR', message: err.message }))
); 
```

为了将 fetchEpic()粘合到 Redux，reducer(如下所示)存储了一个价格映射，它将股票符号映射到价格。为了在 Redux 中存储 MongoDB 的股票价格，reducer 监听“FETCH_STOCK_PRICE_SUCCESS”类型的动作，而不是“FETCH _ STOCK _ PRICE”:

```
// Sample Redux reducer
function reducer(state = { prices: {} }, action) {
  console.log(`+${Date.now() - startTime}ms`, action);

  switch (action.type) {
    case 'FETCH_STOCK_PRICE_SUCCESS':
      const prices = Object.assign({}, state.prices, { [action.symbol]: action.price });
      state = Object.assign({}, state, { prices });
      console.log('New state', state);
      return state;
    default:
      return state;
  }
}

store.dispatch({ type: 'FETCH_STOCK_PRICE', symbol: 'MDB' }); 
```

下面显示的是使用 fetchEpic()和 reducer()通过 Redux store 运行“FETCH_STOCK_PRICE”操作的示例输出。“FETCH_STOCK_PRICE”操作通过，fetchEpic()看到这个操作并发出一个 HTTP 请求。

当 fetchEpic()从 IEX API 得到响应时，它发出一个‘FETCH _ STOCK _ PRICE _ SUCCESS’动作，然后 reducer 更新状态:

```
+1ms { type: '@@redux/INITg.3.m.s.8.f.i' }
+5ms { type: 'FETCH_STOCK_PRICE', symbol: 'MDB' }
+198ms { type: 'FETCH_STOCK_PRICE_SUCCESS',
  symbol: 'MDB',
  price: '79.94' }
New state { prices: { MDB: '79.94' } } 
```

### 结论

Redux-observable 是一个用 React 和 Redux 处理[异步逻辑的工具。这很重要，因为 React 通常不支持异步函数。Redux-observable 是 redux-saga 和 redux-thunk 的一个有趣的替代品，特别是如果你已经有了 RxJS 的经验。所以下次你发现自己想要](http://thecodebarbarian.com/async-await-with-react-and-redux-thunk.html)[编写自己的承诺中间件](https://www.codementor.io/vkarpov/beginner-s-guide-to-redux-middleware-du107uyud)时，给 redux-observable 一个机会。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[减少可观测重叠的初学者指南](https://blog.logrocket.com/a-beginners-guide-to-redux-observable-c0381da8ed3a/)最先出现在[日志博客](https://blog.logrocket.com)上。