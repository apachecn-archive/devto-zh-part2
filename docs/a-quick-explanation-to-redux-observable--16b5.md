# Redux 可观测量的快速解释。

> 原文：<https://dev.to/papaponmx/a-quick-explanation-to-redux-observable--16b5>

## [T1】简介](#intro)

[链接到一个页面](https://gist.github.com/papaponmx/1b61ffebc0b49b7bedbf8e954cfb959e)中的所有代码。

作为一名前端开发人员，我非常熟悉 Redux，它是如何工作的，以及如何用 [`redux-saga`](https://github.com/redux-saga/redux-saga) 处理异步操作。

然而，在我的新工作中，我们使用 [`redux-observable`](https://github.com/redux-observable/redux-observable) 。我发现它很有趣，我的一些朋友问，因此，这里有一个关于它如何与 Redux 一起工作的简要说明。

**注意**:我假设你对 Redux 行话(动作创建者、减少者、存储)和反应感到舒服。

## 有可观测量的单向数据流。

魔术按以下顺序发生:

1.  组件是用从商店映射的道具来呈现的。
2.  某个动作由某个事件触发。这可以是 DOM 事件或生命周期方法，即`componentWillMount()`。
3.  动作由一个`reducer`过滤。与此同时，一些史诗听从并执行一些行动。这就是异步魔法发生的地方。在 epic 中，如果需要，我们可以调度一个新的动作。
4.  还原器反映返回一个新状态。

下面是一个快速示意图
[![alt text](img/545eea7819581cef2e55bc68a02a5d13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rjSyJRpQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s0qgjzrptdalvg4xkqkm.png)

它在代码中的样子。按照 [`redux-ducks`](https://github.com/erikras/ducks-modular-redux) 的模式，我会把所有东西放在同一个文件中。

```
// module/index.js
// Actions
...
// Reducer
...

// Action Creators
...

// Epics
const createEpic = action$ => {
 action$.ofType(FETCH_LIST_SUCCESS)
    .mergeMap(action =>
      ajax.getJSON(`${baseurl}/list`)
        .map(res => fetchUserSuccess(res.data))
    );
}; 
```

Enter fullscreen mode Exit fullscreen mode

按照顺序排列史诗逻辑和模块。下一步是将它添加到我们的 root reducer 和 epic 中。

```
import { combineEpics } from 'redux-observable';
import { combineReducers } from 'redux';
import list, { fetchListEpic } from './lists';

export const rootEpic = combineEpics(
  fetchUserEpic,
  // ...
);

export const rootReducer = combineReducers({
  // ...
  list,
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，`redux-observable`只是我们用来处理异步操作和副作用的一些中间件。最后一步是将其添加到我们的`configureStore.js`中。

```
import { createStore, applyMiddleware } from 'redux';
import { createEpicMiddleware } from 'redux-observable';
import { rootEpic, rootReducer } from './modules';

const epicMiddleware = createEpicMiddleware(rootEpic);

export default function configureStore() {
  const store = createStore(
    rootReducer,
    applyMiddleware(epicMiddleware)
  );

  return store;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你错过了，这里是一页中所有代码的[链接。](https://gist.github.com/papaponmx/1b61ffebc0b49b7bedbf8e954cfb959e)

这是所有的乡亲。