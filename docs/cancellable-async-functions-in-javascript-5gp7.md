# JavaScript 中可取消的异步函数

> 原文：<https://dev.to/chromiumdev/cancellable-async-functions-in-javascript-5gp7>

<small>(这篇文章解释了如何使用生成器来避免对`async`函数的重复调用。[查看最终进场的要点](https://gist.github.com/samthor/8f72127e3cf44bca1fc6527ce7e47023)或继续阅读了解更多！🎓)</small>

JavaScript 是一个可怕的异步调用的迷宫，所有的调用都一样。我们都写过这样的代码——但是在这篇文章中，我将讨论`async`和`await`。这些是[广泛支持的](https://medium.com/dev-channel/es6-modules-in-chrome-canary-m60-ba588dfb8ab7)关键词，帮助你将代码移植到可读性更强的地方。📖👀

最重要的是，我将覆盖一个关键的陷阱:如何处理一个被多次运行的异步方法，以便它不会影响其他工作。🏑💥

先说例子。该函数将获取一些内容，将其显示在屏幕上，并等待几秒钟，然后引起用户的注意:

```
function fetchAndFlash(page) {
  const jsonPromise = fetch('/api/info?p=' + page)
      .then((response) => response.json());
  jsonPromise.then((json) => {
    infoNode.innerHTML = json.html;

    setTimeout(() => {
      flashForAttention(infoNode);
    }, 5000);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像这样用`async`和`await`重写这个，没有回调:

```
async function fetchAndFlash(page) {
  const response = await fetch('/api/info?p=' + page);
  const json = await response.json();
  infoNode.innerHTML = json.html;

  // a bit awkward, but you can make this a helper method
  await new Promise((resolve) => setTimeout(resolve, 5000));

  flashForAttention(infoNode);
} 
```

Enter fullscreen mode Exit fullscreen mode

那不是更好吗？它跳来跳去，很容易看到从上到下的步骤:获取资源，将其转换为 JSON，写入页面，等待五秒钟，然后调用另一个方法。🔜

## 这是个陷阱！

但是这里有些东西会让读者困惑。这不是一个“一次执行”的常规函数——每次我们调用`await`，我们基本上都遵从浏览器的事件循环，这样它就能继续工作。⚡🤖

换句话说:假设您正在阅读使用`fetchAndFlash()`的代码。如果您没有阅读这篇文章的标题，那么运行这段代码会发生什么呢？

```
fetchAndFlash('page1');
fetchAndFlash('page2'); 
```

Enter fullscreen mode Exit fullscreen mode

你可能期望一个接一个发生，或者一个会抵消另一个。事实并非如此——两者或多或少会并行运行(因为 JavaScript 不能在我们等待时阻塞),以*或*的顺序结束，并且不清楚什么 HTML 会出现在您的页面上。⚠️

[![How two tasks can run in parallel and overwrite one another](../Images/d5418988d4f145dfb47ea387a8694af6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LGn-CUAv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yqpfeca7xv1d4podbyfj.png)

明确地说，这种方法的基于回调的版本有完全相同的问题，但是它更明显——以一种非常令人厌恶的方式。在使我们的代码现代化以使用`async`和`await`的过程中，我们使它变得更加模糊。😕

* * *

让我们来看看解决这个问题的几种不同的方法。系好安全带。🎢

## 方法 1:连锁

根据你调用一个`async`方法的方式和原因，它可能能够一个接一个地“链接”它们。假设您正在处理一个点击事件:

```
let p = Promise.resolve(true);
loadButton.onclick = () => {
  const pageToLoad = pageToLoadInput.value;
  // wait for previous task to finish before doing more work
  p = p.then(() => fetchAndFlash(pageToLoad));
}; 
```

Enter fullscreen mode Exit fullscreen mode

每单击一次，就向链中添加了另一个任务。我们也可以用一个辅助函数来概括这一点:

```
// makes any function a chainable function
function makeChainable(fn) {
  let p = Promise.resolve(true);
  return (...args) => {
    p = p.then(() => fn(...args));
    return p;
  };
}
const fetchAndFlashChain = makeChainable(fetchAndFlash); 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可以只调用`fetchAndFlashChain()`，它会在任何*和其他*调用`fetchAndFlashChain()`之后按顺序发生。🔗

但这并不是这篇博文中的建议——如果我们想取消之前的操作呢？你的用户刚刚点击了一个*不同的*加载按钮，所以他们可能不关心之前的事情。🙅

## 方法 2:障碍检查

在我们的现代化的`fetchAndFlash()`中，我们使用了`await`关键字三次，仅仅是因为两个不同的原因:

1.  进行网络获取
2.  等待 5 秒后闪烁

在这两点之后，我们可以停下来问——“嘿，我们仍然是最活跃的任务吗？用户*最近*想做的事情？”🤔💭

我们可以通过用随机数标记每个不同的操作来做到这一点。这意味着创建一个唯一的对象，在本地和全局存储它，并查看全局版本是否偏离本地版本——因为*另一个*操作已经开始。

下面是我们更新的`fetchAndFlash()`方法:

```
let globalFetchAndFlashNonce;
async function fetchAndFlash(page) {
  const localNonce = globalFetchAndFlashNonce = new Object();

  const response = await fetch('/api/info?p=' + page);
  const json = await response.json();
  // IMMEDIATELY check
  if (localNonce !== globalFetchAndFlashNonce) { return; }

  infoNode.innerHTML = json.html;

  await new Promise((resolve) => setTimeout(resolve, 5000));
  // IMMEDIATELY check
  if (localNonce !== globalFetchAndFlashNonce) { return; }

  flashForAttention(infoNode);
} 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但有点拗口。概括起来也不容易，你必须*记住*在所有重要的地方添加检查！

不过，有一种方法——使用[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators)为我们进行归纳。

## 背景:发电机

当`await`推迟执行直到它等待的事情*完成*时——在我们的例子中，要么是网络请求，要么只是等待超时——生成器函数基本上做相反的事情，将执行移回到它被*调用*的地方。

迷茫？这值得一提

```
function* myGenerator() {
  const finalOut = 300;
  yield 1;
  yield 20;
  yield finalOut;
}
for (const x of myGenerator()) {
  console.info(x);
}
// or, slightly longer (but exactly the same output)
const iterator = myGenerator();
for (;;) {
  const next = iterator.next();
  if (next.done) {
    break;
  }
  console.info(next.value);
} 
```

Enter fullscreen mode Exit fullscreen mode

这个程序，两个版本，将打印 1，20 和 300。有趣的是，我可以在任何一个`for`循环中做任何我喜欢的事情，包括早期的`break`，而`myGenerator`中的所有状态都保持不变——我声明的任何变量，以及我在哪里。

这里看不到，但是调用生成器的代码*(特别是它返回的迭代器的`.next()`函数)也可以用一个变量恢复它。我们会看到多快。*

如果我们决定停止，我们可以一起使用这些部分，只是*而不是*继续执行某个任务，并且还可以通过一些输出来恢复执行。嗯——听起来很适合我们的问题！✅

## 解🎉

让我们最后一次重写`fetchAndFlash()`。我们实际上只是改变函数类型本身，并将`await`与`yield`交换:调用者可以等待我们——我们将看到下一步如何:

```
function* fetchAndFlash(page) {
  const response = yield fetch('/api/info?p=' + page);
  const json = yield response.json();

  infoNode.innerHTML = json.html;

  yield new Promise((resolve) => setTimeout(resolve, 5000));

  flashForAttention(infoNode);
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码现在真的没有意义，如果我们试图使用它，它会崩溃。产生每个`Promise`的要点是，现在，一些调用这个生成器的函数可以为我们做`await` *，包括检查一个随机数。你现在不必在意在等待的时候插入这些行——你只需要使用`yield`。*

最重要的是，因为这个方法现在是一个*生成器*，而不是一个`async`函数，所以`await`关键字实际上是一个错误。这绝对是确保您编写正确代码的最佳方式！🚨

我们需要的功能是什么？好了，这就是这篇文章的真正魔力:

```
function makeSingle(generator) {
  let globalNonce;
  return async function(...args) {
    const localNonce = globalNonce = new Object();

    const iter = generator(...args);
    let resumeValue;
    for (;;) {
      const n = iter.next(resumeValue);
      if (n.done) {
        return n.value;  // final return value of passed generator
      }

      // whatever the generator yielded, _now_ run await on it
      resumeValue = await n.value;
      if (localNonce !== globalNonce) {
        return;  // a new call was made
      }
      // next loop, we give resumeValue back to the generator
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这很神奇，但希望也有意义。我们调用传递的生成器，得到一个迭代器。然后，我们对它产生的每一个值进行`await`,像网络响应一样恢复结果值——直到生成器完成。**重要的是**，这让我们能够在每次异步操作后检查全局和局部随机数。

<small>一个扩展:如果有新的呼叫，返回一个特殊的值，因为知道单独的呼叫是否被取消很有用。在[示例要点](https://gist.github.com/samthor/8f72127e3cf44bca1fc6527ce7e47023)中，我返回了一个`Symbol`，一个你可以比较的独特对象。</small>

最后，我们实际上使用了`makeSingle`并包装了我们的生成器供其他人使用，所以现在它的工作就像一个常规的异步方法:

```
// replaces fetchAndFlash so all callers use it as an async method
fetchAndFlash = makeSingle(fetchAndFlash);

// ... later, call it
loadButton.onclick = () => {
  const pageToLoad = pageToLoadInput.value;
  fetchAndFlash(pageToLoad);  // will cancel previous work
}; 
```

Enter fullscreen mode Exit fullscreen mode

万岁！现在，你可以在任何你喜欢的地方打电话给`fetchAndFlash()`，并且知道之前的任何电话都会尽快取消。

* * *

## 靠边:靠边取

热心的人可能会注意到，我上面提到的只是*取消了*一个方法，但没有中止任何正在进行的工作。我说的是`fetch`，它有某种支持的方式[来中止网络请求](https://developers.google.com/web/updates/2017/09/abortable-fetch)。如果异步功能是下载一个非常大的文件，这可能会节省用户的带宽，这不会被我们所做的事情所阻止——一旦文件已经用完了宝贵的字节，我们就会取消。

## 搞定

如果你读到这里，希望你对 JavaScript 的工作方式有了更多的思考。

当你需要做异步工作时，JS 不能阻塞，对你的方法的多次调用可能发生，你可以有策略来处理这种情况——要么是链接，要么就像这篇文章的主题一样，取消以前的调用。

感谢阅读！👋