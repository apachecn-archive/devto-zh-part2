# 记住“函数是对象”有助于编写更简洁的代码

> 原文：<https://dev.to/somedood/remembering-that-functions-are-objects-can-help-in-writing-more-concise-code-1k22>

# 简介

更多的时候，我们沉迷于编写简洁的代码。谁不会呢，对吧？简洁的代码是短小的代码，更容易理解，通常可读性更好。这是快速而肮脏的代码与优雅的代码的区别。这里的关键词是*优雅*。以牺牲可读性为代价使用更短更模糊的变量名来达到“简洁代码”的确是**而不是**简洁代码。更确切地说，这是一种缩略的胡言乱语。

作为开发人员，我们尽可能努力编写这样的代码。这就是为什么 JavaScript 在过去的几年里有了巨大的改变。为了客观地看待 JavaScript 发生了多大的变化，在 ES6(或者 ES2015，如果你很紧张的话)出现之前，曾经有一段时间必须写单词`function`来定义一个函数，不管它是匿名的还是命名的。例如，下面的代码将一个点击监听器(匿名函数)附加到一个 ID 为`veryNiceExample`的 HTML 元素上。为了简单起见，监听器将`MouseEvent`对象记录到控制台。

```
// Using "var" for added immersion
var element = document.getElementById('veryNiceExample');

// Attaches listener
element.addEventListener('click', function(event) {
  console.log(event);
}); 
```

Enter fullscreen mode Exit fullscreen mode

随着 ES6 的引入，整个 JavaScript 社区为[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)而疯狂。我们现在可以用更短的语法做同样的事情。

```
// Using "const" for added immersion
const element = document.getElementById('veryNiceExample');

// Attaches listener
element.addEventListener('click', event => {
  console.log(event);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果它还不够短，聪明的人开始利用 arrow 函数的隐式返回特性来进一步拓展极限。然后可以在代码示例中应用隐式返回。虽然`console.log`不返回任何东西，但是在这种情况下仍然可以使用隐式返回的 arrow 函数，因为它只是一个单一用途的函数，它的返回值并不真正用于任何事情。

```
// Using "const" for added immersion
const element = document.getElementById('veryNiceExample');

// Attaches listener
element.addEventListener('click', event => console.log(event)); 
```

Enter fullscreen mode Exit fullscreen mode

# 函数也是对象

在 JavaScript 中，**一切都是对象**。除非对象是通过`Object.create(null)`创建的，否则一切都继承自`Object`，因为它是原型链中的最后一个环节[。函数也不例外。甚至原始数据类型也是对象。为了强调这一点，所有数据类型(除了`Symbol` s)都有](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)[对象包装器](https://javascriptrefined.io/the-wrapper-object-400311b29151)。我的意思是，通过用`new`关键字调用其构造函数，可以将原语实例化为对象。

**免责声明:**出于性能考虑，不建议使用对象包装器。这仅用于演示目的。

```
const primitiveString = 'This is a string.';
const wrapperObjectString = new String('This is a string.');

console.log(typeof primitiveString); // 'string'
console.log(typeof wrapperObjectString); // 'object' 
```

Enter fullscreen mode Exit fullscreen mode

由于 JavaScript 将函数视为对象，因此可以将函数作为值存储在变量中。

```
// Using "const" for added immersion
const element = document.getElementById('veryNiceExample');

// Stores function through declaration
function handler(event) {
  console.log(event);
}

// Attaches listener
element.addEventListener('click', handler); 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，`handler`与`handler()`不同。变量`handler`返回它存储的值。在这种情况下，它存储的值是函数的**实际定义**。另一方面，`handler()`执行存储在`handler`中的函数，并返回必要的值。在这种情况下，`handler`(定义)没有明确地`return`一个值。因此，如果执行`handler`，则`handler()`返回`undefined`。

也就是说，现在可以使用相同的概念来缩短代码示例。由于`console.log`本质上是一个接受参数的函数，它的定义可以直接用作鼠标点击事件的监听器。

```
// Using "const" for added immersion
const element = document.getElementById('veryNiceExample');

// Attaches listener
element.addEventListener('click', console.log); 
```

Enter fullscreen mode Exit fullscreen mode

**编辑:**正如 [@jburgy](https://dev.to/jburgy) 在[他的评论](https://dev.to/jburgy/comment/696g)中提出的，一个人必须要知道一个函数的所有参数。如果不小心，可能会出现一些参数冲突，例如下面的代码。参见[完整讨论](https://dev.to/jburgy/comment/696g)以了解为什么这不像预期的那样工作。

```
['0', '1', '2'].map(parseInt); // [0, NaN, NaN] 
```

Enter fullscreen mode Exit fullscreen mode

# 抓住承诺

在前面的例子中，将函数视为对象似乎毫无意义。然而，这个概念可以证明在承诺的上下文中是有用的，因为回调函数无处不在。

在任何 JavaScript 应用程序的原型开发阶段，编写快速而肮脏的代码是可以理解的。对于快速调试，被拒绝的承诺通常通过记录错误来处理。例如，下面的代码从 [GitHub REST API v3](https://developer.github.com/v3/) 的主端点获取数据，并将接收到的数据记录为 [JSON](https://en.wikipedia.org/wiki/JSON#Example) 。如果有任何错误，`catch`接受`console.log`作为其参数。这样，它也记录了`Error`对象。

```
fetch('https://api.github.com/')
  .then(res => res.json())
  .then(console.log)
  .catch(console.log); 
```

Enter fullscreen mode Exit fullscreen mode

尽管上面的代码在语法上是合法的，但仍然经常看到单行箭头函数(甚至是普通函数)包装其他函数。反过来，这些单行包装函数不必作为参数传入。例如，考虑下面几行代码。

```
fetch('https://api.github.com/')
  .then(res => {
    return res.json();
  })
  .then(function(data) {
    console.log(data);
  })
  .catch(err => console.log(err)); 
```

Enter fullscreen mode Exit fullscreen mode

这两个例子做同样的操作，产生同样的结果，但是前者更简单*简洁*和*优雅*。相比之下，后者非常繁琐，难以阅读。尽管这种可怕的代码不太可能存在(尤其是在专业环境中)，但这种夸张是为了证明这一点。

额外的好处是，~~程序占用的内存少得可以忽略不计，因为 JavaScript 解释器/引擎不再需要将不必要的函数存储到内存中。~~

# 结论

让代码更加简洁无妨。要编写这样的代码，必须始终记住，函数，即使是内置函数，也只是可以作为参数传递给其他函数的值。那*终究是*回调函数的基础。当然，更重要的是找到*优雅*和可读性之间的平衡。就像生活中的大多数事情一样，这真的取决于具体情况。

总之，更加批判性地思考函数可以节省几行代码...和代码审查者的理智。