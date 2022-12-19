# JavaScript 中的代数效应第 2 部分——用生成器捕获延续

> 原文：<https://dev.to/yelouafi/algebraic-effects-in-javascript-part-2---capturing-continuations-with-generators-13da>

这是关于代数效果和处理程序系列的第二部分。

*   第 1 部分:[延续和控制权转移](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-1---continuations-and-control-transfer-3g88)
*   **第 2 部分:用生成器捕获延续**
*   第 3 部分:[分隔的延续](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-3---delimited-continuations-42hj)
*   第四部分:[代数效果和处理程序](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-4---implementing-algebraic-effects-and-handlers-2703)

在第一篇文章中，我们介绍了延续和控制转移的概念。我们看到了用延续传递风格(CPS)编写的程序在控制转移操作方面更加灵活。在直接风格中，控制转移是由编译器通过调用栈隐式管理的，而在 CPS 延续中，控制转移被具体化为 CPS 函数的第一类参数。

然而，CPS 程序的一个主要缺点是它们更难被人类读写，所以它们更适合被其他程序如编译器或解释器操作。这就是为什么公开延续的编程语言经常提供直接风格的语法/API 来操作它们。

在这一部分，我们将在 JavaScript 中做同样的事情。尽管这种语言没有提供访问延续的方法，但是我们总是可以使用生成器函数来模拟它们。

> 这篇文章假设读者熟悉生成器函数。

### 直接驱动发电机

假设我们有这个简单的函数

```
function greet(name) {
  const message = `Hi ${name}`;
  return message;
}

greet("Stranger");
// => "Hi Stranger" 
```

运行这个功能就像`const result = greet(someString)`一样简单。现在，如果我们采用发电机版本

```
function* greet(name) {
  const message = yield `Hi ${name}`;
  return message;
}

greet("Stranger");
// => greet { <suspended>, __proto__: Generator, ... } 
```

我们只得到生成器对象。为了得到结果，我们需要单步执行生成器，直到它完成。下面是驱动生成器并返回其结果的函数代码

```
function runGenerator(gen, arg) {
  const { done, value } = gen.next(arg);
  if (done) {
    return value;
  }
  return runGenerator(gen, value);
}

runGenerator(greet("Stranger"));
// => "Hi Stranger" 
```

Works greet，但是就像普通函数可以调用其他普通函数一样，我们也希望我们的生成器可以调用其他生成器。例如，这是阶乘函数
的生成器版本

```
function* factorial(n) {
  if (n === 0) return 1;
  const n1 = yield factorial(n - 1);
  return n * n1;
}

runGenerator(factorial(10));
// => NaN 
```

幸运的是，生成器允许我们截取生成的值。这使我们能够根据需要解释这些值，然后用解释的结果恢复生成器。

在我们的例子中，解释子生成器相当于递归地运行它们并得到它们的结果。

```
function isGenerator(x) {
  return x != null && typeof x.next === "function";
}

function runGenerator(gen, arg) {
  const { done, value } = gen.next(arg);
  if (done) {
    return value;
  }
  // interpret calls to child Generators
  if (isGenerator(value)) {
    const result = runGenerator(value);
    return runGenerator(gen, result);
  }
  return runGenerator(gen, value);
}

runGenerator(factorial(10));
// => 3628800 
```

到目前为止，我们可以像普通函数一样调用生成器，包括嵌套调用和递归调用。看起来我们已经能够模拟调用堆栈了。注意这里我们只是重用了底层的 JavaScript 调用栈。

然而，正如我们在上一篇文章中看到的，直接风格不能处理异步问题。CPS 允许我们执行异步调用，但这是有代价的。我们的下一步是在保留直接风格的同时允许这些调用。

### 驱动 CPS 中的发电机

假设我们想要实现一个`sleep`函数，当它在生成器中被生成时，将暂停执行一段时间

```
function* slowDouble(x) {
  yield sleep(2000);
  return x * 2;
} 
```

在当前形式下，`runGenerator`无法实现`sleep`行为，因为它递归/同步运行，直到完成。

为了允许异步调用，我们需要在 CPS 中重写函数:记住在这种风格中我们不返回函数结果，而是将它们传递给提供的 continuation

```
function runGenerator(gen, arg, next) {
  const { done, value } = gen.next(arg);
  if (done) {
    next(value);
  } else if (isGenerator(value)) {
    runGenerator(value, null, function(result) {
      runGenerator(gen, result, next);
    });
  } else {
    runGenerator(gen, value, next);
  }
} 
```

但我们还没到那一步。到目前为止，我们只能生成子生成器或普通值。我们需要一种方法来表示异步调用，我们需要解释给定的表示。

一个简单的解决方案是将异步调用本身表示为 CPS 函数。假设我们写了一个 CPS `sleep`版本

```
function sleep(millis, next) {
  setTimeout(next, millis);
} 
```

如果我们把它做成咖喱

```
function sleep(millis) {
  return next => setTimeout(next, millis);
} 
```

咖喱版更适合搭配`runGenerator`使用。我们可以简单地插入一个 continuation 来恢复带有异步结果的生成器。更一般地说，我们将用接受单个回调的函数来表示异步调用。我们称这些函数为*暂停计算*。

```
function runGenerator(gen, arg, next) {
  const { done, value } = gen.next(arg);
  if (done) {
    next(value);
  } else if (isGenerator(value)) {
    runGenerator(value, null, function continuation(result) {
      runGenerator(gen, result, next);
    });
  } else if (typeof value === "function") {
    // here we handle suspended computations
    value(function continuation(result) {
      runGenerator(gen, result, next);
    });
  } else {
    runGenerator(gen, value, next);
  }
}

runGenerator(slowDouble(10), null, console.log);
// tic tac toc
// 20 
```

对于已经熟悉基于生成器的异步实现的读者来说，这看起来就像是老的管道技巧。但是请注意，我们提供给挂起的计算的回调表示整个程序的继续，所以现在我们可以完全控制下一步做什么。换句话说，我们获得了 CPS 的灵活性，同时仍然可以编写直接风格的代码。

作为一个简单的例子，这里有一个模拟调试器的`break`的例子。我们没有调用 continuation，而是将它保存在一个变量中，然后暂停整个程序。

```
let resume;

const BREAK = next => {
  console.log("**PAUSED**");
  resume = next;
};

function* main() {
  yield breakTest();
  yield sleep(1000);
  console.log("end of main");
}

function* breakTest() {
  for (let i = 1; i < 5; i++) {
    yield sleep(1000);
    console.log("message", i);
    if (i % 2 === 0) yield BREAK;
  }
}

// typing this in the console
runGenerator(main(), null, console.log);
/*
  message 1
  message 2
  **** PROGRAM PAUSED ****
*/
resume();
/*
  message 3
  message 4
  **** PROGRAM PAUSED ****
*/
resume();
// end of main 
```

另一个例子是一个`exit(result)`函数，当它从一个深度嵌套的生成器中产生时，将跳过所有的父代并中止整个计算，给出结果。例如，考虑下面的代码

```
function* main() {
  const result = yield parent();
  return `main result: (${result})`;
}

function* parent() {
  const result = yield child();
  return `parent result: (${result})`;
}

function* child() {
  return "child result";
}

runGenerator(main(), null, console.log);
// => main result: (parent result: (child result)) 
```

使用`exit`我们可以直接从`child`
内部中止

```
function main() { ... }

function parent() { ... }

function* child() {
  yield exit("child result");
  throw "This shouldn't happen";
}

runGenerator(main(), null, console.log);
// should be => child result 
```

> 如果您还记得上一篇文章中的解释器示例，我们在某个时候做了同样的事情，为所有子 CPS 函数提供顶级延续作为第二个参数。我们可以用`runGenerator`做同样的把戏。这将是一次很好的锻炼。

### 通往未被删除的延续之路

好吧，我真诚地假设，你做了最后一个练习。以下是我的解决方案

```
function runGenerator(gen, arg, abort, next) {
  const { done, value } = gen.next(arg);
  if (done) {
    next(value);
  } else if (isGenerator(value)) {
    runGenerator(value, null, abort, function continuation(result) {
      runGenerator(gen, result, abort, next);
    });
  } else if (typeof value === "function") {
    value(abort, function continuation(result) {
      runGenerator(gen, result, abort, next);
    });
  } else {
    runGenerator(gen, value, abort, next);
  }
}

// helper function to thread in the top-level continuation
function start(gen, next) {
  runGenerator(gen, null, next, next);
}

start(main(), console.log);
// => child result 
```

它工作，但是它不是非常令人满意。我们说过 CPS 的承诺是授权给我们，API 的最终用户，所以我们可以实现各种控制操作符。但是在上面的解决方案中，控件是硬编码在解释器内部的(`runGenerator`)。我们不想每次添加控制结构时都修改解释器，更重要的是，我们不想用低级 CPS 代码实现我们的解决方案。我们真正的目标是提供一些更通用的 API，以便在用户领域实现`exit`或其他控制流。

我们一步一步来。首先，观察`start`所做的，本质上是*捕获*顶级延续。但是我们知道我们可以通过在生成器中产生一个挂起的计算来捕获一个延续。因此，我们的第一步是捕获顶级延续。

为此，我们将使`start`本身成为一个生成器，并捕获它的延续。

```
function* start(genFunc) {
  const result = yield function(abort) {
    runGenerator(genFunc(abort), null, abort);
  };
  return result;
} 
```

我们正在手动使用`runGenerator`,这有点难，但是这并没有改变我们的解释器。稍后我们将看到如何抽象出这段代码。

接下来，我们观察到捕获的 continuation 只是作为一个附加参数传递给嵌套的`runGenerator`调用，以保持它在当前范围内可见。我们可以通过利用生成器的词法范围并将捕获的延续作为参数传递给子生成器来实现同样的目的。

我们第一次尝试性的重构产生了下面的代码

```
function* start(genFunc) {
  const result = yield function(abort) {
    runGenerator(genFunc(abort), null, abort);
  };
  return result;
}

function* main(abort) {
  const result = yield parent(abort);
  return `main result: (${result})`;
}

function* parent(abort) {
  const result = yield child(abort);
  return `parent result: (${result})`;
}

function* child(abort) {
  yield next => abort("child result");
  throw "This shouldn't happen";
}

runGenerator(start(main), null, console.log);
// => child result 
```

顺便说一句，注意在`child`中，`next`延续在挂起的计算体中是如何被忽略的，而是调用`abort`。这意味着下一条语句`throw "This shouldn't happen"`不会被执行，控制将直接跳回`start`生成器。

但是我们还没有到那一步，我们如何实现通用的`exit(result)`函数呢？

根据目前的规定，我们不能。如果这个在作用域中不可见，我们的`exit`就无法获得`abort`延续。这肯定很尴尬，我们不想每次想退出的时候都写`yield next => abort(result)`。

不过，还有更好的选择。我们可以在捕获顶级延续的代码中创建`exit`本身(这里是在`start`生成器中)，然后将它传递给子生成器，而不是转发捕获的延续本身，然后在现有函数中创建挂起的计算(`exit`)。

```
function* start(genFunc) {
  const result = yield function(abort) {
    function exit(value) {
      return next => abort(value);
    }
    runGenerator(genFunc(exit), null, abort);
  };
  return result;
}

function* main(exit) {
  const result = yield parent(exit);
  return `main result: (${result})`;
}

function* parent(exit) {
  const result = yield child(exit);
  return `parent result: (${result})`;
}

function* child(exit) {
  yield exit("child result");
  throw "This shouldn't happen";
}

runGenerator(start(main), null, console.log);
// => child result 
```

为了完成重构，我们所需要的就是抽象出代码，这些代码在一个可重用的函数中捕获顶级延续。但是首先我们需要为它取一个合适的名字。`call_with_current_continuation`看起来很有表现力但又相当啰嗦，我们就简称为 [`callcc`](https://en.wikipedia.org/wiki/Call-with-current-continuation) 。

```
function callcc(genFunc) {
  return function(capturedCont) {
    // this is our previous exit
    function jumpToCallccPos(value) {
      return next => capturedCont(value);
    }
    runGenerator(genFunc(jumpToCallccPos), null, capturedCont);
  };
}

function* start() {
  const result = yield callcc(main);
  return result;
}

// rest of the code unmodified

runGenerator(start(), null, console.log);
// => child result 
```

注意，不像在像`Scheme`这样的语言中，我们的实现只允许调用一次`callcc`延续。这里我们受限于 JavaScript 中生成器的工作方式。对`generator.next()`的每个调用都是一张单程票，所以多次调用 continuation 只会让生成器继续前进。只能恢复一次的延续据说是*一击*。可以多次恢复的延续据说是*多拍*。

> 在这个系列中，我们将满足于一次性的延续。如果你对我们如何模拟多重连续感兴趣，这里有一个例子。请注意，这具有不可忽略的空间/时间成本。

这篇文章的其余部分用几个常见的例子说明了`callcc`的用法。

### 例 1:仿效 try/cacth

前面的`exit`示例实现了异常的简化版本。接下来，我们将尝试制作一个更详细的结构化
异常处理
的例子

```
const handlerStack = [];

function* trycc(computation, handler) {
  return yield callcc(function*(k) {
    handlerStack.push([handler, k]);
    const result = yield computation;
    handlerStack.pop();
    return result;
  });
}

function* throwcc(exception) {
  const [handler, k] = handlerStack.pop();
  const result = yield handler(exception);
  yield k(result);
} 
```

`trycc/throwcc`模拟`try/catch/throw`语句。`trycc`从捕获当前延续开始，将它和处理程序一起保存在一个堆栈中，然后运行计算，这个计算可能会(也可能不会)抛出。如果计算成功返回，那么没有抛出异常，我们可以从堆栈中删除处理程序。在计算已经调用了`throwcc`的情况下，我们也弹出处理程序堆栈和捕获的延续，运行处理程序，然后使用捕获的延续跳转回`trycc`被调用的地方。

### 例 2:协同调度

另一个流行的例子是使用我们称之为*协程*的协作调度的实现。它们有点类似于发电机。一旦启动，协程执行一些代码，然后可能让位于中央调度程序。调度程序将保存协程的状态，然后选择另一个协程来运行。下面是一个例子

```
function* main() {
  yield fork(proc("1", 4));
  yield fork(proc("2", 2));
  yield dequeue();
  console.log("end main");
}

function* proc(id, n) {
  for (let i = 0; i <= n; i++) {
    yield sleep(1000);
    console.log(id, i);
    yield pause;
  }
} 
```

假设我们已经实现了`fork`和`pause`，运行`main()`的结果给出了以下输出

```
 1 0
  2 0
  1 1
  2 1
  1 2
  2 2
  1 3
  1 4
  end main 
```

下面给出了协程的一种可能的实现方式

```
const processQueue = [];

function fork(gen) {
  return next => {
    processQueue.push(
      (function*() {
        yield gen;
        yield dequeue();
      })()
    );
    next();
  };
}

const pause = callcc(function*(k) {
  processQueue.push(k());
  yield dequeue();
});

function* dequeue() {
  if (processQueue.length) {
    const next = processQueue.shift();
    yield next;
  }
} 
```

下面是上面代码的工作原理

*   不立即启动所提供的协程，它只是将它添加到一个全局进程队列中
*   通过捕获当前协程的延续，将其添加到进程队列，然后选择下一个要恢复的协程，保存当前协程的状态
*   `dequeue`在协程暂停和返回时都会被调用

### 结论

瞧啊。我们到了第二部分的结尾。只需再发几个帖子来完成对代数效应和处理程序的理解。

这部分的主要要点:

*   当使用直接风格驱动时，生成器可以模拟调用栈，但是不支持异步调用
*   当使用 CPS 驱动时，生成器可以执行异步工作，同时仍然允许用户以直接方式编程
*   更重要的是，我们可以在任何需要的时候捕获程序的当前状态(`callcc`)
*   当调用`callcc`延续时，它中止当前执行上下文，并从调用`callcc`时开始恢复

虽然`callcc`相当强大，但它有一个主要的局限性。捕获的延续表示整个程序的其余部分。这意味着`yield k(someValue)`不能返回值，因为我们所能做的就是继续，直到程序完成。这种延续被称为*无限延续*。

下一部分，我们将看到一种更强大的类型:*分隔延续*，它允许我们只捕获程序剩余部分的一部分。分隔的延续可以返回值，因此它可以在其他函数中进行组合。

下一篇文章再见。感谢您成为一名耐心的读者！