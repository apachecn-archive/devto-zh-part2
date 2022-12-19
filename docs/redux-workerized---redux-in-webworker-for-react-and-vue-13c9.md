# redux-workerezed-React 和 Vue 的 WebWorker 中的 Redux

> 原文：<https://dev.to/mizchi/redux-workerized---redux-in-webworker-for-react-and-vue-13c9>

我在 worker 中制作了使用 redux 的实验模块。

[米脂/还原-工作化](https://github.com/mizchi/redux-workerized)

*   类型脚本友好的 API
*   Redux。储存在工人中
*   React Hooks API 和 Vue 插件

共享存储示例

[![](../Images/cd73bd2645dfe14a9c42afbdbceb0a8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--npULL66x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.gyazo.com/d35c88597e72590b3e73d8618f26c061.gif)

[![](../Images/decff513f7afb337568a9482d35463e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qz2ub6fa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.gyazo.com/f946435f53d2cf0c4f875676ec23f84b.png)

## 代码

完整代码在这里[https://github . com/miz chi/redux-workerezed/tree/master/examples/micro front](https://github.com/mizchi/redux-workerized/tree/master/examples/microfront)

```
import "@babel/polyfill";
import { RootState, increment, Increment, INCREMENT } from "./reducer";

const worker = new Worker("./worker.ts");

// React

import React, { useCallback } from "react";
import ReactDOM from "react-dom";
import { createWorkerContext } from "redux-workerize/react";

const { WorkerContext, useSnapshot, useDispatch, ready } = createWorkerContext<
  RootState,
  { value: number }
>(worker, async (state: RootState) => {
  return state.counter;
});

function CounterApp() {
  const value = useSnapshot(state => state.value);
  const dispatch = useDispatch<Increment>();

  const onClick = useCallback(() => {
    dispatch(increment());
  }, []);

  return <button onClick={onClick}>{value}</button>; }

ready.then(() => {
  ReactDOM.render(
    <WorkerContext>
      <CounterApp />
    </WorkerContext>,
    document.querySelector(".react-root")
  );
});

// Vue
import Vue from "vue";
import Vuex from "vuex";
import App from "./App.vue";
import { workerPlugin, proxy, SYNC } from "redux-workerize/vue";

Vue.use(Vuex);

type CounterSnapshot = {
  value: number;
};

export type State = {
  remote: CounterSnapshot;
};

const store = new Vuex.Store<State>({
  state: {
    remote: {
      value: 0
    }
  },
  mutations: {
    [SYNC](state, payload: CounterSnapshot) {
      state.remote = { ...state.remote, ...payload };
    },
    ...proxy([INCREMENT])
  },
  plugins: [
    workerPlugin(
      worker,
      (state: RootState): CounterSnapshot => {
        return state.counter;
      }
    )
  ]
});

new Vue({
  store,
  el: ".vue-root",
  render(h) {
    return h(App);
  }
}); 
```

注意:需要[react @ 16 . 7 . 0-α0](mailto:react@16.7.0-alpha.0)[react-DOM @ 16 . 7 . 0-α0](mailto:react-dom@16.7.0-alpha.0)

Vuex 用户远程还原。存储快照

## 职工一方

```
import * as Comlink from "comlinkjs";
import { createStore } from "redux";
import reducer from "./reducer";
import { createWorkerizedStore } from "redux-workerized/worker";

const store = createStore(reducer);
const storeAPI = createWorkerizedStore(store);

Comlink.expose({ ...storeAPI }, self); 
```

## 我想做什么

*   对于脱离主线程的性能
*   存储在 Woker 中的是编写通用 javascript 的训练轮。