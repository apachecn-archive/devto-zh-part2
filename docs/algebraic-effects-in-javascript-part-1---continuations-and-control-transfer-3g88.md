# JavaScript 中的代数效应第 1 部分——延续和控制转移

> 原文：<https://dev.to/yelouafi/algebraic-effects-in-javascript-part-1---continuations-and-control-transfer-3g88>

这是关于代数效果和处理程序系列的第一篇文章。

有两种方法可以解决这个问题:

*   指称:解释代数效应在数学/范畴理论中的意义
*   操作性:通过展示代数效应在选定的运行时环境下是如何操作的来解释代数效应的机制

这两种方法都很有价值，并给出了关于该主题的不同见解。但是，并不是每个人(包括我)，都有掌握范畴论和泛代数概念的先决条件。另一方面，操作方法对于更广泛的程序员来说是容易理解的，即使它没有提供完整的描述。

所以我们就走作战这条路。我们将通过一系列的例子，逐步建立对所介绍概念的直觉。到本系列结束时，我们将有一个基于 JavaScript 生成器的代数效果的工作实现。

由于这将是一个很长的话题，我们将把它分成 4 个部分:

*   首先，我们需要熟悉延续和控制转移的概念
*   下一篇文章我们将看到[如何使用生成器来捕获延续](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-2---capturing-continuations-with-generators-13da)
*   然后我们会看到[如何界定延续的范围](https://dev.to/yelouafi/algebraic-effects-in-javascript-part-3---delimited-continuations-42hj)
*   最后，我们将看到代数效果和处理程序背后的机制

### 直传风格 vs 延续传球风格

在这一部分，我们将围绕一个小型函数式语言的简单解释器的例子来构建我们的概念。该语言将支持数字、加法和调用返回其他表达式的函数。

我们将使用以下函数来构建 AST(抽象语法树)，它将被传递给解释器:

```
function fun(param, body) {
  return { type: "fun", param, body };
}

function call(funExp, argExp) {
  return { type: "call", funExp, argExp };
}

function add(exp1, exp2) {
  return { type: "add", exp1, exp2 };
}

// example
const doubleFun = fun("x", add("x", "x"));
program = call(doubleFun, 10); 
```

解释器接受如上的 AST 并返回一个*最终值*。最终值反映了原子表达式，它不需要进一步的评估(这里是数字或`fun`)并且是目标语言(这里是 JavaScript)的对象，我们将用 JavaScript 函数表示数字和`fun`表达式。

为了评估一个程序，除了程序 AST 之外，解释器还需要一个*环境*来将变量名映射到它们的值。我们将使用一个普通的 JavaScript 对象来表示环境。

下面是解释器的一个可能实现:

```
function evaluate(exp, env) {
  if (typeof exp === "number") {
    return exp;
  }
  if (typeof exp === "string") {
    return env[exp];
  }
  if (exp.type === "add") {
    return evaluate(exp.exp1, env) + evaluate(exp.exp2, env);
  }
  if (exp.type === "fun") {
    return function(value) {
      const funEnv = { ...env, [exp.param]: value };
      return evaluate(exp.body, funEnv);
    };
  }
  if (exp.type === "call") {
    const funValue = evaluate(exp.funExp, env);
    const argValue = evaluate(exp.argExp, env);
    return funValue(argValue);
  }
}

evaluate(program);
// => 20 
```

下面是`evaluate`的工作原理:

*   简单数字按原样返回
*   从当前环境中解析变量。我们现在不处理未知变量
*   加法递归计算其操作数，并返回计算结果的总和
*   对于`fun`action 的情况，我们返回一个 JavaScript 函数，该函数将被调用并带有一个最终值(一些其他评估的结果)。当被调用时，该函数将构建一个新的环境，在该环境中，`fun`参数被绑定到所提供的值，然后它在这个新的环境中评估`fun`主体
*   `call`的情况类似于`add`,我们递归地评估函数和参数表达式，然后将函数值应用于参数值

`evaluate`据说是用*的直接风格*写的。这不是口译员所特有的。一个直接风格的程序仅仅意味着函数通过`return`语句传递它们的结果。例如这个简单的函数也是直接风格:

```
function add(x, y) {
  return x + y;
} 
```

相比之下，在延续传球风格(CPS)中:

1.  该函数将回调作为附加参数
2.  该函数从不返回结果。它总是使用回调来传递它的结果
3.  与你所想的相反。最初，它与 async Node.js 函数无关

比如转换成 CPS，前面的函数就变成:

```
function add(x, y, next) {
  const result = x + y;
  return next(result);
} 
```

提供的回调也被称为*延续*，因为它指定了程序中接下来要做什么。当一个 CPS 函数终止时，它在它的延续上抛出结果。

> **推荐**:作为一个快速练习，尝试将解释器转换成 CPS 形式。首先将延续参数添加到`evaluate`的签名中。

解决方案:

```
function evaluate(exp, env, next) {
  if (typeof exp === "number") {
    return next(exp);
  }
  if (typeof exp === "string") {
    return next(env[exp]);
  }
  if (exp.type === "add") {
    return evaluate(exp.exp1, env, function addCont1(val1) {
      return evaluate(exp.exp2, env, function addCont2(val2) {
        return next(val1 + val2);
      });
    });
  }
  if (exp.type === "fun") {
    // notice the function value becomes a CPS itself
    const closure = function(value, next) {
      const funEnv = { ...env, [exp.param]: value };
      return evaluate(exp.body, funEnv, next);
    };
    return next(closure);
  }
  if (exp.type === "call") {
    return evaluate(exp.funExp, env, function callCont1(funValue) {
      return evaluate(exp.argExp, env, function callCont2(argValue) {
        return funValue(argValue, next);
      });
    });
  }
}

function run(program) {
  return evaluate(program, {}, x => x);
} 
```

以下是需要注意的事项:

1.  每个`return`语句要么调用延续，要么调用另一个 CPS 函数
2.  所有这些调用都在[尾部调用位置](https://en.wikipedia.org/wiki/Tail_call)
3.  在我们需要评估多个表达式的情况下(`add`和`call`情况)，我们通过提供捕获中间结果的中间延续来链接这些评估。当链接终止时，我们将结果抛出到主延续上
4.  直接风格的生活更美好

在这个阶段，程序已经很难阅读了。所以你可能会问

### 为什么我们要用这种风格写程序？

简而言之:你没有。但这并不意味着 CPS 毫无用处。

有各种各样的原因使得 CPS 变得有用甚至更好，但是并不是所有的原因都适用于 JavaScript(就其目前的状态而言)。

1.  首要的是控制。在直接风格版本中，调用者控制下一步做什么，延续是隐式的，对我们是隐藏的。然而，在 CPS 版本中，延续是显式的，并作为参数传递，被调用方可以通过调用延续来决定下一步做什么。正如我们将在下一节中看到的，CPS 可以用来实现各种控制流，而这在直接风格中是不可能的

2.  第二，所有的函数调用都在 CPS 的尾部调用位置。尾部调用不需要增加调用堆栈(在下一节解释)。因为在尾部调用之后没有什么要做的，所以在执行尾部调用之前不必保存执行上下文。编译器可以通过用被调用的函数的上下文直接替换当前的执行上下文来优化这些尾部调用(而不是将其放在当前的执行上下文之上)。这个过程被称为尾调用消除，被函数编译器大量利用。不幸的是，尽管是 ECMAScript 规范的一部分，当前的 JavaScript 引擎并没有全部实现尾部调用消除

3.  当然，最重要的是所需的异步，因为 JavaScript 是单线程的。如果我们要使用直接风格的函数来执行远程请求，我们将不得不挂起我们仅有的线程，直到请求被满足，阻塞当前语句上的进程，同时阻止任何其他交互。CPS 提供了一种方便有效的方式来*分叉*一些工作，因此当前代码可以继续执行和处理其他交互。事实上，人们可能认为这是在 JavaScript 中使用这种风格的唯一实际原因

4.  最后， **CPS 非常强大，但并不意味着人类可以直接使用它**。这是一个更适合编译器或解释器的目标。我们的大脑更适应结构化的直接风格。因此，虽然我们自己不会用 CPS 来编写，但它仍然是幕后解释者使用的一个强大工具。在接下来的文章中，我们将看到如何在幕后利用 CPS 的力量来呈现一个更强大的直接风格 API

出于我们的目的，原因 1、3 和 4 适用。我们需要对代码进行更灵活的控制，我们需要在恢复直接风格的同时处理异步问题。

目前，JavaScript 中惯用的解决方案是使用 async/await，这实际上给了我们 3 和 4，而不是 1。我们没有足够的力量来控制流量。

### 什么是控制流？

> 控制流是命令式程序的单个语句、指令或函数调用被执行或评估的顺序([维基百科](https://en.wikipedia.org/wiki/Control_flow))。

默认情况下，在 JavaScript 这样的命令式语言中，语句是顺序执行的(在 CPU 级别，*指令指针*会自动递增，除非你执行一个控制转移指令)。但是该语言也提供了一些控制操作符来改变这种行为。例如，当我们在一个循环中`break`时，控制跳转到循环块后面的第一条指令。类似地，如果条件评估为假，`if`可能跳过整个块。所有这些都是局部控制转移的例子，意味着在同一个函数内部发生转移。

一种重要的控制转移机制是函数调用。这要归功于一种叫做调用栈的数据结构。[这个短视频](https://www.youtube.com/watch?v=Q2sFmqvpBe0)很好的解释了机理(PS 值得一看)。

请注意，在视频中，调用者是如何在被调用者返回后推送指向下一条指令的返回地址的。这看起来非常类似于我们如何提供延续作为 CPS 函数的附加参数。然而，对于调用堆栈，我们对这种延续没有任何权力。当一个函数终止时，控制权会自动转移给调用者。在 CPS 中，我们确实有这种能力，因为延续被具体化为一个正常的函数。

> 这并不意味着我们没有在 CPS 模式下使用调用堆栈。CPS 调用仍然使用调用堆栈，但不依赖它进行控制转移(这是我们从不返回的原因)。这意味着调用堆栈会随着每一步而增长。对于支持尾部调用优化的编译器来说，这不成问题(因为 CPS 调用总是在尾部位置)，但是如果我们的进程的重要部分是同步的(比如大量的递归调用)，它可以在 JavaScript **这样的语言中实现。但是因为我们在这里主要使用 CPS 来处理异步调用，所以我们没有这个问题。**

**异常**代表非本地控制转移的一种常见形式。抛出异常的函数可能会导致控件跳转到调用层次结构中更高层的另一个函数。

```
function main() {
  try {
    // ...
    child1();
    // ...
  } catch (something) {
    console.log(something);
  }
}

function child1() {
  // ...
  child2();
  workAfterChild2();
}

function child2() {
  // ...
  throw something;
  //...
} 
```

`throw`绕过中间函数调用，以到达最近的处理程序。当我们到达`catch`子句时，所有的中间堆栈帧都被自动丢弃。在上面的例子中，跳过了对`child1`的中间调用中剩余的`workAfterChild2()`。因为这是由编译器隐式管理的，所以我们没有任何方法来恢复跳过的工作。我们将在以后讨论代数效应时回到这个机制。

为了说明 CPS 如何实现其他控制流，我们将向我们的解释器添加错误处理，而不依赖于本地 Javascript 异常。诀窍是，除了正常的完成延续，提供另一个绕过下一步并中止整个计算的完成延续。

```
function evaluate(exp, env, abort, next) {
  if (typeof exp === "number") {
    return next(exp);
  }
  if (typeof exp === "string") {
    if (!env.hasOwnProperty(exp)) {
      return abort(`Unkown variable ${exp}!`);
    }
    return next(env[exp]);
  }
  if (exp.type === "add") {
    return evaluate(exp.exp1, env, abort, function cont1(val1) {
      if (typeof val1 != "number") {
        return abort("add called with a non numeric value");
      }
      return evaluate(exp.exp2, env, abort, function cont2(val2) {
        if (typeof val2 != "number") {
          return abort("add called with a non numeric value");
        }
        return next(val1 + val2);
      });
    });
  }
  if (exp.type === "fun") {
    // notice the function value becomes a CPS itself
    const closure = function(value, abort, next) {
      const funEnv = { ...env, [exp.param]: value };
      return evaluate(exp.body, funEnv, abort, next);
    };
    return next(closure);
  }
  if (exp.type === "call") {
    return evaluate(exp.funExp, env, abort, function cont1(funValue) {
      if (typeof funValue != "function") {
        return abort("trying to call a non function");
      }
      return evaluate(exp.argExp, env, abort, function cont2(argValue) {
        return funValue(argValue, abort, next);
      });
    });
  }
}

function run(program) {
  return evaluate(program, {}, console.error, x => x);
}

run(add("x", 3), 10);
// => Unkown variable x!

run(call(5, 3), 10);
// => 5 is not a function 
```

我们将通过添加一个特性来结束这一部分，这个特性将让您对捕获的延续有一个初步的了解:`escape`操作符。

为了了解`escape`是如何工作的，考虑下面的例子:

```
// ie: (x => x + x)(3 + 4)
call(fun("x", add("x", "x")), add(3, 4)); 
```

其计算结果为`14`。如果我们像这样把它包在`escape`操作符里面

```
// escape (eject) in (x => x + x)(3 + eject(4))
escape(
  "eject", // name of the eject function
  call(fun("x", add("x", "x")), add(3, call("eject", 4)))
); 
```

我们改为获取`4`，因为`eject`函数用提供的值中止整个表达式。

下面是我们的代码需要添加的内容。实现出奇的短:

```
function escape(eject, exp) {
  return { type: "escape", eject, exp };
}

function evaluate(exp, env, abort, next) {
  //...
  if (exp.type === "escape") {
    const escapeEnv = { ...env, [exp.eject]: next };
    return evaluate(exp.exp, escapeEnv, abort, next);
  }
}

run(escape("eject", call(fun("x", add("x", "x")), add(3, call("eject", 4)))));
// => 4 
```

我们所需要的就是将`eject`参数绑定到转义表达式的当前延续。

### 结论

第一部分的主要要点:

1.  直接风格依赖于调用堆栈进行控制转移
2.  在直接风格中，函数之间的控制转移是隐式的，对我们是隐藏的。函数必须总是返回到它的直接调用者
3.  您可以使用例外进行非本地控制转移
4.  CPS 函数从不返回结果。它们接受表示当前代码延续的附加回调参数
5.  在 CPS 中，控制转移不依赖于调用堆栈。它通过提供的延续变得显式
6.  CPS 可以模拟本地和非本地控制转移，但是...
7.  **CPS 不是供人类使用的东西，手写的 CPS 代码很快变得不可读**
8.  请务必阅读前面的句子

下一部分，我们将了解如何使用发电机来:

*   恢复直接风格
*   需要时捕获延续
*   未限定的和限定的延续之间的区别

感谢您耐心阅读！