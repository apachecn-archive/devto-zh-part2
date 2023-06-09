# 如何用 React & Redux 执行 HTTP 请求

> 原文：<https://dev.to/gsto/how-to-perform-http-requests-with-react--redux-3673>

异步是前端开发中最棘手的问题之一。这也是 Redux 和 React 被创建的原因之一。React all 始于脸书遇到一个看似微不足道的问题:有时，页面页眉和页脚中的“未读邮件”数量会有所不同。这个斯金纳盒子对脸书的商业模式非常重要，他们决定围绕它建立一个完整的框架。通过管理全局状态中的所有更改，并专门根据该状态呈现组件，可以消除这类问题。

大多数异步请求来自与服务器的交互。让我们看看如何用 HTTP 请求更新 Redux 状态。

# 首先，安装中间件。

Redux 没有提供开箱即用的处理方法。Redux 中的典型模型是调用 dispatch 函数，将一个动作作为参数传入。dispatch 函数将该操作交给 reducer，后者将更新状态。所有这些都是同步动作。如果我们想要调度异步动作呢？为此，我们将使用名为“ [redux-thunk](https://github.com/reduxjs/redux-thunk) 的中间件 redux-thunk 为您提供了分派函数或动作的能力。然后，这些函数可以自己分派动作，但稍后会详细介绍。首先，安装中间件:

```
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'

const initial state = {
  posts_loading: false,
  posts: [],
  posts_error: null,
}

const configureStore = () => createStore(
  reducer,
  initialState,
  applyMiddleware(thunk)),
)

export default configureStore 
```

Enter fullscreen mode Exit fullscreen mode

## 几个快捷的 Thunk 例子

thunk 是一种特殊的返回函数的动作创建器。该函数接收两个参数:dispatch 和 getState，不出所料，这是一个返回状态的函数。这是使用 thunk 的另一个好处:它允许我们基于当前状态触发调度，而不传递任何参数。下面是一些例子:

```
const slowDispatch = () => {
  return (dispatch, getState) => {
     return setTimeout(() =>
       dispatch(increment()), 1000)
     )
  )
}

const maybeDispatch = () => {
  return (dispatch, getState) => {
    if (getState().allowIncrement) dispatch(increment())
  }
}

// If you want to be terser, you can write these as one-liners
const slowDispatch = () => (dispatch) => setTimeout(() => dispatch(increment()),1000)

const maybeDispatch = () => (dispatch, getState) => getState().allowIncrement ? dispatch(increment()) : null 
```

Enter fullscreen mode Exit fullscreen mode

## 创建动作&动作创建者。

又名良好的旧 Redux 样板。在这个练习中，我们将遵循[鸭子模式](https://medium.freecodecamp.org/scaling-your-redux-app-with-ducks-6115955638be)。

首先，有三个动作，一个告诉我们请求何时开始，一个处理成功，一个处理错误。我们将为这些创建类型。您可以只使用字符串而不是声明常量，但是这种策略可以帮助您在调试时更快地识别拼写错误。一个输入错误的操作将抛出一个错误，而不是无声地失败。

```
// types.js
const LOAD_POSTS_REQUEST = 'tasks/history/request'
const LOAD_POSTS_SUCCESS = 'tasks/history/success'
const LOAD_POSTS_FAILURE = 'tasks/history/failure'

export default { 
  LOAD_POSTS_REQUEST,
  LOAD_POSTS_SUCCESS,
  LOAD_POSTS_FAILURE,
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将创建我们的*动作创建者*。同样，我们可以在 thunk 内部这样做，但这有助于保持代码的整洁。您也可以在编写测试时使用这些，它们将帮助您更高效地编写测试。我们将创建三个:一个在加载时更新状态，一个在得到成功响应时更新，一个在得到错误时更新。

```
import types from './types'

const loadPostsRequest = () => ({ type: types.LOAD_POSTS_REQUEST })

const loadPostsSuccess = posts => ({
  type: types.LOAD_POSTS_SUCCESS,
  payload: posts,
})

const loadPostsFailure = error => ({
  type: types.LOAD_POSTS_FAILURE,
  payload: error,
  error: true,
})

export default {
  loadPostsRequest,
  loadPostsSuccess,
  loadPostsFailure,
} 
```

Enter fullscreen mode Exit fullscreen mode

## 更新我们的减速器

现在我们有了动作，我们需要在我们的 reducer 中处理它们。我们将存储三个变量。首先，一个布尔值来跟踪加载状态。我们可以用它来切换界面中的加载指示器。我们还将结果存储在一个数组中，如果有问题，存储我们得到的错误响应。

```
import types from './types'

const postReducer = (state = {}, action) => {
  switch (action.type) {
    case types.LOAD_POST_REQUEST:
      return {
        ...state,
        posts_loading: true,
      }
      case types.LOAD_POST_SUCCESS:
        return {
          ...state,
          posts_loading: false,
          posts: action.payload,
        }
        case types.LOAD_POST_FAILURE:
        return {
          ...state,
          posts_loading: false,
          posts_error: action.payload,
        }
        //...other actions
        default:
           return state 
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 谁来调度急件？创造我们的思维

在鸭子模式中，操作是更高阶的动作创造者。思维是一种操作。另一种思考操作的方式是 action creator+。它将调度操作，有时不止一个，有时还负责其他业务逻辑。同样，使 thunks 特别的是返回一个函数而不是一个动作。

在我们的操作中，我们将部署前面定义的三种操作的某种组合。我们将使用 [Axios](https://www.npmjs.com/package/axios) 来发出 HTTP 请求。

```
const fetchPosts = () => {
  const url = '/our-app/posts.json'
  return (dispatch) => {
    dispatch(actions.loadPostsRequest())
    axios.get(url)
      .then((response) => {
        dispatch(actions.loadPostsSuccess(response.data))
      })
      .catch((error) => {
        dispatch(actions.loadTaskHistoryFailure(error))
      })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了！现在你已经写好了你的 thunk，你需要确保它经过了很好的测试并且能够适应变化。在下一篇文章中，您将确切地了解如何测试 redux thunks 。

帖子[如何用 React & Redux](https://glennstovall.com/redux-http-async/) 执行 HTTP 请求最早出现在[的格伦·斯托瓦尔——成长工程师](https://glennstovall.com)上。