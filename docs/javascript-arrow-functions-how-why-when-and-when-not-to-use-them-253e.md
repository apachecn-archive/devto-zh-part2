# JavaScript 箭头函数:如何、为什么、何时(以及何时不)使用它们

> 原文：<https://dev.to/kball/javascript-arrow-functions-how-why-when-and-when-not-to-use-them-253e>

现代 JavaScript 中最受欢迎的特性之一是引入了箭头函数，有时称为“胖箭头”函数，利用了新的标记`=>`。

这些函数有两个主要好处——非常干净简洁的语法和更直观的作用域和`this`绑定。

这些好处有时会导致箭头函数比其他形式的函数声明更受青睐。

例如——流行的 [airbnb eslint 配置](https://github.com/airbnb/javascript#arrow-functions)会在你创建匿名函数时强制使用 JavaScript 箭头函数。

然而，像工程中的任何事情一样，箭头函数也有积极和消极的一面。它们的使用也有利弊。

了解这些权衡是用好箭头函数的关键。

在本文中，我们将首先回顾箭头函数是如何工作的，然后探究箭头函数在哪些方面改进了我们的代码，最后探究一些箭头函数不是好主意的例子。

## 那么 JavaScript 箭头函数到底是什么呢？

JavaScript 箭头函数大致相当于 python 中的 [lambda 函数或者 Ruby](https://www.programiz.com/python-programming/anonymous-function) 中的[块。](http://ruby-for-beginners.rubymonstas.org/blocks.html)

这些都是匿名函数，它们有自己特殊的语法，接受固定数量的参数，并在它们的*封闭范围*的*上下文*中操作——即定义它们的函数或其他代码。

让我们依次分解这些部分。

### 箭头函数语法

箭头函数有一个单一的总体结构，然后在特殊情况下有许多方法可以简化它们。

核心结构是这样的:

```
(argument1, argument2, ... argumentN) => {
  // function body
} 
```

Enter fullscreen mode Exit fullscreen mode

括号内的参数列表，后跟一个“粗箭头”(`=>`)，再后跟一个函数体。

这与传统函数非常相似，我们只是去掉了`function`关键字，并在参数后添加了一个粗箭头。

然而，有很多方法可以让箭头函数变得更加简洁。

首先，如果函数体是一个单独的表达式，你可以去掉括号，把它放在内联中。表达式的结果将由函数返回。例如:

```
const add = (a, b) => a + b; 
```

Enter fullscreen mode Exit fullscreen mode

第二，如果只有一个参数，你甚至可以省去参数周围的括号。例如:

```
const getFirst = array => array[0]; 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，这可以产生一些非常简洁的语法，我们将在后面强调更多的好处。

#### 高级语法

了解一些高级语法非常有用。

首先，如果您试图使用内联的单表达式语法，但是您返回的值是一个对象文字。您可能会认为这看起来像:

```
(name, description) => {name: name, description: description}; 
```

Enter fullscreen mode Exit fullscreen mode

问题是这种语法是不明确的——看起来好像你试图创建一个传统的函数体。

为了表示您需要一个恰好是对象的表达式，您可以用括号将对象括起来:

```
(name, description) => ({name: name, description: description}); 
```

Enter fullscreen mode Exit fullscreen mode

### 封闭范围上下文

与其他形式的函数不同，arrow 函数没有自己的执行上下文。

实际上，这意味着`this`和`arguments`都是从它们的父函数继承而来的*。*

例如，比较以下带有和不带有箭头函数的代码:

```
const test = {
  name: 'test object',
  createAnonFunction: function() {
    return function() {
      console.log(this.name);
      console.log(arguments);
    };
  },

  createArrowFunction: function() {
    return () => {
      console.log(this.name);
      console.log(arguments);
    };
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个简单的测试对象，它有两个方法——每个方法都是一个创建并返回匿名函数的函数。

区别在于，在第一种情况下，它使用传统的函数表达式，而在后一种情况下，它使用箭头函数。

但是，如果我们在控制台中使用相同的参数运行这些命令，我们会得到非常不同的结果。

```
> const anon = test.createAnonFunction('hello', 'world');
> const arrow = test.createArrowFunction('hello', 'world');

> anon();
undefined
{}

> arrow();
test object
{ '0': 'hello', '1': 'world' } 
```

Enter fullscreen mode Exit fullscreen mode

匿名函数有它自己的函数上下文，所以当你调用它时，没有对测试对象的`this.name`的引用，也没有对创建它时调用的参数的引用。

另一方面，arrow 函数与创建它的函数具有完全相同的函数上下文，这使它可以访问参数和测试对象。

## 箭头函数改进代码的地方

传统 lambda 函数的一个主要用例，现在是 JavaScript 中的 arrow 函数，是一次又一次应用于列表中的项目的函数。

例如，如果您有一个想要使用映射进行转换的值数组，那么箭头函数是理想的:

```
const words = ['hello', 'WORLD', 'Whatever'];
const downcasedWords = words.map(word => word.toLowerCase()); 
```

Enter fullscreen mode Exit fullscreen mode

一个非常常见的例子是提取一个对象的特定值:

```
const names = objects.map(object => object.name); 
```

Enter fullscreen mode Exit fullscreen mode

类似地，当使用`forEach`用现代迭代器风格的循环替换旧式的`for`循环时，箭头函数使`this`远离父对象的事实使它们变得非常直观。

```
this.examples.forEach(example => {
  this.runExample(example);
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 承诺和承诺链

箭头函数使代码更干净、更直观的另一个地方是管理异步代码。

[Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises) 使得管理异步代码变得更加容易(即使你对使用 async/await 感到兴奋，你也应该[理解 promises](https://medium.com/@bluepnume/learn-about-promises-before-you-start-using-async-await-eb148164a9c8) 这是 async/await 的基础！)

然而，使用 promises 仍然需要定义在异步代码或调用完成后运行的函数。

这是一个箭头函数的理想位置，特别是如果你的结果函数是有状态的，引用你的对象中的一些东西。示例:

```
this.doSomethingAsync().then((result) => {
  this.storeResult(result);
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 物体变换

箭头函数的另一个常见且极其强大的用途是封装对象转换。

例如，在 Vue.js 中，[使用`mapState`](https://vuex.vuejs.org/guide/state.html#the-mapstate-helper) 将 Vuex 存储的片段直接包含到 Vue 组件中。

这涉及到定义一组“映射器”,它们将从原始的完整状态对象中转换出来，以准确地提取出所讨论的组件所必需的内容。

这种简单的转换是利用箭头函数的理想和理想的地方。示例:

```
export default {
  computed: {
    ...mapState({
      results: state => state.results,
      users: state => state.users,
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在不应该使用箭头功能的地方

在很多情况下，箭头函数不是一个好主意。他们不仅会帮助你，还会给你带来麻烦。

第一种是在对象的方法中。这是一个函数上下文和`this`正是您想要的例子。

有一段时间，有一种趋势是使用类属性语法和箭头函数的组合来创建“自动绑定”方法，例如，可以由事件处理程序使用但仍然绑定到类的方法。

这看起来像是:

```
class Counter {
  counter = 0;

  handleClick = () => {
    this.counter++;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，即使 handleClick 是由事件处理程序调用的，而不是在`Counter`实例的上下文中调用的，它仍然可以访问实例的数据。

这种方法的缺点是多方面的，在[这篇文章](https://medium.com/@charpeni/arrow-functions-in-class-properties-might-not-be-as-great-as-we-think-3b3551c440b1)中有很好的记录。

虽然使用这种方法确实为您提供了一个符合人机工程学的快捷方式来拥有一个绑定的函数，但是该函数在许多方面的表现并不直观，如果您试图将该对象作为原型来子类化/使用，则会妨碍测试并产生问题。

相反，使用一个常规函数，如果必要的话，在构造函数中绑定它的实例:

```
class Counter {
  counter = 0;

  handleClick() {
    this.counter++;
  }

  constructor() {
    this.handleClick = this.handleClick.bind(this);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 深层调用链

箭头函数可能给你带来麻烦的另一个地方是当它们被用于许多不同的组合时，特别是在函数调用的深层链中。

核心原因与匿名函数相同——它们给了[非常糟糕的堆栈跟踪](https://hackernoon.com/three-reasons-i-avoid-anonymous-js-functions-like-the-plague-7f985c27a006)。

如果你的函数只向下一层，比如说在迭代器内部，这还不算太糟，但是如果你把所有的函数都定义为箭头函数，并在它们之间来回调用，那么当你遇到一个 bug 时，你就会陷入困境，只会得到如下的错误消息:

```
{anonymous}()
{anonymous}()
{anonymous}()
{anonymous}()
{anonymous}() 
```

Enter fullscreen mode Exit fullscreen mode

### 函数具有动态上下文

箭头函数可能给你带来麻烦的最后一种情况是在动态绑定`this`的地方。

如果在这些位置使用箭头函数，动态绑定将不起作用，并且您(或稍后使用您的代码的其他人)可能会对为什么事情没有像预期的那样工作感到非常困惑。

这方面的一些重要例子:

*   调用事件处理程序时，将`this`设置为事件的`currentTarget`属性。
*   如果您仍在使用 jQuery，大多数 jQuery 方法会将`this`设置为已选择的 dom 元素。
*   如果使用 Vue.js，方法和计算函数通常会将`this`设置为 Vue 组件。

当然，您可以故意使用箭头函数来覆盖这种行为，但特别是在 jQuery 和 Vue 的情况下，这通常会干扰正常的功能，并让您困惑为什么看起来与附近的其他代码一样的代码却不能工作。

## 包装完毕

总之:箭头函数是 JavaScript 语言的一个显著的补充，在许多情况下可以实现更加符合人体工程学的代码。

然而，像其他功能一样，它们也有优点和缺点。我们应该把它们作为我们工具箱中的另一个工具，而不是所有功能的一揽子替代品。

* * *

附:如果你对这类话题感兴趣，你或许应该[在 Twitter 上关注我](https://twitter.com/kbal11)或者加入我的邮件列表。我发出一份名为“[周五前端](https://zendev.com/friday-frontend.html)”的每周简讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T4】