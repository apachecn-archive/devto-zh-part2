# 等待那个重要的电话

> 原文：<https://dev.to/ryaninvents/waiting-for-that-important-call--3meh>

有时在测试时，需要等到函数被调用。也许你正在用[节点风格的回调](http://fredkschott.com/post/2014/03/understanding-error-first-callbacks-in-node-js/)测试代码；也许你正在使用[反应渲染道具](https://reactjs.org/docs/render-props.html)。不管您是如何到达那里的，您的测试都需要暂停，直到某个函数被调用。等待承诺实现是可能的，但是如何等到一个任意的函数被调用呢？

## 问题

假设您的测试如下所示:

```
const createEmitterOfSomeSort = require('./myEmitter');

it('should do the thing', async () => {
  const emitter = createEmitterOfSomeSort();
  const callback = jest.fn();
  emitter.on('my-event', callback);

  // TODO: wait for the callback to be called before proceeding

  // Check values which will only change after the given event
  expect(emitter.color).toBe('blue');
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个测试需要等待`my-event`在颜色被设置之前被异步触发。否则，测试会提前结束。

有可能把这一切都包在一个承诺中，当你的事件被触发时，这个承诺就会解决。我已经在测试中做了很多次了；太繁琐了！重构也是一种痛苦。假设您想等待事件触发 5 次，而不是只触发一次。这需要额外的工作，增加了测试的复杂性。

## 我的未遂方案

我决定把我的解决方案写成 [`anticipated-call`](https://www.npmjs.com/package/anticipated-call) 包来发表。该实用程序能够包装任何函数，并为您提供了一种简单的方法来获得一个承诺，该承诺会在函数被调用后解析。

这里有一个在测试中如何使用它的例子:

```
const anticipated = require('anticipated-call');
const createEmitterOfSomeSort = require('./myEmitter');

it('should do the thing', async () => {
  const emitter = createEmitterOfSomeSort();
  const callback = anticipated(jest.fn());
  emitter.on('my-event', callback);

  await callback.nextCall;

  // Check values which will only change after the given event
  expect(emitter.color).toBe('blue');
}); 
```

Enter fullscreen mode Exit fullscreen mode

`await`语句是神奇的调味汁:它将暂停测试的执行，直到回调被调用。

现在，如果您决定事件需要被触发 5 次而不是仅仅一次，那么更新您的测试就很简单:

```
 await callback.nthNextCall(5); 
```

Enter fullscreen mode Exit fullscreen mode

## 测试 React 渲染道具

在我编写渲染道具组件时，这个包对我帮助最大。假设您有一个负责获取数据的组件，其用法如下:

```
(<MyTweetFetcher
  render={({isLoading, username, tweets}) => (
    <h2>{isLoading ? 'Loading...' : username}</h2>
    <ul>
      {tweets.map((tweet) => (
        <li key={tweet.id}>{tweet.content}</li>
      )}
    </ul>
  )
/>) 
```

Enter fullscreen mode Exit fullscreen mode

这些组件通常会多次调用 render prop 来响应异步操作。这种行为给编写测试带来了一个问题:您需要确保回调收到了正确的参数，但是在组件呈现出来之前，您不能执行这种检查。`anticipated-call`前来救援:

```
const Enzyme = require('enzyme');
const anticipated = require('anticipated-call');

const MyTweetFetcher = require('./MyTweetFetcher');

it('should call the render prop with the correct arguments', async () => {
  // The render prop needs to return a valid React node, so use `null` here.
  const renderProp = anticipated(jest.fn(() => null));

  // The `nextCallDuring` method allows you to tell `anticipated-call` that
  // the function should be called as a result of running the passed callback.
  await renderProp.nextCallDuring(() => {
    Enzyme.mount(<MyTweetFetcher render={renderProp} />);
  });

  // The render prop will initially be called while data is loading.
  expect(renderProp.mock.calls[0].isLoading).toBe(true);

  // Wait for the render prop to be called again, after the data has loaded.
  await renderProp.nextCall;

  expect(renderProp.mock.calls[1].isLoading).toBe(false);
  expect(renderProp.mock.calls[1].tweets).toBeInstanceOf(Array);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 更友好的测试

这个包[挺小的](https://github.com/r24y/anticipated-call/blob/develop/src/index.js)；它做不了任何一点承诺争吵就无法完成的事情。然而，它的吸引力在于你不再*有*参与任何承诺争论。当我需要等待回调的时候，我会把`anticipated-call`扔过去，为更困难的问题保存能量。

> 查看 npm 上的 [`anticipated-call`](https://www.npmjs.com/package/anticipated-call) ，如果您有改进的想法，请在 [Github](https://github.com/r24y/anticipated-call) 上提交 PRs 或问题！