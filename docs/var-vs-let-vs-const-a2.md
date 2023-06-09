# Var vs Let vs Const

> 原文：<https://dev.to/nicolasmesa/var-vs-let-vs-const-a2>

交叉发布自我的个人博客[https://blog.nicolasmesa.co](https://blog.nicolasmesa.co)。

你好。

在本帖中，我们将谈论`javascript`。我们将从看一些`var`、`let`和`const`变量声明及其属性的例子开始。然后，我们将仔细阅读我对何时使用每种方法的建议。

## 定义变量

`var`是 javascript 中最初做变量声明的方式。下面是一个如何使用`var`声明变量的例子:

```
var myVariable = 10; 
```

### var 是函数范围的

使用`var`定义的变量是函数范围的。这意味着变量在其封闭函数中的任何地方都是可用的。例如:

```
function functionScopedExample() {
    var a = 10;

    if (a < 20) {
        var b = 15;
    } else {
        var c = 30;
    }

    console.log(a, b, c); // logs: 10 15 undefined
} 
```

这个例子中发生了一些事情。首先，`a`在这个函数中随处可见(和大多数编程语言一样)。第二，`b`也随处可用，尽管我们在`if`块中声明并赋值了它。第三，`c`在函数中也随处可见(尽管我们的执行路径没有进入`else`语句)！这里需要注意的一点是`c`的值是`undefined`(因为`else`语句中的代码没有被执行)，但是`c`还是被声明了。为了理解为什么会这样，我们需要学习变量提升。

下面是另一个*函数作用域的*变量的例子:

```
function functionScopedExample2() {
    var a = 10;

    function innerFunction() {
        var b = 20;
        console.log(a); // logs: 10
    }

    innerFunction();

    console.log(a); // logs: 10
    console.log(b); // error: Uncaught ReferenceError: b is not defined
} 
```

这个例子表明`a`在其封闭函数(`functionScopedExample2`)中是可用的。这包括在其中定义的其他函数(例如`innerFunction`函数)。但是`b`只能从它的封闭函数(`innerFunction`)中引用，不能从外部引用。

*函数范围的*声明会产生奇怪的效果:

```
function functionScopedExample3() {
    var myNumberArray = [];

    for (var i = 0; i < 10; i++) {
        setTimeout(function() {
            myNumberArray.push(i);
        }, 100);

    }

    // wait until all timeouts have executed.
    setTimeout(function() {
        console.log(myNumberArray); // logs: [10, 10, 10, 10, 10, 10, 10, 10, 10, 10]
    }, 500);
} 
```

上面的例子看起来很奇怪。大多数程序员希望`myNumberArray`有 0 - 9 的数字。相反，该数组包含数字`10` 10 次！这是因为变量`i`在整个函数(`functionScopedExample3`)的范围内。当`setTimeout`中的函数执行时，`i`已经有了值`10`。解决这个问题的一个方法是创建另一个函数，它接收`i`作为参数并执行`setTimeout`:

```
function fixedFunctionScopedExample3() {
    var myNumberArray = [];

    // a new scope is created every time this function is called.
    // As a result, newI is isolated.
    function executeTimeout(newI) {
        setTimeout(function() {
            myNumberArray.push(newI);
        }, 100);
    }

    for (var i = 0; i < 10; i++) {
        executeTimeout(i);
    }

    // wait until all timeouts have executed.
    setTimeout(function() {
        console.log(myNumberArray); // logs: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    }, 500);
} 
```

后面我们会看到如何用`let`代替`var`来解决这个问题。

### 可变提升

当我们使用`var`定义一个变量时，变量声明(不是赋值)被*提升*到函数的开头。在*吊装*过程之后，`functionScopedExample`(如上定义)最终会是这样的:

```
function varHoistingExample() {
    // variables are hoisted to the top and all of them start
    // with the value of undefined.
    var a;
    var b;
    var c;

    // a gets its value here.
    a = 10;

    if (a < 20) {
        // b gets its value here.
        b = 15;
    } else {
        // c never gets its value so it remains undefined.
        c = 30;
    }

    console.log(a, b, c); // logs: 10 15 undefined
} 
```

*吊装*会造成很多混乱。看看下面的例子:

```
function weirdHoisting() {
    color = 'yellow';
    var color;

    console.log('My favorite color is', color); // logs: My favorite color is yellow
} 
```

等等，什么？这是怎么回事？记住，首先用`var`定义的变量被*提升*，然后执行功能代码。在本例中，变量*提升*后的代码如下所示:

```
function weirdHoisting() {
    // variable declaration is hoisted to the top of its enclosing function.
    var color;
    color = 'yellow';

    console.log('My favorite color is', color); // logs: My favorite color is yellow
} 
```

变量`color`的声明是*提升*，从而防止任何引用错误。

命名函数声明也被*提升*(如果你在做条件，会有一些[边缘情况)。这里有一个例子:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function#Conditionally_created_functions)

```
function functionHoisting() {
    hoisted(); // logs: I am hoisted
    notHoisted(); // error: Uncaught TypeError: notHoisted is not a function

    var notHoisted = function() {
        console.log('This will not work');
    }

    function hoisted() {
        console.log('I am hoisted');
    }
} 
```

在这个例子中，我们看到*提升的*函数在声明之前就可以被调用。然而,`notHoisted`变量在被调用时还没有被赋予`function`。注意，函数失败不是因为`notHoisted`的名字还没有被声明，而是因为它还没有指向一个函数。在提升过程之后，这个函数看起来会像这样:

```
function functionHoisting() {
    // variable and function declaration are hoisted.
    var notHoisted;

    function hoisted() {
        console.log('I am hoisted');
    }

    hoisted(); // logs: I am hoisted
    notHoisted(); // error: Uncaught TypeError: notHoisted is not a function

    // function would be assigned here but we never reach this.
    notHoisted = function() {
        console.log('This will not work');
    }
} 
```

## 让和 const

`let`是 javascript 中定义变量的一种较新的方式。下面是一个使用`let`的变量定义的例子:

```
let myVariable = 10; 
```

`const`是 javascript 中定义常量的一种方式。下面是一个使用`const`定义的常数的例子:

```
const myConstant = 200; 
```

### let 和 const 是块范围的

`let`和`const`都是块范围的。这意味着在声明后，变量/常量在**块的任何地方都是可用的。`let` / `const`定义是**不是** *吊起*。让我们看几个例子:**

```
function blockScopedExample() {
    let a = 10;

    if (a < 20) {
        let b = 15;
    } else {
        let c = 30;
    }

    console.log(a); // logs: 10
    console.log(b); // error: Uncaught ReferenceError: b is not defined

    // we would never reach this since the code fails in the line before
    // but this would be the output if it had been executed
    console.log(c); // error: Uncaught ReferenceError: c is not defined
} 
```

这个例子表明`b`和`c`是块范围的。这就是为什么他们在`if` / `else`街区之外不可用的原因。`a`是在函数开始时定义的，使得它在函数**块**内的任何地方都可用。在函数的开头用`let`声明类似于用`var`定义它。

还记得我们的数组以相同的值结束的例子吗？这个问题很容易解决，用`let`代替`var`来定义`i`:

```
function blockScopedExample2() {
    let myNumberArray = [];

    // let makes the variable i block-scoped
    for (let i = 0; i < 10; i++) {
        setTimeout(function() {
            myNumberArray.push(i);
        }, 100);

    }

    // wait until all timeouts have executed.
    setTimeout(function() {
        console.log(myNumberArray); // logs: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    }, 500);
} 
```

因为变量`i`是块范围的而不是函数范围的，所以它的值在整个块执行中保持不变。

这里有另一个例子说明用`let`定义的变量不是用*提升的*:

```
function noHoisting() {
    color = 'yellow'; // error: Uncaught ReferenceError: color is not defined
    let color;

    // this is never reached.
    console.log('My favorite color is', color);
} 
```

这个函数的输出是大多数来自其他语言的程序员所期望的(在使用它之前必须声明一个变量)。

到目前为止，如果我们使用`const`来代替的话，我们使用`let`的每个例子(除了`blockScopedExample2`)都会有相同的结果。那么，`const`给了我们什么？

### 常数

`const`是如何在 javascript 中声明常量。使用`const`定义常量后，不能再为常量赋值。这里有一个例子:

```
function cantReassignConstants() {
    const myConstant = 10;
    myConstant = 9; // error: Uncaught TypeError: Assignment to constant variable
} 
```

用`const`定义的常量也必须在声明时赋值(不像`let`和`var`)。例如，下面这段代码在解析过程中会失败(不是在运行时)。

```
function constantsCantBeDeclaredAndNotDefined() {
    const myConstant;
    myConstant = 5;
} 
```

如果您尝试定义此函数，您会看到如下错误:

```
Uncaught SyntaxError: Missing initializer in const declaration 
```

由于这个错误发生在解析过程中，函数声明失败，我们甚至无法调用它。

```
constantsCantBeDeclaredAndNotDefined(); // error: Uncaught ReferenceError: constantsCantBeDeclaredAndNotDefined is not defined 
```

关于用`const`定义的常量，需要注意的一点是只有赋值是常量。赋给常量的对象不会变成不可变的。例如，以下代码运行时没有错误:

```
function constDoesntMakeObjectsImmutable() {
    const myObjectConstant = {
        myKey: 'myValue'
    };

    myObjectConstant.myKey = 'something else';

    console.log('myObjectConstant.myKey =', myObjectConstant.myKey); // logs: myObjectConstant.myKey = something else
} 
```

#### Object.freeze()

如果你想让对象不可变，你可以在给对象赋值前使用`Object.freeze()`:

```
function freezingObject() {
    const myFrozenObject = Object.freeze({
        myKey: 'This value cannot be changed'
    });

    myFrozenObject.myKey = 'something else';

    console.log('myFrozenObject.myKey = ', myFrozenObject.myKey); // logs: myFrozenObject.myKey = This value cannot be changed
} 
```

即使`myKey`的值没有改变，赋值也没有失败(它无声地失败了)。如果你想让它失败，在函数的开头使用`'use strict;'`,就像这样:

```
function freezingObject() {
    'use strict';
    const myFrozenObject = Object.freeze({
        myKey: 'This value cannot be changed'
    });

    myFrozenObject.myKey = 'something else'; // error: Uncaught TypeError: Cannot assign to read only property 'myKey' of object '#<Object>'

    // never executed
    console.log('myFrozenObject.myKey = ', myFrozenObject.myKey);
} 
```

`Object.freeze`只会冻结我们作为参数传递的`object`。如果`object`包含了对其他对象的引用，那些仍然可以被修改，除非我们也冻结它们。例如:

```
function objectFreezeIsShallow() {
    const myFrozenObject = Object.freeze({
        key1: {
            reassignable: 'reassign me'
        },
        key2: Object.freeze({
            notReassignable: 'This value cannot change'
        })
    });

    myFrozenObject.key1.reassignable = 'reassigned';
    myFrozenObject.key2.notReassignable = 'this will not work';

    console.log('myFrozenObject.key1.reassignable =', myFrozenObject.key1.reassignable); // logs: myFrozenObject.key1.reassignable = reassigned
    console.log('myFrozenObject.key2.notReassignable =', myFrozenObject.key2.notReassignable); // logs: myFrozenObject.key2.notReassignable = This value cannot change
} 
```

在这种情况下，`key1`和`key2`都不能被重新分配，因为它们被冻结了。然而，`key1`的内部对象没有被冻结，并且`reassignable`键被设置为不同的值。注意，自从对象被冻结后，`notReassignable`键的值没有被重新分配。

## 推荐

那么，我们应该使用哪一个(些)呢？为什么？

有很多理由使用一个而不是另一个，所以我把它分成每个理由一节。请注意，这些是我的偏好，其他程序员有不同的想法完全没问题:)

#### 永远不要在同一个项目中使用这三种方法

决定哪些是你想用的，并坚持使用。使用这三种方法会令人困惑。很难知道选择哪一个，代码也更难理解。善待你代码的未来读者(包括你自己)。

#### 偏好一致性

如果你正在做一个使用`var`的老项目，继续使用`var`。这将保持项目的一致性。

#### 如果使用 var，将变量声明放在顶部

如果你正在使用`var`，把所有的变量声明放在函数的顶部。这将有助于你看到哪些变量在函数中可用，不会有任何*提升*的惊喜。

#### 考虑旧的浏览器支持

如果你的代码需要支持没有`let`或`const`的旧浏览器，考虑使用`var`来代替。或者，使用 transpiler 将所有的`let`和`const`声明转换成`var`。

#### 更喜欢 let 而不是 var

如果你开始一个新项目，我会鼓励你使用`let`而不是`var`。`let`更容易预测，尤其是对于来自不同语言的开发人员。

#### 尽可能选择 const 而不是 let

我坚信一切都应该是常数，除了循环或类似的非常特殊的情况(在这些情况下，回退到`let`)。我可能会再写一篇文章，解释为什么使用`const`(Java 中的`final`)是个好选择，但是要点是:

##### 排除故障

这将简化调试。例如，考虑下面的代码:

```
function useConstWhenPossible(myOtherObject) {
    const myObject = myOtherObject.getMyObject();

    // ...
    // 100 lines of code
    // ...

    myObject.doSomething(); // error: Uncaught TypeError: Cannot read property 'doSomething' of undefined

    // ...
    // 100 lines of code
    // ...

    return ...;
} 
```

当您看到错误时，您会转到常量声明。你可以立刻判断出`myOtherObject.getMyObject()`返回了`undefined`。没有必要查看其他 100 行代码来确定`myObject`是否被重新分配。

##### 变量命名

你必须为你的常量取一个好名字，因为你将无法重用它们。这将使您的代码更容易阅读，并且更加自文档化。

##### 有助于防止错误

另一个开发人员(甚至可能是你)将没有机会定义一个现有的变量并破坏整个函数。

#### 使用命名函数

这将为您省去许多麻烦，尤其是在移动代码时。你不需要确保你的函数调用是在函数声明之后，一切都会一致地工作。