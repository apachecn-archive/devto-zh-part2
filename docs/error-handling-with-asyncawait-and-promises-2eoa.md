# 用 async/await 和 promises 处理错误

> 原文：<https://dev.to/vcarl/error-handling-with-asyncawait-and-promises-2eoa>

(照片由亨特·牛顿在 Unsplash 上拍摄)

我喜欢承诺。它们是异步行为的极好模型，并且`await`使得避免回调地狱变得非常容易(尽管我认为承诺本身在这方面做得很好)。一旦您可以为承诺如何工作建立一个心理模型，您就可以用几行代码构建一些非常复杂的异步流。

尽管我很喜欢在我的工具箱中有 async/await，但是在使用它的时候有几个处理错误的怪癖。很容易编写错误处理方法，使其包含比您想要的更多的错误，并制定策略来抵消 async/await 带来的一些可读性优势。

对于 async/await，在等待承诺时处理错误的一种常见方法是用 try/catch 块包装它。这导致了一个相对简单的失败案例:如果您在您的`try`块中做任何其他事情，任何抛出的异常都将被捕获。

### 常规异步/等待

```
async () => {
  try {
    const data = await fetchData();
    doSomethingComplex(data);
  } catch (e) {
    // Any errors thrown by `fetchData` or `doSomethingComplex` are caught.
  }
} 
```

这是 async/await 和 JS 异常之间不幸的交互。如果 JS 有一种机制只捕捉某些异常，我们将能够更精确地描述我们想要处理的错误。当然，那样我们就要写 Java 了。

最显而易见的解决方法是将你的重物移出`try`区，但这并不令人满意。数据流变得奇怪，即使只有 1 个赋值，也不能使用`const`。

### 逻辑从`try`块中提取

```
async () => {
  let data;
  try {
    data = await fetchData();
  } catch (e) {
    // Only errors from `fetchData` are caught.
    return;
  }
  doSomethingComplex(data);
}; 
```

这段代码读起来并不特别愉快，而且随着您处理更多潜在的边缘情况，只会变得更加不愉快。它还需要纪律来跟上，并有很高的可能性在未来意外吞下错误。需要纪律来正确维护的代码是有问题的；超过一定的范围，人为错误就变得不可避免。

然而，等待承诺并不能让它消失。因为仍然有一个承诺，你可以像你会做的那样处理错误，而不用等待它。

### 与`.catch()`恭候

```
async () => {
  const data = await fetchData().catch(e => {
    // Only errors from `fetchData` are caught.
  });
  if (!data) return;
  doSomethingComplex(data);
}; 
```

这非常有效，因为大多数时候错误处理是相对独立的。您的成功案例仍然受益于 await，而没有错误处理强制奇怪的代码结构，但是它要求您在数据上添加一个空检查。对于更复杂的异步流，我认为这将更容易阅读和更直观地编写。空检查很容易被忘记，并且可能引入在编写复杂流时容易被忽略的错误。

因为很难在不引入错误的情况下处理错误，*我倾向于避免在浏览器中运行的任何东西上使用`async/await`。*当我不关心失败案例时，这是一个极好的便利，但编程很难，当错误被吞下时编程更难。将`await`投入广泛使用有太多的陷阱。

## 承诺呢？

当处理没有 async/await 的承诺时，错误处理的选择更简单。只有两个选择:`.catch()`，或者`.then()`的第二个参数。他们有一个主要的不同，几周前我给[做了一个演示](https://codesandbox.io/s/j45mmo2rmw)。

### 承诺与`.catch()`

```
() => {
  fetchData()
    .then(data => {
      doSomethingComplex(data);
    })
    .catch(err => {
      // Errors from `fetchData` and `doSomethingComplex` end up here.
    });
}; 
```

这与我们的第一个 try/catch 块有相同的问题——它处理错误过于热情。最终，当我在编辑`doSomethingComplex`时犯了一个错别字，我会因为看不到错误而浪费时间。相反，我更喜欢用 error 参数来表示`.then()`。

```
 fetchData()
    .then(
      data => {
        doSomethingComplex(data);
      },
      err => {
        // Only errors from `fetchData` are caught.
      }
    );
}; 
```

我很少用`.catch()`。我希望成功案例中的错误传播到我能看到的地方。否则，开发过程中的任何问题都会被吞下，增加了我在没有意识到的情况下发布 bug 的几率。

然而，我更喜欢非常精确地处理错误。我更喜欢让 bug 浮出水面，这样就可以观察和修复它们。如果你想让用户界面在遇到任何问题的时候都保持突突状态，阻止错误的传播可能是你想要的。请注意，这样做意味着只会记录严重的故障。

## 承诺的其他问题

我在承诺中遇到的一个重要“陷阱”是，承诺中的错误总是会导致拒绝。如果你正在开发某种外部数据的抽象，这可能是一个问题。如果您假设您的 promise rejection 处理程序只需要处理网络错误，那么您最终会引入错误。非网络异常不会出现在您的错误跟踪工具中，或者在出现时会丢失重要的上下文。

```
const fetchData = () =>
  requestData().then(({ data }) =>
    // What if `removeUnusedFields` throws?
    // It could reference a field on `undefined`, for example.
    data.map(removeUnusedFields)
  );

//
fetchData().then(handleSuccess, err => {
  // This code path is called!
}); 
```

这就是承诺的行为方式，但在发展过程中，它咬了我几次。没有简单的修复方法，所以这只是开发过程中需要记住的一个案例。它不太可能在生产中自然发生，但是在您编辑代码时，它会耗费您的时间。

当你写代码的时候，总会有一些未知的东西，所以假设你的错误处理最终会和一些它没有被设计来处理的东西一起运行是安全的。不精确的错误处理在生产率和发布的错误数量方面有很大的成本。我最近在编辑一系列复杂的异步任务时遇到了一个例子，这些任务使用了带有 try/catch 的 await。它抛出了 try 中的最后一个函数调用，执行成功和失败代码路径。我花了一段时间才注意到这种行为，花了更长时间才明白为什么会发生这种情况。

总的来说，承诺有很多方式会让你在处理错误时处于不利地位。理解错误如何传播将有助于您编写更好地容忍错误的代码。在正确处理错误和避免[过度防御的代码](https://medium.com/@cvitullo/overly-defensive-programming-e7a1b3d234c2)之间，这是一条微妙的线，但从长远来看，这将带来回报。

展望未来，有人提议添加[模式匹配](https://github.com/tc39/proposal-pattern-matching)(在撰写本文时是[阶段 1](https://tc39.github.io/process-document/) ，这将为精确处理错误提供一个强大的工具。考虑到在 JS 生态系统的不同部分中使用的描述错误的不同方式，模式匹配看起来是描述它们的一种极好的方式。

关于承诺的更多阅读，我向[推荐诺兰·劳森的这篇文章](https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html)，它是对这篇文章早期草稿的回复。有趣的是，他建议避免在`.then()`中处理错误，偏向于`.catch()`，阅读不同的视角也很好。它更多地谈到了如何共同做出承诺，这是我根本没有提到的。

* * *

感谢阅读！我在 Twitter 上的名字是 [@cvitullo](https://twitter.com/cvitullo) (但在其他大多数地方我都是 vcarl)。我主持了 React 开发者聊天室[React flux](http://join.reactiflux.com/)和 Node 聊天室 [Nodeiflux](https://discordapp.com/invite/vUsrbjd) 。JS 开发者。如果您有任何问题或建议，请联系我们！