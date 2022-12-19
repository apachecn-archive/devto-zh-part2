# 热模块重新加载时处理冗余可观察间隔

> 原文：<https://dev.to/terrierscript/stop-redux-observable-interval-when-hot-module-reload-1goo>

在 redux-observable 史诗中使用`interval`的情况下，当重新加载 HMR 工作时，该观察者不会被移除。

这种现象并不明显，但感觉很糟糕。

# 解决方法:用`module.hot.dispose`使主体着火并停止

仅供参考:如果使用 typescript，@types/webpack-env 包含了`module.hot`类型。

```
import { combineEpics } from "redux-observable"
import { interval, Subject } from "rxjs"
import { map, takeUntil } from "rxjs/operators"

const disposer = new Subject() // Setup subject for dispose.

export const timerEpic = () => {
  return interval(1000).pipe(
    takeUntil(disposer), // stop when dispose subscribed.
    map((time) => ({
      type: "TIMER",
      value: time
    }))
  )
}

if (module.hot) {
  module.hot.dispose((data) => {
    disposer.next() // send dispose when fire HMR dispose event
  })
} 
```

### 分割 HMR 部分

你可以像这样分割相关的 HMR 代码。

```
// hotReload.ts
import { Subject } from "rxjs"
import { takeUntil } from "rxjs/operators"

const hmrDisposer = new Subject()

export const registerDisposeHandler = (module) => {
  if (module.hot) {
    module.hot.dispose(() => hmrDisposer.next())
  }
}

export const takeUntilHotReload = () => takeUntil(hmrDisposer) 
```

注意，您需要在 rootEpic 文件上调用 dispose hook。

```
export const rootEpic = combineEpics(pingEpic)

// DO WRITE with ROOT EPIC.
registerDisposeHandler(module) 
```