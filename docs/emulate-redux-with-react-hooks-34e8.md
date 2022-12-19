# 用 React 钩子模拟 Redux

> 原文：<https://dev.to/terrierscript/emulate-redux-with-react-hooks-34e8>

**注意:React Hooks 是 RFC。本文是实验性的**

# 1。将 combineReducers 与 useReducer 一起使用

我们可以在 redux combineReducer 中创建嵌套的 Reducer，我尝试将嵌套的 reducer 和`useReducer`钩子结合起来。

```
import { combineReducers } from "redux"

const counter = (state = 0, action) => {
  switch (action.type) {
    case "INCREMENT":
      return state + 1
    case "DECREMENT":
      return state - 1
  }
  return state
}

const inputValue = (state = "foo", action) => {
  switch (action.type) {
    case "UPDATE_VALUE":
      return action.value
  }
  return state
}

export const rootReducer = combineReducers({
  counter,
  // nest
  someNested: combineReducers({
    inputValue
  })
}) 
```

并创建组件

```
import React, { useReducer } from "react"

const App = () => {
  const [state, dispatch] = useReducer(rootReducer, undefined, {
    type: "DUMMY_INIT"
  })

  return (
    <div className="App">
      <div>
        <h1>counter</h1>
        <div>count: {state.counter}</div>
        <button onClick={(e) => dispatch({ type: "INCREMENT" })}>+</button>
        <button onClick={(e) => dispatch({ type: "DECREMENT" })}>-</button>
      </div>
      <div>
        <h1>Input value</h1>
        <div>value: {state.someNested.inputValue}</div>
        <input
          value={state.someNested.inputValue}
          onChange={(e) =>
            dispatch({
              type: "UPDATE_VALUE",
              value: e.target.value
            })
          }
        />
      </div>
    </div>
  )
} 
```

通过虚拟初始状态(=未定义)和任何虚拟动作，我都可以得到好的结果。

```
const [state, dispatch] = useReducer(rootReducer, undefined, {
  type: "DUMMY_INIT"
}) 
```

# 2:用 createContext 和 useContext 创建提供者

我们可以避免用上下文传递道具。

[https://react js . org/docs/hooks-FAQ . html # how-to-avoid-passing-callbacks-down](https://reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down)

```
const ReducerContext = createContext()

// Wrap Context.Provider
const Provider = ({ children }) => {
  const [state, dispatch] = useReducer(rootReducer, undefined, {
    type: "DUMMY_INIT"
  })
  return (
    <ReducerContext.Provider value={{ state, dispatch }}>
      {children}
    </ReducerContext.Provider>
  )
}

const App = () => {
  return (
    <Provider>
      <div className="App">
        <Counter />
        <InputValue />
      </div>
    </Provider>
  )
} 
```

对于消费者，我们可以使用`useContext`

```
const Counter = () => {
  const { state, dispatch } = useContext(ReducerContext)
  return (
    <div>
      <h1>counter</h1>
      <div>count: {state.counter}</div>
      <button onClick={(e) => dispatch({ type: "INCREMENT" })}>+</button>
      <button onClick={(e) => dispatch({ type: "DECREMENT" })}>-</button>
    </div>
  )
}
const InputValue = () => {
  const { state, dispatch } = useContext(ReducerContext)
  return (
    <div>
      <h1>Input value</h1>
      <div>value: {state.someNested.inputValue}</div>
      <input
        value={state.someNested.inputValue}
        onChange={(e) =>
          dispatch({
            type: "UPDATE_VALUE",
            value: e.target.value
          })
        }
      />
    </div>
  )
} 
```

如果你想用`<Consumer>`，像这样。

```
 const Counter = () => {
  return (
    <ReducerContext.Consumer>
      {({ state, dispatch }) => {
        return (
          <div>
            <h1>counter</h1>
            <div>count: {state.counter}</div>
            <button onClick={(e) => dispatch({ type: "INCREMENT" })}>+</button>
            <button onClick={(e) => dispatch({ type: "DECREMENT" })}>-</button>
          </div>
        )
      }}
    </ReducerContext.Consumer>
  )
} 
```

# 3。使用 useCallback 模拟 bindActionCreactors

如果我们想要绑定动作，我们可以使用 useCallback

```
 const increment = useCallback((e) => dispatch({ type: "INCREMENT" }), [
    dispatch
  ])
  const decrement = useCallback((e) => dispatch({ type: "DECREMENT" }), [
    dispatch
  ])
  const updateValue = useCallback(
    (e) =>
      dispatch({
        type: "UPDATE_VALUE",
        value: e.target.value
      }),
    [dispatch]
  )
  return <div>
   :
    <button onClick={increment}>+</button>
    <button onClick={decrement}>-</button>
   :
  </div> 
```

## 4。模拟 mapStateToProps 并使用 useMemo 重新选择

```
const InputValue = () => {
  const { state, dispatch } = useContext(ReducerContext)
  // memolized. revoke if change state.someNested.inputValue 
  const inputValue = useMemo(() => state.someNested.inputValue, [
    state.someNested.inputValue
  ])

  return (
    <div>
      <h1>Input foo</h1>
      <div>foo: {inputValue}</div>
      <input
        value={inputValue}
        onChange={(e) =>
          dispatch({
            type: "UPDATE_VALUE",
            value: e.target.value
          })
        }
      />
    </div>
  )
} 
```

# 5。模拟容器

```
 const useCounterContext = () => {
  const { state, dispatch } = useContext(ReducerContext)
  const counter = useMemo(() => state.counter, [state.counter])
  const increment = useCallback(
    (e) => setTimeout(() => dispatch({ type: "INCREMENT" }), 500),
    [dispatch]
  )
  const decrement = useCallback((e) => dispatch({ type: "DECREMENT" }), [
    dispatch
  ])

  return { counter, increment, decrement }
}

const Counter = () => {
  const { counter, increment, decrement } = useCounterContext()
  return (
    <div>
      <h1>counter</h1>
      <div>count: {counter}</div>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  )
}

// 
const useInputContainer = () => {
  const { state, dispatch } = useContext(ReducerContext)
  // memolized action dispatcher
  const updateValue = useCallback(
    (e) =>
      dispatch({
        type: "UPDATE_VALUE",
        value: e.target.value
      }),
    [dispatch]
  )
  // memolized value
  const inputValue = useMemo(() => state.someNested.inputValue, [
    state.someNested.inputValue
  ])
  return {
    updateValue, inputValue
  }
}

const InputValue = () => {
  const { updateValue, inputValue } = useInputContainer()
  return (
    <div>
      <h1>Input foo</h1>
      <div>value: {inputValue}</div>
      <input value={inputValue} onChange={updateValue} />
    </div>
  )
} 
```

# 示例代码

[https://stackblitz.com/edit/github-hgrund?file=src/App.js](https://stackblitz.com/edit/github-hgrund?file=src/App.js)

# 额外:中间件

## Extra-1:异步获取

我们可以用`useEffect`、**、[来模拟中间件，但这可能不被推荐，我们等待 Suspence](https://mobile.twitter.com/dan_abramov/status/1057011888335347713) 、**

* * *

减速器

```
const fetchedData = (state = {}, action) => {
  switch (action.type) {
    case "FETCH_DATA":
      return action.value
  }
  return state
} 
```

我们创建异步函数返回随机值。

```
const fetchData = (dispatch) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ random: Math.random() })
    }, 100)
  })
  // Really:
  // return fetch("./async.json")
  //   .then((res) => res.json())
  //   .then((data) => {
  //     return data
  //   })
} 
```

容器:
我们希望将`useEffect`传递给空数组(`[]`)。
[https://react js . org/docs/hooks-effect . html # tip-optimizing-performance-by-skipping-effects](https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)

```
const useFetchDataContainer = () => {
  const { state, dispatch } = useContext(ReducerContext)

  // call on mount only
  useEffect(() => {
    fetchData().then((data) => {
      dispatch({
        type: "FETCH_DATA",
        value: data
      })
    })
  }, [])

  const reload = useCallback(() => {
    fetchData().then((data) => {
      dispatch({ type: "FETCH_DATA", value: data })
    })
  })

  const data = useMemo(
    () => {
      return JSON.stringify(state.fetchedData, null, 2)
    },
    [state.fetchedData]
  )
  return { data, reload }
}

const FetchData = () => {
  const { data, reload } = useFetchDataContainer()
  return (
    <div>
      <h1>Fetch Data</h1>
      <pre>
        <code>{data}</code>
      </pre>
      <button onClick={reload}>Reload</button>
    </div>
  )
} 
```

## Extra-2:模拟定制中间件(如 applyMiddleware)

如果我们需要减速器中间件，我们可以包装减速器调度

```
// my custom middleware
const myMiddleware = (state, dispatch) => {
  return (action) => {
    if (action.type == "OOPS") { // fire action when `OOPS` action.
      dispatch({ type: "SET_COUNT", value: state.counter + 100 })
    }
  }
}

const useEnhancedReducer = (reducer, enhancer) => {
  const [state, baseDispatch] = useReducer(reducer, undefined, {
    type: "DUMMY_INIT"
  })

  const next = useMemo(() => enhancer(state, baseDispatch), [
    state,
    baseDispatch
  ])

  // wrapped dispatch
  const dispatch = useCallback((action) => {
    baseDispatch(action)
    next(action)
  })

  return { state, dispatch }
}

const Provider = ({ children }) => {
  const { state, dispatch } = useEnhancedReducer(rootReducer, myMiddleware)
  const value = { state, dispatch }
  return (
    <ReducerContext.Provider value={value}>{children}</ReducerContext.Provider>
  )
} 
```