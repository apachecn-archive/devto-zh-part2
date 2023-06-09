# JavaScript 中的代数效果第 4 部分——实现代数效果和处理程序

> 原文：<https://dev.to/yelouafi/algebraic-effects-in-javascript-part-4---implementing-algebraic-effects-and-handlers-2703>

这是关于代数效果和处理程序系列的最后一部分。

*   第 1 部分:[延续和控制权转移](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-1---continuations-and-control-transfer-3g88)
*   第 2 部分:[用生成器捕获延续](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-2---capturing-continuations-with-generators-13da)
*   第 3 部分:[分隔的延续](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-3---delimited-continuations-42hj)
*   第 4 部分:实现代数效果和处理程序

所以我们来到了核心话题。事实是，我们已经在前面的部分中介绍了大部分内容。特别是在第三部分，我们看到了定界的延续。

在这一部分，我们将看到代数效应的机制与定界延拓的机制没有太大的不同。但首先，让我们从一个更熟悉的角度来探讨这个话题。我们将利用与 JavaScript 错误处理的相似性来介绍这个概念。

### 从例外到代数效应

下面是一个简单的错误处理的例子。不要太关注程序逻辑，我们感兴趣的只是调用栈的机制。

```
function main(n) {
  return handler(n);
}

function handler(n) {
  try {
    unsafeOperation(n);
  } catch (e) {
    return 0;
  }
}

function unsafeOperation(n) {
  const x = oneMoreIndirection(n);
  return x * 2;
}

function oneMoreIndirection(n) {
  if (n < 0) {
    throw "cant be under zero!";
  }
  return n + 1;
}

main(-1);
// => 0 
```

一旦我们到达`oneMoreIndirection`，调用栈看起来就像:

```
main(-1) -> handler(-1) -> unsafeOperation(-1) -> oneMoreIndirection(-1) 
```

当`oneMoreIndirection`抛出时，异常冒泡到最近的`try` / `catch`块，在本例中位于`handler`。该处理程序(`oneMoreIndirection(-1) -> unsafeOperation(-1)`)下的所有堆栈帧都将被丢弃。所以调用栈变成了这样:

```
main() -> handler() 
```

现在，让我们想象那些被丢弃的帧具体代表什么。如果我们要在`throw "can't be a zero!"`之后恢复，那么我们应该

1.  `return n + 1`来自`oneMoreIndirection`
2.  然后从`unsafeOperation`开始`return x * 2`
3.  然后返回到...嗯

之后我们应该返回哪里？它一定在`handler`里面的某个地方，但是具体在哪里呢？控制现在在`catch`中，但是可能不清楚我们的延续适合在哪里。但是请记住，例外是通过双重决策实现的

1.  控制被转移到最近的封闭处理程序
2.  从抛出函数到处理程序的堆栈帧被丢弃

那么，如果我们保持决策(1)但改变(2):堆栈帧没有被丢弃，而是具体化为一个函数(一个分隔的延续)，作为参数提供给处理程序，会发生什么呢？在一个假设的 JavaScript 中，这看起来像:

```
function handler() {
  try {
    unsafeOperation(0);
  } catch (e, /**/resume/**/) {
    // ...
    return 0;
  }
} 
```

现在，我们应该如何处理`resume`可能还不是很明显。毕竟恢复一个已经中止的功能没有太大意义。但这只是在我们认为非本地控制转移仅仅意味着发出异常信号的情况下。如果我们能以一种更普遍的方式使用它，作为一种(可能是深度嵌套的)函数和封闭处理程序之间的交互，会怎么样呢？

函数可以*抛出一个请求*，处理程序解释这个请求，然后使用提供的延续来恢复函数。与异常一样，函数不需要知道任何关于处理程序的信息，也不需要知道请求是如何完成的。这就是代数效应的核心思想。

> 我不打算讨论抛出效果和在函数中立即执行它们背后的动机。这个问题只是解释了为什么我们应该分离纯计算和不纯计算，这本身就是一个完整的主题。
> 
> 我想在这里提到的是，我看到的许多关于这个主题的讨论缺乏必要的背景。许多在 Haskell 这样的语言中有效的参数不一定能移植到 JavaScript 这样的语言中。在前一种情况下，像纯度这样的东西更容易给程序一个数学上的解释，编译器可以利用它在编译时证明程序的许多特性。JavaScript 不是这样的，它没有相同的数学形式下划线(例如，我认为没有理由禁止 JavaSript 中局部变量**的变异)。另一方面，我相信其他一些属性，如[组合性](https://en.wikipedia.org/wiki/Principle_of_compositionality)，在任何编程语言中都同样重要。**

回到我们之前的例子，这是整个例子在我们假设的 JavaScript 中的样子:

```
function main() {
  return handler();
}

function handler() {
  try {
    operation();
  } catch (e, resume) {
    return resume("Yassine");
  }
}

function operation() {
  return oneMoreIndirection();
}

function oneMoreIndirection() {
  const name = throw "Your name, please?";
  return `Hi ${name}`;
} 
```

如果你曾经使用过像`redux-saga`这样的库，这是相同的想法，但是是在 streoids 上。在这里，您可以完全控制效果(而在像`redux-saga`这样的库中，效果的解释是硬编码在库中的)。正如我们将看到的，您甚至可以控制已处理计算的返回值。

好了，已经看到了平行宇宙中的 JavaScript，让我们回到现实。虽然我们可能永远不会看到`catch`条款在某一天成为延续论点，但我们可以使用我们的老朋友，发电机，作为一个体面的安慰。

### 用生成器实现代数效果

我们将分两步走。

1.  首先，我们将实现 exception like 部分:将控制转移到最近的处理程序
2.  然后，我们将添加代码来捕获处理程序之前的分隔延续

我们将基于上一篇文章
中的这个版本来实现

```
function isGenerator(x) {
  return x != null && typeof x.next === "function";
}

function runGenerator(gen, arg) {
  const { value, done } = gen.next(arg);

  if (done) {
    const _return = gen._return;
    if (isGenerator(_return)) {
      runGenerator(_return, value);
    } else if (typeof _return === "function") {
      _return(value);
    }
  } else {
    if (isGenerator(value)) {
      value._return = gen;
      runGenerator(value, null);
    } else if (typeof value === "function") {
      value(gen);
    }
  }
}

function start(gen, onDone) {
  gen._return = onDone;
  runGenerator(gen, null);
} 
```

快速剩余，代码依赖于生成器上的一个`_return`字段，它指向父生成器。在生成器内部，我们可以调用一个子生成器(在这种情况下，我们将它的`_return`设置为当前生成器)，或者生成一个挂起的计算(这只是一个接受当前生成器的函数的别称)。

首先，让我们添加我们的`try/catch`子句的等价物。

```
function withHandler(handler, gen) {
  function* withHandlerFrame() {
    const result = yield gen;
    // eventually handles the return value
    if (handler.return != null) {
      return yield handler.return(result);
    }
    return result;
  }

  const withHandlerGen = withHandlerFrame();
  withHandlerGen._handler = handler;
  return withHandlerGen;
} 
```

*   我们需要做的第一件事是在它自己的生成器中运行`withHandler`,这样它就有了自己的堆栈框架
*   我们将提供的处理程序保存在`withHandler`自己的生成器的`_handler`字段中
*   在这个生成器中，我们运行提供的计算
*   处理程序可能最终处理计算的返回值，我们稍后会看到它是如何有用的

例如:

```
const abortHandler = {
  //optional, handles the return value
  *return(result) {
    // ...
  },
  *abort(msg) {
    console.error(msg);
    return 0;
  }
};

function* main() {
  yield withHandler(abortHandler, someFunc());
} 
```

我们将`abortHandler`设置为从`someFunc()`内部抛出的所有`abort`效果的处理程序。该函数或者它的一个子函数可以使用`perform("abort", msg)`抛出一个异常，这个异常会冒泡到处理程序。

下面是我们的第一个实现`perform`(注意我们没有捕获延续)

```
function perform(type, data) {
  return performGen => {
    // finds the closest handler for effect `type`
    let withHandlerGen = performGen;
    while (
      withHandlerGen._handler == null ||
      !withHandlerGen._handler.hasOwnProperty(type)
    ) {
      if (withHandlerGen._return == null) break;
      withHandlerGen = withHandlerGen._return;
    }

    if (
      withHandlerGen._handler == null ||
      !withHandlerGen._handler.hasOwnProperty(type)
    ) {
      throw new Error(`Unhandled Effect ${type}!`);
    }

    // found a handler, get the withHandler Generator
    const handlerFunc = withHandlerGen._handler[type];
    const handlerGen = handlerFunc(data);

    // will return to the parent of withHandler
    handlerGen._return = withHandlerGen._return;
    runGenerator(handlerGen, null);
  };
} 
```

该函数返回执行以下操作的挂起计算

1.  查找能够处理类似效果的最近的处理程序
2.  如果我们找不到合适的处理程序，我们就会抛出一个错误(这次是真的)
3.  如果找到匹配的处理程序，我们用效果数据实例化它的函数
4.  将处理程序生成器的`_return`地址设置为`withHandler`子句的父级
5.  运行处理程序的生成器

注意，最后一步意味着我们完全忽略了`performGen`，这对应于`catch`如何放弃投掷函数。

让我们看看它是如何与前面的适用于生成器的错误处理示例一起工作的

```
const abort = {
  *abort(msg) {
    console.error(msg);
    return 0;
  }
};

function* main(n) {
  return yield handler(n);
}

function* handler(n) {
  return yield withHandler(abort, unsafeOperation(n));
}

function* unsafeOperation(n) {
  const x = yield oneMoreIndirection(n);
  return x * 2;
}

function* oneMoreIndirection(n) {
  if (n < 0) {
    // throw
    yield perform("abort", "can't be under zero!");
  }
  return n + 1;
}

start(main(2), console.log);
// => 6

start(main(-1), console.log);
// => can't be under zero!
// => 0 
```

让我们仔细看看在这种情况下`perform` / `withHandler`是如何协同工作的。

由于`withHandler`没有改变调用堆栈，只是包装了给定的生成器并设置了一个特殊的`_handler`字段，当我们到达`oneMoreIndirection(-1)`时，堆栈看起来像这样:

```
main(-1) -> handler(-1) -> withHandler({abort}) -> unsafeOperation(-1) ->  oneMoreIndirection(-1) 
```

`yield perform("abort", msg)`查找最近的处理程序，该处理程序成为`withHandler`子句:
的父级的直接子级

```
main(-1) -> handler(-1) -> abort(msg) 
```

注意这和我们在上一篇文章中看到的`shift` / `reset`是多么的相似。当`shift`不使用捕获的延续时，它会有效地丢弃所有堆栈帧，直到并包括`reset`块。`shift`替换，于是，整个周围的`reset`被遮挡，成为`reset`父母的主要表情。事实上，`shift` / `reset`与`perform` / `withHanndler`有更多的相似之处，我们稍后会看到。

#### 捕捉分隔的延续

现在，我们将通过为处理程序提供一个表示先前丢弃的堆栈帧的定界延续来概括我们的异常处理。然而，这一次，我们将以不同的方式进行。在开始编写代码之前，我们将从一个使用示例开始，分析在这个示例中应该如何工作，然后展示实现。

该示例使用一个`read`效果从周围环境中获取一个值。出于我们的目的，处理程序将用一个常量值来解释效果。

```
// define the `read` handler
const constRead = {
  *read(_, resume) {
    const result = yield resume("Stranger");
    return result;
  }
};

function* main() {
  return yield withHandler(constRead, greet());
}

function* greet() {
  const name = yield withCivility();
  return `Hi, ${name}`;
}

function* withCivility() {
  // throw the `read` effect
  const name = yield perform("read");
  return `M. ${name}`;
}

start(main(), console.log);
// => Hi, M.Stranger; 
```

假设我们有一个有效的`perform`实现，让我们想象一下这个例子应该如何操作调用栈。和往常一样，在我们到达`withCivility()`
之前，什么都不会发生

```
main() -> withHandler({read}) -> greet() -> withCivility() 
```

在执行`read`效果时，我们从前面的例子中知道，处理程序将成为`main()`的直接子对象。然而，之前被丢弃的中间帧现在将成为提供给`read`处理器
的定界延续

```
main() -> read(_, <<withHandler({read}) -> greet() -> withCivility()>>) 
```

这里我们要指出一个重要的事情。捕获的延续仍然由`withHandler({read})`包装，这是很重要的，因为我们仍然想要处理来自剩余计算的进一步的`read`效果。还要注意，`read`处理程序在`withHandler({read})`范围之外运行，这一点也很重要，这个处理程序可以自己将`read`效果(或任何其他效果)转发给上游处理程序。这使得组合不同的处理程序成为可能。链中的每个处理程序可以执行一些预处理，然后将相同的(或另一个)效果委托给父处理程序。

所以，现在当`read`的处理程序恢复分隔的继续符时，堆栈变成了

```
main() -> read(_, <<>>) -> withHandler({read}) -> greet() -> withCivility() 
```

注意我们的延续只能被调用一次(一次)。这通过将第二个参数`read`设置为`<<>>`来表示。

如果`withCivility`执行第二个`read`效果，它将再次被周围的`withHandler`捕获，一个新的处理程序实例将被创建并插入堆栈。新处理程序的父代将是`withHandler({rad})`的父代，在本例中是以前的`read`处理程序。

好了，已经看到了一个关于`perform`应该如何操作调用堆栈的例子。让我们把它放到实际代码中

```
function perform(type, data) {
  return performGen => {
    // finds the closest handler for effect `type`
    let withHandlerGen = performGen;
    while (
      withHandlerGen._handler == null ||
      !withHandlerGen._handler.hasOwnProperty(type)
    ) {
      if (withHandlerGen._return == null) break;
      withHandlerGen = withHandlerGen._return;
    }

    if (
      withHandlerGen._handler == null ||
      !withHandlerGen._handler.hasOwnProperty(type)
    ) {
      throw new Error(`Unhandled Effect ${type}!`);
    }

    // found a handler, get the withHandler Generator
    const handlerFunc = withHandlerGen._handler[type];

    const handlerGen = handlerFunc(data, function resume(value) {
      return currentGen => {
        withHandlerGen._return = currentGen;
        runGenerator(performGen, value);
      };
    });

    // will return to the parent of withHandler
    handlerGen._return = withHandlerGen._return;
    runGenerator(handlerGen, null);
  };
} 
```

键码是

```
function resume(value) {
  return currentGen => {
    withHandlerGen._return = currentGen;
    runGenerator(performGen, value);
  };
} 
```

它向处理程序代码中的行`const result = yield resume("Stranger")`赋予其含义。特别是，`withHandlerGen._return = currentGen`划定了从`performGen`(执行效果的发生器)到`currentGen`(执行`yield resume(...)`的发生器)的延续。

你可能已经注意到`withHandler` / `perform`的实现看起来与上一篇文章中的`shift` / `reset`很相似:

*   在货架上做一个特殊的标记
*   在堆栈框架上安装一个处理程序

*   `shift`找到最近的`reset`并成为`reset`的父的直接子

*   `perform`找到与`withHandler`最匹配的&，匹配的处理程序成为`withHandler`父级的直接子级

*   捕获所有中间帧，并将它们具体化为计算的参数

*   捕获所有中间帧，并将它们具体化为匹配处理程序的一个参数

事实上，代数效应可以被看作是定界延拓的一种更结构化的替代方案。

瞧，这就是所有代数效应的作用机制。在这篇文章的剩余部分，我们会看到更多的例子。

### 例 1:逆向测井

我们的第一个例子是一个`log`处理程序，它以相反的顺序打印记录的消息。这可能看起来有点花哨，但应该会给我们一个更坚实的机制的理解。

```
function log(msg) {
  return perform("log", msg);
}

const reverseLog = {
  *log(msg, resume) {
    yield resume();
    console.log(msg);
  }
};

function* main() {
  return yield withHandler(reverseLog, parent());
}

function* parent() {
  yield child();
}

function* child() {
  yield log("A");
  yield log("B");
  yield log("C");
} 
```

在执行第一个`log`效果
之前，让我们看看调用堆栈

```
main() -> withHandler({reverseLog}) -> parent() -> child() 
```

`yield log("A")`之后

```
main() -> log("A", <<withHandler({reverseLog}) -> parent() -> child()>>) 
```

处理程序在记录消息之前调用延续，因此

```
main() -> log("A", <<>>) -> withHandler({reverseLog}) -> parent() -> child() 
```

`yield log("B")`之后

```
main() -> log("A", <<>>) -> log("B", <<withHandler({reverseLog}) -> parent() -> child()>>) 
```

同样，第二个处理程序实例在日志记录之前调用延续，所以

```
main() -> log("A", <<>>) -> log("B", <<>>) -> withHandler({reverseLog}) -> parent() -> child() 
```

`yield log("C")`之后

```
main() -> log("A", <<>>) -> log("B", <<>>) -> log("C", <<withHandler({reverseLog}) -> parent() -> child()>>) 
```

在第三个处理程序实例调用延续
之后

```
main() -> log("A", <<>>) -> log("B", <<>>) -> log("C", <<>>) -> withHandler({reverseLog}) -> parent() -> child() 
```

`child()`、`parent()`、`withHandler({reverseLog})`相继终止，导致下面的调用栈

```
main() -> log("A", <<>>) -> log("B", <<>>) -> log("C", <<>>) 
```

现在，日志将从最右边的堆栈帧开始恢复，以相反的顺序打印消息。

### 例 2:收集日志

这个函数在一个数组中收集日志，而不是记录它们

```
const collectLogs = {
  return(x) {
    return [x, ""];
  },
  *log(msg, resume) {
    const [x, acc] = yield resume();
    return [x, `${msg} {acc}`];
  }
};

function* main() {
  return yield withHandler(collectLogs, parent());
}

function* parent() {
  return yield child();
}

function* child() {
  yield log("A");
  yield log("B");
  yield log("C");
  return 10;
}

start(main(), console.log);
// => [10, "A B C "] 
```

在第三个处理程序实例调用 continuation 之后，我们用
结束

```
main() -> log("A", <<>>) -> log("B", <<>>) -> log("C", <<>>) -> withHandler({collectLogs}) -> parent() -> child() 
```

`child()`将`10`返回给`parent()`，后者将相同的值返回给`withHandler({collectLogs})`

```
main() -> log("A", <<>>) -> log("B", <<>>) -> log("C", <<>>) -> withHandler({collectLogs}) 
```

由于`collectLogs`已经定义了一个`return`子句，该值将由匹配的处理程序处理，这导致`withHandler({collectLogs})`将`[10, ""]`返回给其父`log("C")`。这个把`""` ( `acc`)和`"C"` ( `msg`)串联起来，把`[10, "C "]`返回给`log("B")`。整个过程的结果是返回`[10, "A B C "]`

### 组合处理程序

这里我们编写了两个先例处理程序

```
const reverseLog = {
  *log(msg, resume) {
    yield resume();
    console.log(msg);
    yield log(msg);
  }
};

const collectLogs = {
  return(x) {
    return [x, ""];
  },
  *log(msg, resume) {
    const [x, acc] = yield resume();
    return [x, `${msg}  ${acc}`];
  }
};

function* main() {
  return yield withHandler(collectLogs, withHandler(reverseLog, parent()));
}

// ... rest unmodified

start(main(), console.log);
// => C
// => B
// => A
// => [undefined, "C B A "] 
```

第一个处理程序以相反的顺序打印消息，然后将`log`效果转发给`collectLogs`，因为日志是以相反的顺序转发的，所以它们最终也以相反的顺序收集。

### 结论

还有许多其他的例子(状态，异步，...).一些简单的可以在这里找到。如果你觉得更有冒险精神，你可以参考[ocaml 示例集](https://github.com/kayceesrk/effects-examples)(并非所有示例都适用于 JavaScript)。

这就结束了我们关于代数效果和处理程序的系列。希望这不是太起哄，再次感谢你是一个耐心的读者！

### 有些引用

*   [介绍使用 Eff 语言的代数效果和处理程序](https://www.eff-lang.org/handlers-tutorial.pdf)
*   用 Koka 语言谈论代数效应
*   [代数效果有什么代数的](https://arxiv.org/abs/1807.05923)，如果你觉得比较冒险的话。(提示:在编程世界中，代数运算的数量不是参数的数量，而是可能结果的数量，解释`I^A -> I`可以翻译成`(A -> I) -> I` (function == exponential)，这也是调用其延续`(A -> I)`的 CPS 函数的符号，具有类型`A`的值，与处理程序的符号相同，例如:一个布尔类型有两个可能结果`Bool -> I -> I`可以看作`I^2 -> I`；请不要再问我了！)