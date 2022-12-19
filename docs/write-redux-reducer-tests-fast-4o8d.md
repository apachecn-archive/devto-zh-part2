# 快速编写 Redux Reducer 测试

> 原文：<https://dev.to/gsto/write-redux-reducer-tests-fast-4o8d>

Redux 可能附带了许多样板文件、模式和库，但它的核心很简单。一个当前状态和一个动作进去，新的状态出来。

代码简单并不意味着不应该测试它。如果您在应用程序中使用 Redux，这意味着您的商店是它的核心部分。出于这个原因，您应该对您的缩减器可能采取的每个动作，以及它们可能采取的每个逻辑分支进行测试。但是不要烦恼！因为 reducers 并不复杂，所以编写测试也不复杂。通过一点设置，您可以遵循这个模式，像专家一样完成 reducer 测试。

*(先快速说明一下！帖子[写 Redux 减速器测试快](https://glennstovall.com/write-redux-reducer-tests-fast/)最早出现在我的简讯上，[周二脉动](https://glennstovall.com/newsletter)。)*

## 设置减速器测试

如果我需要每个测试都有一个缩写状态，我使用的唯一设置是使用 startState 对象。不像[测试 thunks](https://glennstovall.com/how-to-test-async-redux-thunks/) ，不需要模拟存储。我们的初始状态(以及我们在测试中使用的状态)将是简单的对象。这些测试尝起来应该像[香草](https://glennstovall.com/which-version-of-javascript-is-vanilla-javascript/)。

```
const startState = {} // initial state shape if needed 
```

Enter fullscreen mode Exit fullscreen mode

## 减速器

我将从之前关于[创建异步动作](https://glennstovall.com/redux-http-async/)的教程中提取 reducer 示例。代码重用，哇哦！🎉

```
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

作为快速参考，这里有一些动作创建器，您可以使用它们来处理这个 reducer。我们很快就会需要它们

```
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

## 减速器测试模式

我为一个缩减器编写的每个测试都遵循这个模式:

1.  我宣布一个初始状态
2.  我声明一个预期的结果状态
3.  我创建了一个动作
4.  我用动作和初始状态调用 reducer
5.  我比较实际状态和预期状态，我预期会发生变化。

下面是代码形式的模板:

```
it('should apply the updates as expected', () => {
  const start = { ...startState } // add or change fields as needed.
  const expected = {} // expected result state
  const action = actions.myActionCreator() //include arguments as needed
  const actual = reducer(start, action) 
  expect(actual).toEqual(expected)
}) 
```

Enter fullscreen mode Exit fullscreen mode

嘣。完成了。为了使事情更简单，如果您的应用程序没有初始状态，您可以声明从头开始。正如您将在下面看到的，您将需要为特定的情况调整公式，但是它们都将遵循这个模板。

## 例 1:加载 _ 发布 _ 请求

让我们看看它的实际效果。我们的第一个动作就是切换一个布尔值。注意，在这个例子中，我不会创建一个预期的结果状态。因为我们只对一个布尔值感兴趣，所以我们可以查看那个值，并使用 Jest 的 toBeTruthy()和 toBeFalsy()匹配器。如果你不熟悉所有的匹配器，[这里有一个快速列表](https://jestjs.io/docs/en/expect)供参考。

```
describe('LOAD_POSTS_REQUEST', () => {
  it('marks the current task as not loaded', () => {
    const start = {
        ...startState,
        posts_loading: false,
      }
    const action = actions.loadPostsRequest()
    const actual = reducer(start, action).posts_loading
    expect(actual).toBeTruthy()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 例 2:加载 _ 发布 _ 成功

这里我们将编写两个测试:一个确认我们将文章加载到状态，另一个确认我们已经标记文章不再处于加载状态。因此，我们可以将一些设置代码移到 before 函数中。

```
describe('LOAD_POSTS_SUCCESS', () => {
  let actual
  let expected
  beforeEach(() => {
    const start = {
      ...startState,
      posts: [],
      posts_loading: true
    }
    expected = ['some', 'posts']
    const action = actions.loadPostsSuccess(expected)
    actual = reducer(start, action)
  })
  it('marks posts as loaded', () => {
    expect(actual.posts_loading).toBeFalsy()
  })
  it('saves posts in state', () => {
    expect(actual.posts).toEqual(expected)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 例 3:LOAD _ post _ FAILURE

类似于我们的 thunk 示例，我们的失败用例看起来类似于我们的成功用例。尽管如此，彻底一点还是好的。没有什么比期待有用的错误信息却什么也没找到更令人沮丧的了。

```
describe('LOAD_POSTS_FAILURE', () => {
  let actual
  let expected
  beforeEach(() => {
    const start = {
      ...startState,
      posts_error: null,
      posts_loading: true
    }
    expected = 'Posts not found!'
    const action = actions.loadPostsFailure(expected)
    actual = reducer(start, action)
  })
  it('marks posts as loaded', () => {
    expect(actual.posts_loading).toBeFalsy()
  })
  it('saves posts error in state', () => {
    expect(actual.posts_error).toEqual(expected)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 将此应用到您的代码库

如果您的 reducer 更新状态的方式有错误，那么调试会很困难。虽然 Redux DevTools 有所帮助，但是如果这些 bug 从来没有出现在浏览器中不是更好吗？为了帮助防止它们逃脱，请确保您的减速器经过彻底测试。该模式可以很容易地调整到其他常见的减速器用例:

*   你的减速器中有条件逻辑吗？为每个逻辑分支编写一个测试。
*   对您的减速器进行验证？抛出有效和无效的动作，以确保它正确处理这两种情况。
*   在您的 reducer 中转换数据？调整预期的调用，以确保数据按照您想要的方式输出。

仍然有一个特别的行动让你感到困难？这可能是一个迹象，表明您有一个混乱或过于复杂的动作或状态形状，一些重构可能是有序的。

*(想要更多像这样可操作的编码和职业建议？你会在[周二脉搏](https://glennstovall.com/newsletter)找到它。)*