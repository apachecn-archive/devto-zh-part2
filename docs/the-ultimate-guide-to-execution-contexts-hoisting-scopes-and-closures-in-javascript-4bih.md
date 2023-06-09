# JavaScript 中执行上下文、提升、作用域和闭包的终极指南

> 原文：<https://dev.to/tylermcginnis/the-ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript-4bih>

* * *

***这是我们[高级 JavaScript](https://tylermcginnis.com/courses/advanced-javascript/) 课程的一部分。如果你喜欢这篇文章，就去看看吧。***

* * *

[https://www.youtube.com/embed/Nt-qa_LlUH0](https://www.youtube.com/embed/Nt-qa_LlUH0)

这似乎令人惊讶，但在我看来，理解 JavaScript 语言最重要和最基本的概念是理解执行上下文。通过正确地学习它，您将能够很好地学习更高级的主题，如提升、作用域链和闭包。考虑到这一点，什么是“执行上下文”呢？为了更好地理解它，我们先来看看我们是如何编写软件的。

编写软件的一个策略是将我们的代码分成独立的部分。尽管这些“片段”有许多不同的名称(函数、模块、包等)，但它们都是为了一个目的而存在的——分解和管理我们应用程序中的复杂性。现在，不要像编写代码的人那样思考，而是从 JavaScript 引擎的角度思考，它的工作是由 T2 解释代码。我们能不能使用同样的策略，将代码分成片段，来管理解释代码的复杂性，就像我们为了编写代码所做的那样？事实证明我们可以，这些“片段”被称为执行上下文。**就像函数/模块/包允许你管理编写代码的复杂性一样，执行上下文允许 JavaScript 引擎管理解释和运行代码的复杂性。**现在我们知道了执行上下文的目的，接下来我们需要回答的问题是它们是如何创建的，它们由什么组成？

JavaScript 引擎运行代码时创建的第一个执行上下文称为“全局执行上下文”。最初，这个执行上下文将由两件事组成——一个全局对象和一个名为`this`的变量。如果您在浏览器中运行 JavaScript，那么`this`将引用全局对象`window`,如果您在节点环境中运行 JavaScript，那么`global`将引用全局对象。

[![Shows how even with no code, the global execution context still creates a window object and a this object for you](img/b6c113a8d531a6baa8832ec1707819ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zlGOjGfK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/no-code.png)

上面我们可以看到，即使没有任何代码，全局执行上下文仍然会由两个东西组成——`window`和`this`。这是最基本形式的全局执行上下文。

让我们一步步来，看看当我们真正开始向程序中添加代码时会发生什么。让我们从添加几个变量开始。

[![Globals variables in the creation phase](img/7f9b9dbe876ae137993b6efe897628be.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c-9FRS17--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/global-variables-in-creation-phase.png)

[![Global variables in the execution phase](img/af1026104305e1a145f17ff5fa4151d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RpOqaW_K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/global-variables-in-execution-phase.png)

你能看出上面两张图片的不同之处吗？关键是每个执行上下文都有两个独立的阶段，一个`Creation`阶段和一个`Execution`阶段，每个阶段都有自己独特的职责。

在全局`Creation`阶段，JavaScript 引擎将

1)创建一个全局对象。
2)创建一个名为“this”的对象。
3)为变量和函数设置内存空间。
4)在内存中放置任何函数声明时，给变量声明分配一个默认值“未定义”。

直到`Execution`阶段，JavaScript 引擎才开始一行一行地运行代码并执行它。

我们可以在下面的 GIF 中看到这个从`Creation`阶段到`Execution`阶段的流程。

[![Animated GIF showing flow from creation phase to execution phase](img/9ea82229df89ba0f202e2449a17bd3ba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7md78Hvc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/global-execution-context-gif.gif)

在`Creation`阶段`window`和`this`被创建，变量声明(`name`和`handle`)被赋予默认值`undefined`，任何函数声明(`getUser`)被完全放入内存。然后，一旦我们进入`Execution`阶段，JavaScript 引擎就开始一行一行地执行代码，并将实际值赋给已经存在于内存中的变量。

> gif 很酷，但没有单步执行代码并亲眼看到过程那么酷。因为这是你应得的，我专门为你创建了 [JavaScript Visualizer](https://tylermcginnis.com/javascript-visualizer) 。如果你想浏览上面的代码，使用[这个链接](https://tylermcginnis.com/javascript-visualizer/?code=var%20name%20%3D%20%27Tyler%27%0Avar%20handle%20%3D%20%27%40tylermcginnis%27%0A%0Afunction%20getUser%20%28%29%20%7B%0A%20%20return%20%7B%0A%20%20%20%20name%3A%20name%2C%0A%20%20%20%20handle%3A%20handle%0A%20%20%7D%0A%7D)。

为了真正巩固`Creation`阶段与`Execution`阶段的概念，让我们在`Creation`阶段之后和`Execution`阶段之前记录一些值*。* 

```
console.log('name: ', name)
console.log('handle: ', handle)
console.log('getUser :', getUser)

var name = 'Tyler'
var handle = '@tylermcginnis'

function getUser () {
  return {
    name: name,
    handle: handle
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，您希望控制台记录什么？当 JavaScript 引擎开始逐行执行我们的代码并调用我们的 console.logs 时，`Creation`阶段已经发生了。这意味着，正如我们前面看到的，变量声明应该已经被赋值为`undefined`，而函数声明应该已经完全在内存中。正如我们所料，`name`和`handle`是`undefined`，而`getUser`是对内存中函数的引用。

```
console.log('name: ', name) // name: undefined
console.log('handle: ', handle) // handle: undefined
console.log('getUser :', getUser) // getUser: ƒ getUser () {}

var name = 'Tyler'
var handle = '@tylermcginnis'

function getUser () {
  return {
    name: name,
    handle: handle
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 这个在创建阶段给变量声明赋予默认值`undefined`的过程被称为**提升**。

希望你只是“啊哈！“时刻。你可能已经得到了“吊装”的解释，但并不成功。“提升”令人困惑的一点是，实际上没有东西被“提升”或移动。现在您已经理解了执行上下文，并且在`Creation`阶段，变量声明被赋予了一个默认值`undefined`，您理解了“提升”,因为这实际上就是提升。

* * *

此时，您应该对全局执行上下文及其两个阶段`Creation`和`Execution`相当熟悉了。好消息是，您只需要学习另一个执行上下文，它几乎与全局执行上下文完全相同。它被称为函数执行上下文，每当函数被**调用**时就会被创建。

这是关键。只有在 JavaScript 引擎第一次开始解释您的代码(全局执行上下文)以及调用函数时，才会创建执行上下文。

现在我们需要回答的主要问题是，全局执行上下文和函数执行上下文有什么区别。如果您还记得，我们在前面说过，在全局`Creation`阶段，JavaScript 引擎将

1)创建一个全局对象。
2)创建一个名为“this”的对象。
3)为变量和函数设置内存空间。
4)在内存中放置任何函数声明时，给变量声明分配一个默认值“未定义”。

当我们谈论函数执行上下文时，哪些步骤**和**没有意义？这是第一步。我们应该只在全局执行上下文的`Creation`阶段创建一个全局对象，而不是每次调用一个函数，JavaScript 引擎创建一个函数执行上下文。除了创建一个全局对象，函数执行上下文需要担心的一件事是，全局执行上下文没有参数。考虑到这一点，我们可以修改之前的列表。每当**函数**执行上下文被创建时，JavaScript 引擎将

1.~~创建一个全局对象。~~

1。创建 arguments 对象。
2
。创建名为 this 的对象。

3。为变量和函数设置内存空间。

4。将任何函数声明放在内存中时，给变量声明赋一个缺省值“undefined”。

为了看到这一点，让我们回到前面的代码，但是这次不是仅仅定义`getUser`，而是让我们看看当我们调用它时会发生什么。

> [自己将代码可视化](https://tylermcginnis.com/javascript-visualizer/?code=var%20name%20%3D%20%27Tyler%27%0Avar%20handle%20%3D%20%27%40tylermcginnis%27%0A%0Afunction%20getUser%20%28%29%20%7B%0A%20%20return%20%7B%0A%20%20%20%20name%3A%20name%2C%0A%20%20%20%20handle%3A%20handle%0A%20%20%7D%0A%7D%0A%0AgetUser%28%29)

[![GIF showing how a new execution context is created when a function is invoked](img/097a113497211efb971c2f2519835963.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LULiqLC6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/function-execution-context-gif.gif)

正如我们谈到的，当我们调用`getUser`时，一个新的执行上下文被创建。在`getUsers`执行上下文的`Creation`阶段，JavaScript 引擎创建一个`this`对象和一个`arguments`对象。因为`getUser`没有任何变量，JavaScript 引擎不需要设置任何内存空间或者“提升”任何变量声明。

您可能还注意到，当`getUser`函数执行完毕时，它会从可视化中移除。实际上，JavaScript 引擎创建了所谓的“执行堆栈”(也称为“调用堆栈”)。每当调用一个函数时，都会创建一个新的执行上下文并将其添加到执行堆栈中。每当一个函数通过`Creation`和`Execution`阶段完成运行时，它就会从执行堆栈中弹出。因为 JavaScript 是单线程的(意味着一次只能执行一个任务)，所以这很容易可视化。使用“JavaScript Visualizer ”,执行堆栈以嵌套方式显示，每个嵌套项目都是执行堆栈上的一个新的执行上下文。

> [自己将代码可视化](https://tylermcginnis.com/javascript-visualizer/?code=function%20a%20%28%29%20%7B%0A%20%20console.log%28%27In%20fn%20a%27%29%0A%20%20%0A%20%20function%20b%20%28%29%20%7B%0A%20%20%20%20console.log%28%27In%20fn%20b%27%29%0A%20%20%20%20%0A%20%20%20%20function%20c%20%28%29%20%7B%0A%20%20%20%20%20%20console.log%28%27In%20fn%20c%27%29%0A%20%20%20%20%7D%0A%20%20%20%20%0A%20%20%20%20c%28%29%0A%20%20%7D%0A%0A%20%20b%28%29%0A%7D%0A%0Aa%28%29)

[![Shows how JavaScript is single threaded and each time a function is invoked, a new execution context is added to the execution stack](img/070a52a1a2172e9cdfc76486ecd579cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I8h-mVqz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/javascript-execution-stack.gif)

* * *

至此，我们已经看到了函数调用如何创建自己的执行上下文，这些上下文被放置在执行堆栈上。我们还不知道局部变量是如何起作用的。让我们修改一下代码，让我们的函数有局部变量。

> [自己将代码可视化](https://tylermcginnis.com/javascript-visualizer/?code=var%20name%20%3D%20%27Tyler%27%0Avar%20handle%20%3D%20%27%40tylermcginnis%27%0A%0Afunction%20getURL%20%28handle%29%20%7B%0A%20%20var%20twitterURL%20%3D%20%27https%3A%2F%2Ftwitter.com%2F%27%0A%0A%20%20return%20twitterURL%20%2B%20handle%0A%7D%0A%0AgetURL%28handle%29)

[![Execution Context with local variables and an argument](img/e2e475207a9de8a80b0872d16f9dd362.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nNw8pRhb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/local-variables.gif)

这里有几个重要的细节需要注意。首先，您传入的任何参数都将作为局部变量添加到该函数的执行上下文中。在这个例子中，`handle`既作为变量存在于`Global`执行上下文中(因为它是在那里被定义的),也存在于`getURL`执行上下文中，因为我们把它作为参数传入。其次，在函数内部声明的变量存在于函数的执行上下文中。所以当我们创建`twitterURL`时，它存在于`getURL`执行上下文中，因为它是在那里定义的，**而不是**执行上下文。这似乎是显而易见的，但它是我们下一个主题作用域的基础。

* * *

过去，你可能听说过“作用域”的定义，类似于“变量可访问的地方”。不管这在当时是否有意义，有了您对执行上下文和 JavaScript Visualizer 工具的新知识，作用域将比以前更加清晰。事实上，MDN 将“范围”定义为“执行的当前上下文”。听起来熟悉吗？我们可以把“范围”或“变量可访问的位置”理解为与我们理解执行上下文的方式非常相似。

给你做个测试。当`bar`被记录在下面的代码中时会是什么？

```
function foo () {
  var bar = 'Declared in foo'
}

foo()

console.log(bar) 
```

Enter fullscreen mode Exit fullscreen mode

让我们在 JavaScript Visualizer 中检查一下。

> [自己将代码可视化](https://tylermcginnis.com/javascript-visualizer/?code=function%20foo%20%28%29%20%7B%0A%20%20var%20bar%20%3D%20%27Declared%20in%20foo%27%0A%7D%0A%0Afoo%28%29%0A%0Aconsole.log%28bar%29)

[![Visualizes how variables can't be accessed once the Execution Context is removed from the stack](img/3323551d4b26338e0a641d0d12dcdbdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5eewZCIV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/scope.gif)

当`foo`被调用时，我们在执行堆栈上创建一个新的执行上下文。`Creation`阶段创建`this`、`arguments`，并将`bar`设置为`undefined`。然后进入`Execution`阶段，将字符串`Declared in foo`分配给`bar`。之后，`Execution`阶段结束，并且`foo`执行上下文被弹出堆栈。一旦`foo`从执行堆栈中移除，我们就尝试将`bar`记录到控制台。根据 JavaScript Visualizer，那一刻就好像`bar`从未存在过，所以我们得到了`undefined`。这向我们展示了在函数内部创建的变量是局部作用域的。这意味着(在很大程度上，我们稍后会看到一个异常)一旦函数的执行上下文被弹出执行堆栈，它们就不能被访问。

这是另一个。代码执行完毕后，控制台上会记录什么？

```
function first () {
  var name = 'Jordyn'

  console.log(name)
}

function second () {
  var name = 'Jake'

  console.log(name)
}

console.log(name)
var name = 'Tyler'
first()
second()
console.log(name) 
```

Enter fullscreen mode Exit fullscreen mode

同样，让我们来看看 JavaScript 可视化工具。

> [自己将代码可视化](https://tylermcginnis.com/javascript-visualizer?code=function%20first%20%28%29%20%7B%0A%20%20var%20name%20%3D%20%27Jordyn%27%0A%0A%20%20console.log%28name%29%0A%7D%0A%0Afunction%20second%20%28%29%20%7B%0A%20%20var%20name%20%3D%20%27Jake%27%0A%0A%20%20console.log%28name%29%0A%7D%0A%0Aconsole.log%28name%29%0Avar%20name%20%3D%20%27Tyler%27%0Afirst%28%29%0Asecond%28%29%0Aconsole.log%28name%29)

[![Visualized how each scope has its own unique variable environment](img/34d00445382324966c9462e35fd8489c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RMZ8IP7r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/unique-scopes.gif)

我们得到`undefined`、`Jordyn`、`Jake`，然后是`Tyler`。这向我们展示的是，您可以将每个新的执行上下文视为具有自己独特的变量环境。即使有其他包含变量`name`的执行上下文，JavaScript 引擎也会首先在当前的执行上下文中查找该变量。

这就带来了一个问题，如果变量在当前的执行上下文中不存在怎么办？JavaScript 引擎会停止寻找那个变量吗？让我们看一个例子来回答这个问题。在下面的代码中，将记录什么？

```
var name = 'Tyler'

function logName () {
  console.log(name)
}

logName() 
```

Enter fullscreen mode Exit fullscreen mode

> [自己将代码可视化](https://tylermcginnis.com/javascript-visualizer/?code=var%20name%20%3D%20%27Tyler%27%0A%0Afunction%20logName%20%28%29%20%7B%0A%20%20console.log%28name%29%0A%7D%0A%0AlogName%28%29)

[![Visualizes how if an execution context can't find a variable, it'll look in all its parent execution contexts](img/d2aa1495b8ece82162593059a76a8247.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9WS5dZ24--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/parent-lookup.gif)

您的直觉可能是它将记录`undefined`，因为`logName`执行上下文在其范围内没有`name`变量。那是公平的，但是它是错误的。如果 JavaScript 引擎找不到函数执行上下文中的本地变量，它会在最近的父执行上下文中查找该变量。这个查找链将一直继续下去，直到引擎到达全局执行上下文。在这种情况下，如果全局执行上下文没有该变量，它将抛出一个引用错误。

> JavaScript 引擎逐个检查每个单独的父执行上下文(如果变量不存在于本地执行上下文中)的过程称为`Scope Chain`。JavaScript Visualizer 通过缩进每个新的执行上下文并使用独特的彩色背景来显示作用域链。从视觉上可以看到，任何子执行上下文都可以引用位于其任何父执行上下文中的任何变量，但反之则不然。

* * *

前面我们了解到，在函数内部创建的变量是局部作用域的，一旦函数的执行上下文被弹出执行堆栈，它们就不能被访问了。是时候深入那个“**大部**”了。一种不正确的情况是，如果一个函数嵌套在另一个函数中。在这种情况下，即使父函数的执行上下文已经从执行堆栈中移除，子函数仍然可以访问外部函数的范围。说了很多话。和往常一样，JavaScript Visualizer 可以帮助我们解决这个问题。

> [自己将代码可视化](https://tylermcginnis.com/javascript-visualizer/?code=var%20count%20%3D%200%0A%0Afunction%20makeAdder%28x%29%20%7B%0A%20%20return%20function%20inner%20%28y%29%20%7B%0A%20%20%20%20return%20x%20%2B%20y%3B%0A%20%20%7D%3B%0A%7D%0A%0Avar%20add5%20%3D%20makeAdder%285%29%3B%0Acount%20%2B%3D%20add5%282%29)

[![Visualizes closures](img/419744ca85f3e250f4fad9dbfbcd7205.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dUcCeGvH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tylermcginnis.cimg/posts/advanced-javascript/closure-scope.gif)

注意，在`makeAdder`执行上下文从执行堆栈中弹出后，JavaScript Visualizer 会创建一个所谓的`Closure Scope`。在那个`Closure Scope`内部是存在于`makeAdder`执行上下文中的同一个变量环境。发生这种情况的原因是因为我们有一个函数嵌套在另一个函数中。在我们的例子中，`inner`函数嵌套在`makeAdder`函数中，所以`inner`在`makeAdder`变量环境上创建了一个`Closure`。即使在`makeAdder`执行环境被弹出执行堆栈之后，因为`Closure Scope`被创建，`inner`仍然可以访问`x`变量(通过作用域链)。

正如您可能猜到的那样，子函数“关闭”其父函数的可变环境的概念被称为`Closures`。

* * *

### 奖金部分

这里还有几个相关的话题，我知道如果我不提的话，会有人找我的麻烦🙈。

#### 全局变量

在浏览器中，每当您在全局执行上下文中(任何函数之外)创建一个变量时，该变量将作为属性添加到`window`对象中。

在浏览器和节点中，如果你创建一个没有声明的变量(即没有`var`、`let`或`const`)，这个变量也将作为一个属性添加到全局对象中。

```
// In the browser
var name = 'Tyler'

function foo () {
  bar = 'Created in foo without declaration'
}

foo()

console.log(window.name) // Tyler
console.log(window.bar) // Created in foo without declaration 
```

Enter fullscreen mode Exit fullscreen mode

#### 设和常数

[https://www.youtube.com/embed/6vBYfLCE9-Q](https://www.youtube.com/embed/6vBYfLCE9-Q)

#### 这个关键字

在本文中，我们了解到在每个执行上下文的`Creation`阶段，JavaScript 引擎会创建一个名为`this`的对象。如果你想了解更多为什么这很重要以及如何确定什么是`this`关键字，我建议阅读 [**WTF is this -理解 this 关键字，用 JavaScript**](https://tylermcginnis.com/this-keyword-call-apply-bind-javascript/) 调用、应用和绑定

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们在 T2 大学高级 JavaScript 课程的一部分。