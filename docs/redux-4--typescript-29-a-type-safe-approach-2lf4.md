# Redux 4 + TypeScript:一种类型安全的方法

> 原文：<https://dev.to/resir014/redux-4--typescript-29-a-type-safe-approach-2lf4>

*这篇文章也发表在我的[个人网站](https://resir014.xyz/posts/2018/07/06/redux-4-plus-typescript/)上。*

即使当 JavaScript 社区对它的意见不一时，我还是忍不住继续使用 [Redux](https://redux.js.org/) 。它的不可变状态管理模式对我们来说太熟悉了，在构建大型应用程序时尤其有用。它的 TypeScript 支持也很出色，Redux 4 对其类型声明进行了急需的改进。

几个月前，我写了一篇关于它的指南，它受到了一定的关注。这些反馈帮助我取得了超出正常水平的进步，对此我感激不尽。

本着这种精神，我终于花时间根据我收到的反馈更新了该指南，用 React、Redux 和 TypeScript 的最新版本更新了所有内容，并引入了一些新的技巧。

请注意，以下指南是在以下设备上测试的:

*   `react@^16.4.0`
*   `redux@^4.0.0`
*   `react-redux@^6.0.0`
*   `typescript@^3.3.0`

## 我们在建造什么

为了演示这篇文章，我们将构建一个简单的应用程序。我们将创建一个网站，从 [OpenDota API](https://docs.opendota.com/) 提取数据，并显示某些英雄和职业球队的信息。这也将展示如何在支持 Redux 的应用中为每个功能/模块构建商店。

## TL；速度三角形定位法(dead reckoning)

如果你想直接跳到例子，我还在 GitHub 上发布了一个示例项目，基于我以前帖子的反馈。点击[这里](https://github.com/resir014/react-redux-typescript-example)去那里。

* * *

## 更新

*   **2018-12-08:** 更新`Dispatch`从`redux`而非`react-redux`进口。该指南现在也被测试用于 TypeScript `^3.0.0`。(感谢 [cancerberoSgx](https://github.com/resir014/react-redux-typescript-example/pull/2) ！)
*   **2019-01-05:** 因巴别塔不支持将`const enum` s 改为`enum` s。(感谢[凯尔吉伦](https://github.com/nextriot)！)
*   **2019-03-09:**`react-redux`的最新版本打破了我在这篇文章的上一个版本中提到的“儿童-道具-还原-容器”方法的类型。我建议现在不要使用这种模式，但是如果你仍然想使用它，我已经将本文中相应的部分升级为具有相同的模式，使用新引入的`ReactReduxContext`。
*   **2019-09-22:** 上述模式在一个 Create React 应用程序设置上被打破。现在使用内置的`react-redux`钩子重写了整个`LayoutContainer`。

* * *

## 目录结构

我跟你说实话，对我来说，开始使用 React + Redux 最困难的一步是弄清楚如何构建你的项目。实际上没有什么方法可以做到这一点，但正确处理这件事仍然很重要，这样就不会在以后的道路上造成更多的干扰。我通常是这样做的。

### 使用专用的`store/`目录

许多指南/项目在根目录`actions/`和`reducers/`中分别构建它们的存储，以模仿 Redux 架构中的模式。

*(注意，下面的目录树假设您的代码放在一个`src/`目录中。)*

```
.
|-- actions
|   |-- chat.ts
|   |-- index.ts
|   `-- layout.ts
|-- components
|   |-- Footer.tsx
|   `-- Header.tsx
|-- containers
|   `-- ChatWindow.tsx
|-- reducers
|   |-- chat.ts
|   |-- index.ts
|   `-- layout.ts
|-- ...
|-- index.tsx
`-- types.d.ts 
```

Enter fullscreen mode Exit fullscreen mode

然而，我个人觉得这很分散注意力。当您的代码库变得更大时，您最终会将共享相同上下文的代码分散在很长的目录树中，这对于想要快速浏览您的代码的新手来说不是很直观。因此，在这种情况下，粗略地遵循 Redux 模式在代码可读性方面并不那么有利。

所以我决定为我所有的 Redux 动作/reducer 专用一个`store/`目录。这个方法大部分是借鉴了 Wix 的 Tal Kol 做的[本指南](https://hackernoon.com/redux-step-by-step-a-simple-and-robust-workflow-for-real-life-apps-1fdf7df46092)，稍加调整。

```
.
|-- components
|   |-- Footer.tsx
|   `-- Header.tsx
|-- containers
|   `-- LayoutContainer.tsx
|-- pages
|   |-- index.tsx
|   |-- matches.tsx
|   `-- heroes.tsx
|-- store
|   |-- heroes
|   |   |-- actions.ts
|   |   |-- reducer.ts
|   |   `-- types.ts
|   |-- layout
|   |   |-- actions.ts
|   |   |-- reducer.ts
|   |   `-- types.ts
|   `-- index.ts
|-- ...
|-- index.tsx
`-- types.d.ts 
```

Enter fullscreen mode Exit fullscreen mode

### 按上下文对门店进行分组

作为上述指南的扩展，状态树应该由上下文构成**。** 

```
.
`- store
    |-- heroes // Handles application states inside the `/heroes` page.
    |   |-- actions.ts
    |   |-- reducer.ts
    |   `-- types.ts
    ├── layout // Handles global layout settings, e.g. theme, small/large text.
    |   |-- actions.ts
    |   |-- reducer.ts
    |   `-- types.ts
    `-- index.ts 
```

Enter fullscreen mode Exit fullscreen mode

### 结合减速器内部`store/index.ts`

在`store/`目录的根目录下包含一个`index.ts`文件。我们将使用它来声明顶级应用程序状态对象类型，并导出我们组合的 reducers。

```
// ./src/store/index.ts

import { combineReducers, Dispatch, Reducer, Action, AnyAction } from 'redux'
import { connectRouter, RouterState } from 'connected-react-router'
import { LayoutState, layoutReducer } from './layout'

// The top-level state object.
//
// `connected-react-router` already injects the router state typings for us,
// so we can ignore them here.
export interface ApplicationState {
  layout: LayoutState
  router: RouterState
}

// Whenever an action is dispatched, Redux will update each top-level application state property
// using the reducer with the matching name. It's important that the names match exactly, and that
// the reducer acts on the corresponding ApplicationState property type.
export const createRootReducer = (history: History) =>
  combineReducers({
    layout: layoutReducer,
    router: connectRouter(history)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

## 店铺类型

在每个存储模块中包含一个`types.ts`文件。这是我们保存状态类型的地方，以及与这个 Redux store 模块相关的任何其他类型。

```
// ./src/store/heroes/types.ts

// Response object for GET /heroes
// https://docs.opendota.com/#tag/heroes%2Fpaths%2F~1heroes%2Fget
export interface Hero {
  id: number
  name: string
  localized_name: string
  primary_attr: string
  attack_type: string
  roles: string[]
  legs: number
}

// This type is basically shorthand for `{ [key: string]: any }`. Feel free to replace `any` with
// the expected return type of your API response.
export type ApiResponse = Record<string, any>

// Use `enum`s for better autocompletion of action type names. These will
// be compiled away leaving only the final value in your compiled code.
//
// Define however naming conventions you'd like for your action types, but
// personally, I use the `@@context/ACTION_TYPE` convention, to follow the convention
// of Redux's `@@INIT` action.
export enum HeroesActionTypes {
  FETCH_REQUEST = '@@heroes/FETCH_REQUEST',
  FETCH_SUCCESS = '@@heroes/FETCH_SUCCESS',
  FETCH_ERROR = '@@heroes/FETCH_ERROR',
  SELECTED = '@@heroes/SELECTED'
}

// Declare state types with `readonly` modifier to get compile time immutability.
// https://github.com/piotrwitek/react-redux-typescript-guide#state-with-type-level-immutability
export interface HeroesState {
  readonly loading: boolean
  readonly data: Hero[]
  readonly errors?: string
} 
```

Enter fullscreen mode Exit fullscreen mode

## 打字动作

现在我们已经搭建好了一切，是时候开始行动了！

### 用`typesafe-actions`编写类型安全动作

Piotrek Witek 创建了 [`typesafe-actions`](https://github.com/piotrwitek/typesafe-actions) 库，它提供了创建类型安全 Redux 动作的有用的帮助函数。我们将使用它来编写 Redux 操作。

```
// ./src/store/heroes/actions.ts

import { action } from 'typesafe-actions'
import { HeroesActionTypes, Hero } from './types'

// Here we use the `action` helper function provided by `typesafe-actions`.
// This library provides really useful helpers for writing Redux actions in a type-safe manner.
// For more info: https://github.com/piotrwitek/typesafe-actions
export const fetchRequest = () => action(HeroesActionTypes.FETCH_REQUEST)

// Remember, you can also pass parameters into an action creator. Make sure to
// type them properly as well.
export const fetchSuccess = (data: Hero[]) => action(HeroesActionTypes.FETCH_SUCCESS, data)
export const fetchError = (message: string) => action(HeroesActionTypes.FETCH_ERROR, message) 
```

Enter fullscreen mode Exit fullscreen mode

## 打字减速器

Redux 4 中输入 reducers 要简单得多。

```
// ./src/store/heroes/reducer.ts

import { Reducer } from 'redux'
import { HeroesState, HeroesActionTypes } from './types'

// Type-safe initialState!
const initialState: HeroesState = {
  data: [],
  errors: undefined,
  loading: false
}

// Thanks to Redux 4's much simpler typings, we can take away a lot of typings on the reducer side,
// everything will remain type-safe.
const reducer: Reducer<HeroesState> = (state = initialState, action) => {
  switch (action.type) {
    case HeroesActionTypes.FETCH_REQUEST: {
      return { ...state, loading: true }
    }
    case HeroesActionTypes.FETCH_SUCCESS: {
      return { ...state, loading: false, data: action.payload }
    }
    case HeroesActionTypes.FETCH_ERROR: {
      return { ...state, loading: false, errors: action.payload }
    }
    default: {
      return state
    }
  }
}

// Instead of using default export, we use named exports. That way we can group these exports
// inside the `index.js` folder.
export { reducer as heroesReducer } 
```

Enter fullscreen mode Exit fullscreen mode

## 与`redux-saga`异步处理动作

如果您的 action dispatcher 包含许多异步任务，那么最好包含一个库来处理 Redux 上的副作用。两个常用的库是 [redux-thunk](https://github.com/reduxjs/redux-thunk) 和 [redux-saga](https://redux-saga.js.org/) 。由于更干净的 API，我们将使用`redux-saga`，它利用了[生成器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)。

```
// ./src/store/heroes/sagas.ts

import { all, call, fork, put, takeEvery } from 'redux-saga/effects'
import { HeroesActionTypes } from './types'
import { fetchError, fetchSuccess } from './actions'
import callApi from '../../utils/callApi'

const API_ENDPOINT = process.env.REACT_APP_API_ENDPOINT || ''

// Here we use `redux-saga` to trigger actions asynchronously. `redux-saga` uses something called a
// "generator function", which you can read about here:
// https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*

function* handleFetch() {
  try {
    // To call async functions, use redux-saga's `call()`.
    const res = yield call(callApi, 'get', API_ENDPOINT, '/heroes')

    if (res.error) {
      yield put(fetchError(res.error))
    } else {
      yield put(fetchSuccess(res))
    }
  } catch (err) {
    if (err instanceof Error) {
      yield put(fetchError(err.stack!))
    } else {
      yield put(fetchError('An unknown error occured.'))
    }
  }
}

// This is our watcher function. We use `take*()` functions to watch Redux for a specific action
// type, and run our saga, for example the `handleFetch()` saga above.
function* watchFetchRequest() {
  yield takeEvery(HeroesActionTypes.FETCH_REQUEST, handleFetch)
}

// Export our root saga.
// We can also use `fork()` here to split our saga into multiple watchers.
export function* heroesSaga() {
  yield all([fork(watchFetchRequest)])
} 
```

Enter fullscreen mode Exit fullscreen mode

为了将它们包含在我们的根存储中，我们添加了一个收集所有存储传奇的`rootSaga()`生成器函数。

```
// ./src/store/index.ts

import { all, fork } from 'redux-saga/effects'

// We `fork()` these tasks so they execute in the background.
export function* rootSaga() {
  yield all([
    fork(heroesSaga),
    fork(teamsSaga),
    // `fork()` any other store sagas down here...
  ])
} 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化 Redux 存储

初始化 Redux 存储应该在一个`configureStore()`函数中完成。在这个函数中，我们引导所需的中间件(包括`redux-saga`)并将它们与我们的 reducers 结合起来。

```
// ./src/configureStore.ts

import { Store, createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'
// `react-router-redux` is deprecated, so we use `connected-react-router`.
// This provides a Redux middleware which connects to our `react-router` instance.
import { connectRouter, routerMiddleware } from 'connected-react-router'
// We'll be using Redux Devtools. We can use the `composeWithDevTools()`
// directive so we can pass our middleware along with it
import { composeWithDevTools } from 'redux-devtools-extension'
// If you use react-router, don't forget to pass in your history type.
import { History } from 'history'

// Import the state interface and our combined reducers/sagas.
import { ApplicationState, createRootReducer, rootSaga } from './store'

export default function configureStore(
  history: History,
  initialState: ApplicationState
): Store<ApplicationState> {
  // create the composing function for our middlewares
  const composeEnhancers = composeWithDevTools({})
  // create the redux-saga middleware
  const sagaMiddleware = createSagaMiddleware()

  // We'll create our store with the combined reducers/sagas, and the initial Redux state that
  // we'll be passing from our entry point.
  const store = createStore(
    createRootReducer(history),
    initialState,
    composeEnhancers(applyMiddleware(routerMiddleware(history), sagaMiddleware))
  )

  // Don't forget to run the root saga, and return the store object.
  sagaMiddleware.run(rootSaga)
  return store
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 与 React 连接

现在让我们用 React 把一切都联系起来。

### 容器组件

**更新:**`react-redux`的最新版本打破了我在这篇文章的上一个版本中提到的“儿童道具容器”的模式。我建议现在不要使用这种模式，但是如果你仍然想使用它，这里有一个升级的方法，使用全新的`useDispatch`和`useSelector`钩子:

```
// ./src/containers/LayoutContainer

import * as React from 'react'
import { useDispatch, useSelector } from 'react-redux'

import { ApplicationState } from '../store'
import { ThemeColors } from '../store/layout'
import * as layoutActions from '../store/layout/actions'

// Redux-specific props.
interface LayoutContainerProps {
  theme: ThemeColors
  setTheme: (theme: ThemeColors) => void
}

// Wrapper props for render/children callback.
interface LayoutContainerRenderProps {
  render?: (props: LayoutContainerProps) => React.ReactElement
  children?: (props: LayoutContainerProps) => React.ReactElement
}

// ...

const LayoutContainer: React.FC<LayoutContainerRenderProps> = ({ render, children }) => {
  // We can use Hooks to call in our selector/dispatch functions.
  const { theme } = useSelector((state: ApplicationState) => state.layout)
  const dispatch = useDispatch()

  // Create the `setTheme` handler. We use the `dispatch` we got from `useDispatch()` to create said selector.
  const setTheme = (color: ThemeColors) => dispatch(layoutActions.setTheme(color))

  // Create a render/children props wrapper with the above variables set as a callback.
  if (render) {
    return render({ theme, setTheme })
  }

  if (children) {
    return children({ theme, setTheme })
  }

  return null
}

export default LayoutContainer 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以从任何组件使用 Redux 存储链接！

```
// ./src/components/layouts/Header.tsx

import * as React from 'react'
import LayoutContainer from '../../containers/LayoutContainer'

const Header: React.SFC = ({ children }) => (
  <LayoutContainer>
    {({ theme, setTheme }) => (
      <React.Fragment>
        <CurrentTheme>Current theme: {theme}</CurrentTheme>
        <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>Switch theme</button>
      </React.Fragment>
    )}
  </LayoutContainer>
)

export default Header 
```

Enter fullscreen mode Exit fullscreen mode

### 页面组件

当连接纯 React 组件时，在页面级别连接它们是一个好主意。提醒一下，当将状态/动作分派器映射到组件时，我们需要将我们映射到组件属性类型的商店的状态/动作分派器属性类型结合起来。

```
// Separate state props + dispatch props to their own interfaces.
interface PropsFromState {
  loading: boolean
  data: Hero[]
  errors: string
}

// We can use `typeof` here to map our dispatch types to the props, like so.
interface PropsFromDispatch {
  fetchRequest: typeof heroesActions.fetchRequest
}

// Combine both state + dispatch props - as well as any props we want to pass - in a union type.
type AllProps = PropsFromState & PropsFromDispatch & ConnectedReduxProps

const API_ENDPOINT = process.env.REACT_APP_API_ENDPOINT || ''

class HeroesIndexPage extends React.Component<AllProps> {
  public componentDidMount() {
    this.props.fetchRequest()
  }

  public render() {
    const { loading } = this.props

    return (
      <Page>
        <Container>
          <TableWrapper>
            {loading && <LoadingOverlay />}
            {this.renderData()}
          </TableWrapper>
        </Container>
      </Page>
    )
  }

  private renderData() {
    const { loading, data } = this.props

    return (
      <HeroesTable columns={['Name', 'Legs']} widths={['auto', '120px']}>
        {loading &&
          data.length === 0 && (
            <HeroLoading>
              <td colSpan={2}>Loading...</td>
            </HeroLoading>
          )}
        {data.map(hero => (
          <tr key={hero.id}>
            <HeroDetail>
              <HeroIcon src={API_ENDPOINT + hero.icon} alt={hero.name} />
              <HeroName>
                <Link to={`/heroes/${hero.name}`}>{hero.localized_name}</Link>
              </HeroName>
            </HeroDetail>
            <td>{hero.legs}</td>
          </tr>
        ))}
      </HeroesTable>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### [的](#using-raw-reactredux-endraw-s-raw-connect-endraw-)利用`react-redux`的`connect()`

`react-redux` `connect()`函数将我们的 React 组件连接到 redux 存储。

```
// ./src/pages/heroes.tsx

import { Dispatch } from 'redux'
import { connect } from 'react-redux'
import { ApplicationState } from '../store'

// ...

// It's usually good practice to only include one context at a time in a connected component.
// Although if necessary, you can always include multiple contexts. Just make sure to
// separate them from each other to prevent prop conflicts.
const mapStateToProps = ({ heroes }: ApplicationState) => ({
  loading: heroes.loading,
  errors: heroes.errors,
  data: heroes.data
})

// mapDispatchToProps is especially useful for constraining our actions to the connected component.
// You can access these via `this.props`.
const mapDispatchToProps = (dispatch: Dispatch) => ({
  fetchRequest: () => dispatch(heroesActions.fetchRequest())
})

// Now let's connect our component!
// With redux v4's improved typings, we can finally omit generics here.
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(HeroesPage) 
```

Enter fullscreen mode Exit fullscreen mode

## 附加辅助类型

我们还可以为连接的 Redux 组件添加一个助手类型。

```
// Additional props for connected React components. This prop is passed by default with `connect()`
export interface ConnectedReduxProps<A extends Action = AnyAction> {
  // Correct types for the `dispatch` prop passed by `react-redux`.
  // Additional type information is given through generics.
  dispatch: Dispatch<A>
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在在任何 Redux-connected 组件上，我们可以用上面的接口
扩展它的 props 接口

```
// Extend the interface (for example).
interface ComponentProps extends ConnectedReduxStore {}

class Component extends React.Component<ComponentProps> {
  public componentDidMount() {
    // We can use the extended interface above as follows.
    this.props.dispatch(layoutActions.fetchRequest())
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 样本代码

希望这个指南对你有用！基于您的反馈，我也在 GitHub 上发布了一个遵循上述指南的示例项目。点击[这里](https://github.com/resir014/react-redux-typescript-example)去那里。

## 参考文献

要了解关于 React、Redux 和 TypeScript 的更多信息，以下指南是很好的读物:

*   [React +打字稿 cheatsheet](https://github.com/sw-yx/react-typescript-cheatsheet) 作者 [@sw-yx](https://github.com/sw-yx)
*   [由](https://github.com/piotrwitek/react-redux-typescript-guide) [@piotrwitek](https://github.com/piotrwitek) 反应&在打字稿中的还原