# 剖析一个 JavaScript 错误

> 原文：<https://dev.to/bugsnag/anatomy-of-a-javascript-error-51kc>

> 这篇博客是关于 JavaScript 调试的两部分系列文章的第二部分。阅读第一部分，通过代码示例了解 [JavaScript 源代码图](https://dev.to/bugsnag/anatomy-of-source-maps-22dk)的内部工作原理。

当一个应用程序死亡时，这并不是一个美好的景象。错误消息可能很难理解，有时我们不得不戴上调查员的帽子来解开谜团并找到罪魁祸首。

大多数软件开发人员花费所有的时间来避免错误，但是在 Bugsnag，我们的整个产品都是围绕捕获和管理错误而构建的。因此，我们处理了很多 JavaScript 错误及其相关 API 的来龙去脉。在这篇博客中，我们将看看不同类型的 JavaScript 错误，它们为什么会发生，以及如何在您的应用程序中处理它们。

## 自动生成的错误及其发生的原因

理解 JavaScript 错误的第一步是理解它们来自哪里。大多数发生在野外的 JavaScript 错误都是由 JavaScript 引擎自动生成的。有许多类型的错误，但它们通常属于 3 类之一。

### `TypeError`

这是最常见的一类错误，当某些值不是预期的类型时，就会发生这种情况。这种情况经常发生在调用一个函数之类的东西时，而这个函数实际上并不是一个函数，因为它是“未定义的”或者其他值。

```
window.foo()
  // => TypeError: window.foo is not a function
[].length
  // => 0
[].length()
  // => TypeError: array.length is not a function 
```

Enter fullscreen mode Exit fullscreen mode

TypeError 的另一个常见情况是试图访问未定义值的属性。

```
window.foo.bar
  // => TypeError: Cannot read property 'bar' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

### `SyntaxError`

当 JavaScript 引擎解析脚本并遇到语法上无效的代码时，会出现这些错误。如果 JavaScript 文件包含语法错误，文件中的所有代码都不会执行。

```
console.log('hello')
    notValid( 
```

Enter fullscreen mode Exit fullscreen mode

这段代码不仅会产生错误，而且无效语法前面的`console.log`甚至不会运行。

### `ReferenceError`

当代码引用当前范围内不存在的值时，会出现这种情况。例如:

```
console.log(somethingMadeUp)
  // => ReferenceError: somethingMadeUp is not defined 
```

Enter fullscreen mode Exit fullscreen mode

## 手动投掷错误

并非所有的错误都是偶然的。它们也可以被有意触发。当应用程序不能正常运行时，最好大声地、明确地、清楚地失败。否则，问题的原因可能不清楚，或者更糟的是，根本没有被开发人员注意到。

手动触发错误的最简单方法是使用 throw 语句:

```
throw 'Invalid input'; 
```

Enter fullscreen mode Exit fullscreen mode

这将自动创建一个带有消息“无效输入”的`Error`对象的实例，但是错误实例也可以手动创建并传递。

```
let error = new Error('Invalid input')
// later
throw error; 
```

Enter fullscreen mode Exit fullscreen mode

手动抛出错误对于库作者来说尤其有用，因为他们可以告知使用他们的库的开发人员他们是如何出错的。例如，当使用无效参数调用函数时。

```
function sayName(name) {
  if(typeof name !== 'string') {
    throw new Error('name must be a string, received:' + typeof name);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 try/catch 拦截错误

如果您知道某段代码有风险，可能会抛出错误，那么可以将它封装在一个`try/catch`语句中。

```
try {
  someCrashyFunction()
} catch(error) {
  // display it to the user maybe
  // and report it to Bugsnag
  Bugsnag.notify(error);
} 
```

Enter fullscreen mode Exit fullscreen mode

Try catch 块也可以相互嵌套。一旦处理了错误，如果希望将错误传递到调用堆栈的更高层，则可以再次引发错误。

```
try {
  someCrashyFunction()
} catch(error) {
  // Handle the error here:
  // ...
  // then pass it up the chain
  throw error;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 必须通过全局错误处理来捕捉所有错误

即使是最努力编写的代码，错误有时仍然会出现。没事的。错误时有发生。当他们这样做的时候，尽快知道是很重要的。这就是 Bugsnag 这样的错误报告工具的用武之地。

### 全局错误处理的工作原理

为了捕捉和处理浏览器会话中可能发生的所有错误，我们可以挂钩到`window.onerror`事件处理程序。这允许为任何可能弹出的未处理错误设置一个全局处理程序。这就是 [Bugsnag 错误报告库](https://github.com/bugsnag/bugsnag-js)用来报告基于浏览器的 JavaScript 应用程序中未被捕获的错误。

在节点环境中，没有`window`对象，所以等效的方法是使用`process.on('unhandledException, callback)`。

全局错误处理不能替代细粒度控制，而细粒度控制可以通过`try/catch`语句实现。相反，它为那些通过早期防线的异常提供了一个安全网。通过将错误处理放在离潜在问题的源头更近的地方，我们可能会更好地了解如何最好地处理它，并可能在用户注意到问题之前恢复。对于所有没有被发现的问题，我们都放心，因为我们知道我们的全局错误处理程序会把问题暴露给我们。

## 失信

随着 [ES2015](https://babeljs.io/learn-es2015/) 的出现，我们在 JavaScript 中获得了对[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)的一流支持，这极大地提高了异步代码的清晰度。承诺的一个缺点是他们倾向于接受在他们的方法中出现的错误。如果在这个方法中产生了一个错误，它将永远不会冒泡到全局错误处理程序，因此不会被报告给 Bugsnag。

```
fetch('https://my-api.endpoint')
.then((response) => {
  response.thisMethodDoesNotExist() // this error will be swallowed

  doSomethingElse() // this code will never run
}) 
```

Enter fullscreen mode Exit fullscreen mode

这就是为什么为所有承诺链添加一个 catch 语句总是最佳实践，这样就可以处理任何错误。

```
fetch('https://my-api.endpoint')
  .then((response) => {
      response.thisMethodDoesNotExist()
    doSomethingElse() // this code will never run
  })
  .catch((error) => {
    console.error(error)
    // # => response.thisMethodDoesNotExist is not a function
    Bugsnag.notify(error)
    // show the error to the user
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这解决了不可见错误的问题，但是它也有一些缺点。首先，为我们使用的每个承诺编写这个错误处理代码是很麻烦的。其次，如果 catch 语句中出现错误，它也会被吞掉，我们又回到了开始的地方。为了解决这个问题，我们可以挂钩到一个全局的未处理的承诺拒绝处理程序。

```
window.addEventListener("unhandledrejection", (event) => {
  console.error(event.reason);
  // report the error here
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何失败且没有明确的`catch`处理程序的承诺都将触发`unhandledrejection`事件。

如果您使用的是 [bugsnag-js 通知程序](https://github.com/bugsnag/bugsnag-js)，那么未处理的承诺拒绝将被自动捕获并记录到 bugsnag 中，因此您不必担心会错过它们。

## 属性错误

一旦捕获到错误，就可以对其进行检查，以便从中提取有用的信息。最重要的是**名称**、**消息**和**堆栈**属性。

有用信息的第一位是错误的**名称**和**消息**。这些字段是 Bugsnag 收件箱中错误列表的显示内容，也是浏览器控制台的打印内容。

初始化时会设置错误消息。

```
let error = new Error('This is my message')
    console.log(error.message)
    // => This is my message 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，错误的名称与其构造函数相同，因此当使用`new Error('oh no!')`或`throw('oh no!'`创建错误时，其名称将为“error”。如果你使用`new TypeError('oh no!')`创建一个错误，它的名字将会是“类型错误”。只需设置一个错误，就可以覆盖它的名称。

```
let myError = new Error('some message');
    myError.name = 'ValidationError';
    throw myError; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们将错误名称改为`ValidationError`，这将反映在 Bugsnag 仪表板中；但是，在某些浏览器(如 Chrome)中，它仍然会作为“错误”打印到控制台。为了解决这个问题，可以使用定制的错误类，我们将在本文稍后讨论这一点。

### 堆栈跟踪

`Error.prototype.stack`属性包含错误的 stacktrace。stacktrace 以简单字符串的形式存储在错误中，其中堆栈中的每个函数都由换行符分隔。在 bugsnag-js 库中，我们使用一个名为 [error-stack-parser](https://github.com/stacktracejs/error-stack-parser) 的工具将 stacktrace 解析成一个有用的数据结构。

值得注意的是，stacktrace 是由初始化错误的位置决定的，而不是由抛出错误的位置决定的。这意味着如果一个错误被创建并从`functionA`返回，然后在`functionB`中抛出，那么栈顶将是`functionA`。

很可能您会缩小您的 JavaScript 代码，当您这样做时，stacktrace 中的行将与原始源文件不匹配。为了找到原始源，我们使用源映射来查找和翻译堆栈跟踪。在本系列的另一篇博客[中了解更多关于源地图如何工作的信息。](https://dev.to/bugsnag/anatomy-of-source-maps-22dk)

## 创建自定义错误类型

有时，除了 JavaScript 语言中已经内置的错误类型之外，创建自定义错误类型也很有用。这种情况的一个可能的用例是，可以建立一个应用程序，以不同的方式处理不同类型的错误。

例如，在一个节点应用程序中，我们可能会有一个特殊的错误类来处理 API 请求中的验证错误。如果发现验证错误，应用程序会知道用一个 [HTTP 400 状态](https://httpstatuses.com/400)来响应。

自定义错误还允许捕获带有特定于该错误类的错误的附加自定义数据。

ES6 类使得定义自定义错误类型的任务变得极其简单。例如，如果我们想为无效字段抛出特定类型的错误，我们可以这样定义它。

```
class ValidationError extends Error {
  constructor(field, reason) {
    super(reason);
    this.field = field;
    this.reason = reason;
    // the next line is important so that the ValidationError constructor is not part
    // of the resulting stacktrace
    Error.captureStackTrace(this, ValidationError);
  }

  // we can also define custom methods on this class
  prettyMessage() {
     return `ValidationError: [${this.fields}] reason: ${this.reason}`;
     // ex: "ValidationError: [age] reason: Must be a number"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，错误处理代码可以利用`instanceof`来确定抛出了什么类型的错误，并做出适当的响应。例如，在一个 [Express.js](https://expressjs.com/en/guide/error-handling.html) 应用程序中，可以设置定制的中间件来完成这个任务。

```
app.use(function errorHandler (err, req, res, next) {
 if (error instanceof ValidationError) {
   // respond with 400 status and include relevant error details
   return res.status(400).json({
      type: error.name,
      message: error.prettyMessage(),
      field: error.field,
    });
  } else {
    // This is some other kind of error, let the default error handler deal with it
    next(error)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

尽管这个例子使用了 Express.js 中间件，但是在其他类型的 JavaScript 应用程序中也可以采用类似的方法，只需使用一个简单的`try/catch`。

```
try {
  submitForm();
} catch (error) {
  if (error instanceof ValidationError) {
    // show the error to the user
    displayErrorMessage(error.prettyMessage());
  } else {
    // pass it to the default error handler
    throw error;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果没有自定义的错误类，这种专门的错误处理会更加困难。这需要做一些棘手的事情，比如比较错误消息或一些自定义属性。幸运的是，使用 error 类进行比较要清楚得多。

## 包装完毕

当应用程序失败时，理想情况下，它们为用户提供了流畅的体验，但对于开发人员来说，它们应该响亮而清晰地失败，以便快速分析问题。正确利用 JavaScript 语言提供的错误处理工具有助于澄清应用程序中不透明的异常，以便快速理解和解决它们。

* * *

试试 Bugsnag 的 [JavaScript 错误报告](https://www.bugsnag.com/platforms/javascript/)，免费 14 天。