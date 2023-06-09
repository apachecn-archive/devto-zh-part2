# 如何测试异步冗余 Thunks

> 原文：<https://dev.to/gsto/how-to-test-async-redux-thunks-h9c>

在之前的文章中，您学习了如何在 redux 应用程序中[发出 HTTP 请求。我们使用](https://glennstovall.com/redux-http-async/) [redux-thunk](https://github.com/reduxjs/redux-thunk) ，一个除了 [flux 风格](https://github.com/redux-utilities/flux-standard-action)动作之外还能让你调度功能的库。有了它，您可以分派函数来处理更复杂的情况，例如异步操作。但是你如何测试它们呢？测试可能比编写正常运行的代码本身更费事。处理服务器响应和计时已经够难的了。幸运的是，您可以将一些工具和模式应用到工作中，轻松地使您的代码库更加可靠。

首先，我们将看一下我们将用于测试的工具。然后，如何将它们应用到我们的操作中。

## 测试贸易的工具

*   Jest–[Jest](https://jestjs.io/)是来自脸书开发生态系统的 JavaScript 测试库，就像 React 一样。它的设计不需要任何配置，不妨碍您更容易、更快地编写测试。
*   **redux-mock-store**–由于该操作的主要目标是更新 Redux 商店，因此您需要一种模拟 Redux 商店的方法。 [redux-mock-store](https://github.com/dmitry-zaets/redux-mock-store) 就是这么做的。
*   Moxios–[Moxios](https://github.com/axios/moxios)是一个 Javascript 库，负责处理 Axios 请求。我们将利用这一点将我们的代码从服务器中分离出来，这样我们就可以隔离我们的逻辑并只测试它。

## 快速回顾:我们的 HTTP 行动

这是上一个教程中的 thunk】

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

现在，让我们开始向代码中添加一些测试，这样我们就可以对代码库进行修改，而不用担心会导致回归。

## 1。创建一个模拟商店

首先，我们将建立我们的商店。因为我们的 redux 应用程序使用 thunk 中间件，所以我们在测试时也需要应用该中间件。完成后，我们将创建一个函数来帮助我们设置测试状态。许多应用程序都有某种初始状态。我们将创建一个助手函数，它接受一个已配置的存储，并将初始状态与您作为参数传递的状态相结合，而不是为每个测试都创建一个这样的函数。

```
import thunk from 'redux-thunk'
import configureMockStore from 'redux-mock-store'
export const startState = {} //put initial state here
export const mockStore = configureMockStore([thunk])
export const makeMockStore = (state = {}) => { 
  return mockStore({
    ...startState,
    ...state,
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

## 2。创建一些 Moxios 助手

接下来，让我们为 moxios 创建几个助手函数。Axios 和 Moxios 查看状态代码来确定是解决还是拒绝承诺。当我们编写多个 API 测试时，这两个函数将节省我们一点时间。如果您的应用程序有许多不同的基于 HTTP 的 thunks，这些助手函数将为您节省相当多的击键次数。

```
const mockSuccess = data => ({ status: 200, response: { data } })
const mockError = error => ({ status: 500, response: error }) 
```

Enter fullscreen mode Exit fullscreen mode

## 3。为您的测试配置安装和拆卸

对于我们的测试，我们需要设置并拆除 Moxios 适配器。它拦截传出的 HTTP 请求，允许您控制正在测试的函数返回的响应。看起来是这样的:

```
describe('fetchPosts', () => {
  beforeEach(() => moxios.install())
  afterEach(() => moxios.uninstall())
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 4。写下你的成功测试

我们在这里想要断言什么？

您没有测试任何服务器端逻辑。你没有测试状态的改变，因为那是缩减器的工作。您应该为此编写单独的 reducer 测试。thunk 只负责决定分派哪些动作，所以这才是重点。

因此，我们的测试需要完成的工作是:

1.  创建商店的模拟实例。您将向它分派操作。
2.  创建一个模拟服务器响应。
3.  调用您的 thunk 并断言它向您的模拟存储分派了正确的操作。

总的来说，是这样的。

```
it('dispatches loadPostsSuccess with server data on success', () => {
    const response = ['some', 'posts']
    const store = makeMockStore()
    moxios.wait(() => {
      const request = moxios.requests.mostRecent()
      request.respondWith(mockSuccess(response))
    })
    const expected = [
      actions.loadPostsRequest(),
      actions.loadPostsSuccess(response),
    ]
    store.dispatch(fetchPosts()).then(() => {
      const actual = store.getActions()
      expect(actual).toEqual(expected)
    })
  }) 
```

Enter fullscreen mode Exit fullscreen mode

## 5。现在对错误响应做同样的事情

不要只考快乐之路。当编写测试时，谨慎的做法是问自己，“什么会出错？”我们的服务器可能会抛出一个错误响应，所以我们也想测试这个用例。在我们的例子中，错误测试用例看起来与我们的成功测试用例几乎相同。

```
it('dispatches loadPostsError with server data on success', () => {
    const response = 'error message'
    const store = makeMockStore()
    moxios.wait(() => {
      const request = moxios.requests.mostRecent()
      request.respondWith(mockError(response))
    })
    const expected = [
      actions.loadPostsRequest(),
      actions.loadPostsError(response),
    ]
    store.dispatch(fetchPosts()).then(() => {
      const actual = store.getActions()
      expect(actual).toEqual(expected)
    })
  }) 
```

Enter fullscreen mode Exit fullscreen mode

## 将此应用到您的应用程序中

这是如何在应用程序中测试异步操作的最纯粹的例子。当然，在现实世界中，事情从来没有这么简单。如果您能想到其他的用例，一定要为它们编写测试。例如，您期望从服务器得到不同种类的成功或错误响应吗？你需要额外的逻辑来处理它们吗？如果是这样，这可能是创建额外测试的用例。

仔细考虑您的不同用例，并决定最佳方法。

帖子[如何测试 Async Redux Thunks](https://glennstovall.com/how-to-test-async-redux-thunks/) 最早出现在[的格伦·斯托瓦尔——成长工程师](https://glennstovall.com)上。