# 用笑话嘲弄和自然的反应

> 原文：<https://dev.to/supremebeing7/mocking-with-jest-and-react-native-3mdn>

我一直在构建一个 React 原生应用，并使用 [Jest](https://jestjs.io/) 学习测试。这是一次有趣的旅程，除了分享一些我认为有用的[文章](https://medium.com/react-native-training/learning-to-test-react-native-with-jest-part-1-f782c4e30101)的链接之外，我不打算深入介绍我对这个测试套件的很多方法。

无论如何，这篇文章的重点是如何用 Jest 来模仿，特别是我如何在我的单元测试中找出如何模仿一个叫做 [`'react-native-router-flux'`](https://github.com/aksonov/react-native-router-flux) 的流行库。

我在这篇文章中的代码样本[之后测试一个动作，我的一个动作路由到一个新场景:](https://pillow.codes/testing-in-react-native-jest-detox-d7b3b79a166a) 

```
// Actions.js
import { Actions } from 'react-native-router-flux'
// ...some codes...
Actions.replace('afterLoginScene') 
```

Enter fullscreen mode Exit fullscreen mode

我不觉得有必要在单元测试那个特定动作的时候测试那个转换，但是测试试图运行那个代码，结果很糟糕。所以我想以某种方式存根或模拟`Actions`，这样我就可以测试该动作中的其他所有内容。经过大量的反复试验和谷歌搜索，一位同事与我分享了一篇 [StackOverflow 帖子](https://stackoverflow.com/a/45007792/3477163)，里面有答案:

```
// testHelper.js
export default jest.mock(
  'react-native-router-flux', () => ({
    Actions: {
      replace: jest.fn(),
      // whatever other Actions you use in your code
    },
  })
)

// Actions.test.js
import 'testHelper.js'
import { Actions } from 'react-native-router-flux' 
```

Enter fullscreen mode Exit fullscreen mode

现在我只能测试预期的动作是否被触发:

```
 const expectedActions = [
    { type: 'LOGIN/SET_LOGIN_ERROR', message: '' },
    { type: 'LOGIN/STORE_SOMETHING', something: 'stuff' },
  ]

  store
    .dispatch(LoginActions.sendLogin(username, password))
    .then(() => {
      expect(store.getActions()).toEqual(expectedActions)
      done()
    }) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

版本:

```
"jest": "22.4.4"
"jest-expo": "27.0.1"
"react-native": "0.55.2"
"react-native-router-flux": "4.0.0-beta.32" 
```

Enter fullscreen mode Exit fullscreen mode