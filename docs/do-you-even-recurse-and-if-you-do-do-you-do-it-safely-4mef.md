# 你会复发吗？...如果你做了，你做的安全吗？

> 原文：<https://dev.to/pichardoj/do-you-even-recurse-and-if-you-do-do-you-do-it-safely-4mef>

## 背景

你写过`factorial`函数吗？如果有，那么你可能会做类似于:
的事情

```
function factorial(n) {
  let result = n;

  for (let i = n - 1; i > 1; i++) {
    result = result * i;
  }

  return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

甚至类似于:

```
function factorial(n) {
  return a > 1 
    ? n * factorial(n - 1) 
    : 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

这两种方法都是有效的，但是第二种方法更容易理解它在做什么，我们可以很容易地理解`factorial`做的是`n * (n - 1)!`，它调用自己，直到`n`等于`1`，然后我们完成，这就是我们所说的递归函数:

> 调用自身或处于潜在的函数调用循环中的函数。

## 问题

递归很棒，它帮助我们写出更简洁、易读和简单的代码。然而，关于递归有一个很大的缺点，比如我们的`factorial`函数，当我们调用`factorial(5)`时，我们得到`120`，然而，如果我们用一个更大的值调用同一个函数，比如说`12,000`，我们得到一个完全不同的结果:

> 范围错误:超出了最大调用堆栈大小

你看，每个运行时都有一个最大堆栈大小(node 有一个大约 11k 的限制)，所以当我们进行长递归循环时，我们的程序会崩溃，因为没有更多的堆栈空间。

## 解

幸运的是，有一种替代方法可以让我们安全地编写递归函数，**尾调用优化**。

TCO 是许多语言已经实现的处理长递归链的过程。它基于这样一个前提:当一个过程/函数调用一个子例程作为它的最终动作时，就有可能用新调用的框架替换当前的调用堆栈框架，因此，与该函数的循环版本一样有性能。

那么，我们如何修改我们的`factorial`函数来满足这个约束呢？我们可以这样做:

```
function factorial(n, acc = 1) {
  return n > 1 
    ? factorial(n - 1, n * acc) 
    : acc;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们添加了一个属性`acc`，它允许我们将任何相关信息(我们当前的累积积)传递给下一个阶乘调用，因此，使之前调用的所有信息都变得无用，并允许我们删除那个堆栈帧，因此，我们将替换同一个帧 11k+次，而不是 11k+个堆栈帧。

很漂亮吧？

可悲的是，尽管 TCO 是 javascript 规范的一部分，但许多引擎已经决定不实现它。

## 一个有趣的选择

尽管如此，仍然有一种使用递归的安全方法。我们可以实施我们自己版本的 TCO。

根据我们对 TCO 的了解，我们的目标应该是为递归函数**提供一种方式，让我们保持一个恒定的大小**，而不是堆栈大小的线性增长，所以让我们问问自己，我们知道什么样的控制流结构会有这种行为？循环！那么，如果我们有一个重复执行函数的循环会怎么样呢？这就是我们所说的**蹦床**。

蹦床是一种特殊的循环，它执行**被链接的函数**，即返回下一个要调用的函数的函数。那么，如果我们把每个递归调用转换成一个 thunk，并把它传递给一个 trampoline，会怎么样呢？我们的堆栈会保持不变的大小吗？让我们看看:

首先，我们必须将阶乘函数重写为 thunked 函数，类似于:

```
function factorial(n, ret = res => res) {
  return n > 1 
    ? () => factorial(n - 1, res => ret(n * res)) 
    : ret(1);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们分析一下我们在那里做了什么，好吗？

1.  我们在函数签名中添加了一个参数`ret`，正如你所看到的，这是一个函数，它实现了一个特殊的角色，它允许我们构造我们的 thunks。
2.  我们现在返回一个函数，而不是阶乘计算的值，通过这样做，我们打算推迟该函数的执行，直到我们的蹦床决定调用它。

所以让我们进入我们的蹦床实现。

正如我们所说的，蹦床是一个一次执行一个函数的循环，因此，利用装饰器模式，我们可以编写下面的代码:

```
function trampoline(fn) {
  return function(...args) {
    let result = fn(...args);

    while (result && typeof result === 'function') {
      result = result();
    }

    return result;
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所意识到的，实现相当简单，我们用蹦床来装饰我们的递归函数，以降低 TCO。这里有一些值得注意的地方:

1.  `while`一直运行，直到不再有`functions`可以调用。
2.  我们的`fn`参数只在开始时使用，因为每个结果代表下一个要调用的`function`。

因此，我们的最终结果将类似于:

[https://repl.it/@pichardoJ/doYouEvenRecureBro?lite=true](https://repl.it/@pichardoJ/doYouEvenRecureBro?lite=true)

正如你所看到的，我们的调用堆栈从不通过`13`帧，这允许我们处理更长的递归链，而不用担心堆栈溢出。

**多一点点**

即使`trampoline`函数工作得很好，我仍然会在我们的 API 中添加一些东西，一个`Symbol`！是的，ES6 允许我们进行元编程，所以我最终的实现是:

```
function factorial(n, ret = res => res) {
  return n > 1
    ? {
        fn: () => factorial(n - 1, res => ret(n * res)),
        [Symbol.for('recurse')]: true
      }
    : ret(1);
}

function trampoline(fn) {
  return function(...args) {
    let result = fn(...args);

    while (result && result[Symbol.for('recurse')]) {
      result = result.fn();
    }

    return result;
  };
}

// Or with Decorator syntax

@trampoline
function factorial(n, ret = res => res) {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们就能确保在应该停下来的时候停下来，而不是之后。

## 大结局

递归很棒，是函数式声明性编程的支柱之一，然而，它有一个有趣的缺点，可能会导致一些意想不到的问题。这里我们看到了如何使用尾部调用来优化递归调用。同样重要的是要注意，通过使执行路径变得更复杂，性能(时间方面)会降低，所以使用这种方法时要慎重，避免在不需要的地方增加额外的复杂性。

我希望这篇文章对你有用，请让我知道你对 TCO 实现的想法。

**p.s.** 在做这个主题的研究时，我偶然发现了[这篇](https://eli.thegreenplace.net/2017/on-recursion-continuations-and-trampolines/)很棒的文章，它提到了使用 python 生成器实现类似效果的可能性，所以我将研究一种使用 ES6 生成器来改进我们优化递归调用的方法。