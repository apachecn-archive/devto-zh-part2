# 如何让函数在 Javascript 中部分适用

> 原文：<https://dev.to/ycmjason/how-to-make-functions-partially-applicable-in-javascript--416b>

## TL；速度三角形定位法(dead reckoning)

```
const enablePartialApplication = (fn) => (...args) => {
    if (args.length >= fn.length) return fn(...args);
    return enablePartialApplication(fn.bind(null, ...args));
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 什么是部分函数应用？

部分函数应用描述了将一些参数部分应用于函数的能力。听起来很抽象？让我们看一些例子。

假设我们有一个函数`add`，它简单地将两个数相加:

```
const add = (x, y) => x + y;
add(3, 5); // 8 
```

Enter fullscreen mode Exit fullscreen mode

如果我们只提供一个参数，那么结果就是`NaN`。

```
add(3) // basically equivalent to: add(3, undefined)
// -> 3 + undefined
// -> NaN 
```

Enter fullscreen mode Exit fullscreen mode

很简单吗？

然而，一些函数式编程语言会对此进行不同的处理。例如，如果 Haskell 要处理`add(3)`，而不是执行函数体`x + y`，它将做如下事情:

```
// let's pretend haskell is running the following javascript
const addThreeTo = add(3);
// addThreeTo is basically the function: (y) => 2 + y
addThreeTo(5); // 8
// or simply
add(3)(5); // 8

// but we could still do
add(3, 5); // 8 
```

Enter fullscreen mode Exit fullscreen mode

注意我们如何通过做`addThreeTo(5)`来提供第二个参数`5`。

Haskell 检测提供的参数数量。当它少于函数定义中的预期值时，它不执行函数体，而是返回另一个接受剩余参数的函数。这就是所谓的部分功能应用。

好吧。那很酷。但是我们为什么需要这个呢？你不知道。但这是非常方便的东西。

想象一下，有一个数字列表，我们希望给每个数字加上`5`，如果没有部分函数应用，我们必须用一个箭头函数将`add`括起来。

```
const add = (x, y) => x + y;
[1, 2, 3, 4, 5, 6].map(i => add(5, i)); 
```

Enter fullscreen mode Exit fullscreen mode

然而，对于部分函数应用程序，我们可以做类似于:
的事情

```
// let's pretend haskell is running the following javascript
const add = (x, y) => x + y;
[1, 2, 3, 4, 5, 6].map(add(5)); 
```

Enter fullscreen mode Exit fullscreen mode

### 实盘

部分函数应用也可以被认为是固定参数。

```
// let's pretend haskell is running the following javascript
const fn = (x, y, z) => (x * y) / z;
const fnFixedX = fn(3); // fixes x to 3: (y, z) => (3 * y) / z
fnFixedX(2, 1); // (3 * 2) / 1 -> 6 
```

Enter fullscreen mode Exit fullscreen mode

或者我们也可以一起修复`x`和`y`。

```
// let's pretend haskell is running the following javascript
const fn = (x, y, z) => (x * y) / z;
const fnFixedXY = fn(3, 2); // fixes x to 3: (z) => (3 * 2) / z
fnFixedXY(1); // (3 * 2) / 1 -> 6 
```

Enter fullscreen mode Exit fullscreen mode

我希望你现在已经明白什么是部分函数应用了。如果没有，请在继续之前再读一遍。

## 实现

在前面的例子中，我们假设 haskell 正在运行 javascript，以说明什么是部分功能应用程序。现在，我们是否可以在 Javascript 中实现部分功能应用程序呢？是的，我们可以！

让我们定义一个函数`enablePartialApplication(fn)`，它将返回一个我们可以使用部分函数应用程序的函数。

我们想要达到的目标:

```
const sum = enablePartialApplication((x, y) => x + y);
const sum3 = sum(3);
sum3(10); // 13
sum(3, 5); // 8
sum(3)(4); // 7 
```

Enter fullscreen mode Exit fullscreen mode

再比如:

```
const fn = enablePartialApplication((x, y, z) => (x * y) / z);
fn(3, 2, 1); // (3 * 2) / 1 -> 6
fn(3, 2)(1); // 6
fn(3)(2, 1); // 6
fn(3)(2)(1); // 6 
```

Enter fullscreen mode Exit fullscreen mode

### 骷髅

函数的输入和输出是显而易见的，因此我们可以勾画出函数的框架:

```
const enablePartialApplication = (fn) => {
    return () => {

    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 看着争论

正如我前面提到的，Haskell 查看 1)传入的参数数量和 2)函数定义期望的参数数量，以决定是否需要部分应用。

第一个很简单，我们可以使用 rest 操作符，以列表的形式接收参数，然后对其执行`.length()`。

```
const enablePartialApplication = (fn) => {
    return (...args) => { // use rest operator to take arguments as a list
        args.length // number of arguments passed in
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

第二个也没那么难，我们可以用`Function.length`。文档见[此处](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/length)。

```
const enablePartialApplication = (fn) => {
    return (...args) => {
        args.length // number of arguments passed in
        fn.length // number of arguments expected by fn
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们知道如果`args.length < fn.length`是部分函数应用，否则，即`args.length >= fn.length`，只是简单的函数调用。所以让我们把这个放进我们的函数:

```
const enablePartialApplication = (fn) => {
    return (...args) => {
        if (args.length >= fn.length) return fn(...args); // function invocation
        // partial function application here
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 与`Function.prototype.bind`斗法

回想一下 argument fixing 类比，它是否让你想起了 javascript 中的一些函数？是啊！`Function.prototype.bind`！我们可以用它来修改函数的参数！

```
const add = (x, y) => x + y;
const add3 = add.bind(null, 3); // the first argument is the thisArg
add3(5); // 8
add3.length; // 1 
```

Enter fullscreen mode Exit fullscreen mode

我们可以简单地将输入`args`绑定到`fn`,这样绑定的函数将会期待剩余的参数。

```
const enablePartialApplication = (fn) => {
    return (...args) => {
        if (args.length >= fn.length) return fn(...args);
        return fn.bind(null, ...args); // use Function.prototype.bind to fix arguments
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个实现将允许我们做以下事情:

```
const sum = enablePartialApplication((x, y) => x + y);
const sum3 = sum(3);
sum3(10); // 13
sum(3, 5); // 8
sum(3)(4); // 7

const fn = enablePartialApplication((x, y, z) => (x * y) / z);
fn(3, 2, 1); // (3 * 2) / 1 -> 6
fn(3, 2)(1); // 6
fn(3)(2, 1); // 6 
```

Enter fullscreen mode Exit fullscreen mode

厉害！但是，返回的绑定函数不支持部分应用。所以下面的不会起作用

```
const fn = enablePartialApplication((x, y, z) => (x * y) / z);
fn(3)(2)(1); // Trying to partially apply the function: fn(3)
// -> ((y, z) => (3 * y) / z)(2)(1)
// -> ((3 * 2) / undefined)(1)
// -> (NaN)(1)
// -> ERROR: NaN is not a function 
```

Enter fullscreen mode Exit fullscreen mode

### 递归

现在我们要做的，就是让有界函数部分适用。轻松点。在上面递归调用`enablePartialApplication`！

```
const enablePartialApplication = (fn) => {
    return (...args) => {
        if (args.length >= fn.length) return fn(...args);
        return enablePartialApplication(fn.bind(null, ...args)); // make the bound function partially applicable
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

有了这个实现，我们可以想链接多少就链接多少！

```
const g = enablePartialApplication((a, b, c, d, e, f) => a + b - c * d / e ** f);
g(1, 2, 3, 4, 5, 6); // 2.999232
g(1, 2)(3, 4, 5)(6); // 2.999232
g(1)(2)(3)(4)(5)(6); // 2.999232
// ... 
```

Enter fullscreen mode Exit fullscreen mode

*牛逼！*

### 清理干净

注意我们的函数`enablePartialApplication`立即返回另一个函数。因此，我们可以简化为:

```
const enablePartialApplication = (fn) => (...args) => {
    if (args.length >= fn.length) return fn(...args);
    return enablePartialApplication(fn.bind(null, ...args));
}; 
```

Enter fullscreen mode Exit fullscreen mode

像这样？分享评论！:D