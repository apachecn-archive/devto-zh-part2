# 函数式 JavaScript:函数装饰器第 2 部分#JavaScript

> 原文：<https://dev.to/joelnet/function-decorators-part-2-javascript-4km9>

[本文最初发布在媒体](https://hackernoon.com/function-decorators-part-2-javascript-fadd24e57f83)上。

# 开头

> **函数装饰器**允许你在不修改原函数的情况下增强现有函数。

在第 1 部分中，我演示了函数装饰者如何将回调转化为承诺，然后再转化回来。但是函数装饰器比有限范围的回调和承诺有用得多，所以我认为这个主题需要重启。

我认为展示一堆例子将是展示函数装饰者的最好方式，所以**这篇文章将稍微轻于文字，更多地关注代码**。

# 一个函数装饰者的 Hello World

```
// basic decorator (supports 1 arity functions)
const decorator = f => a => f(a) 
```

Enter fullscreen mode Exit fullscreen mode

为了支持 n-arity 函数，我们可以把它扩展成这样(仍然什么也不做)。

```
// basic decorator (supports n-arity functions)
const decorator = f => (...args) => f(...args) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建并使用一个`helloWorld`装饰器来装饰`add`函数。

```
// our Hello World decorator
const helloWorld = f => (...args) => {
  console.log('Hello World')
  return f(...args)
}

// function to be decorate
const add = (a, b) => a + b

// decorate the function
const helloWorldAdd = helloWorld(add)

// run it
helloWorldAdd(3, 4)
//=> "Hello World"
//=> 7 
```

Enter fullscreen mode Exit fullscreen mode

使用这个基础装饰器作为您想要创建的任何函数装饰器的模板。

# 日志功能装饰器

轻松地将您的日志逻辑包装到现有函数中。

```
// note: logged function name may change with minification
const logger = f => (...args) => {
  const value = f(...args)
  console.log(`${f.name||'Anonymous'}(${args}) = ${value}`)
  return value
}

const add = (a, b) => a + b

// we can make a new decorated function like so
const logAdd = logger(add)
logAdd(3, 4)
//=> "add(3, 4) = 7"
//=> 7 
```

Enter fullscreen mode Exit fullscreen mode

作业:你如何修改它来支持异步功能？要得到提示，请看下面的定时函数装饰器。

# 定时器功能装饰器

同步和异步代码都可以使用的基本定时器函数。

第 15 行检查值是否是一个承诺，并将返回值放入一个 then 中，而不是返回它。

```
// timed decorator
const timed = f => (...args) => {
  const start = Date.now()
  const value = f(...args)
  return value && typeof value.then === 'function'
    ? value.then(value =>
      ({ timespan: Date.now() - start, value }))
    : { timespan: Date.now() - start, value }
}

// synchronous function
const fastFunction = x => x * 2

// asynchronous function
const slowFunction = x => new Promise(resolve =>
  setTimeout(() => resolve(x * 2), 2000)
)

timed(fastFunction)(123)
//=> { timespan: 0, value: 246 }

timed(slowFunction)(456)
//=> Promise: { timespan: 2000, value: 912 } 
```

Enter fullscreen mode Exit fullscreen mode

# 函数参数保护装饰器

防止所有参数为空或未定义。

```
// requireAll decorator
const requireAll = f => (...args) => {
  const valid = args.filter(arg => arg != null)
  if (valid.length < f.length) {
    throw new TypeError('Argument cannot be null or undefined.')
  }
  return f(...valid)
}

// decorated add function
const add = requireAll((a, b) => a + b)

add(3, 4)
//=> 7

add(3)
//=> TypeError: Argument cannot be null or undefined.

add(null, 4)
//=> TypeError: Argument cannot be null or undefined. 
```

Enter fullscreen mode Exit fullscreen mode

作业:如何改进这个装饰器？如何添加参数名？你如何防范一些争论？

# 异常处理

您可以返回一个包含值或错误的对象，而不是引发异常。这类似于任一单子如何处理它的值。(现在不用担心单子)。

```
// function decorator to catch exceptions
const tryCatch = f => (...args) => {
  try {
    return { error: null, value: f(...args) }
  } catch(err) {
    return { error: err, value: null }
  }
}

const double = x => {
  if (!isNumeric(x)) {
    throw new TypeError('value must be numeric')
  }

  return x * 2
}

// new "safe" double function
const safeDouble = tryCatch(double);

safeDouble('abc')
//=> { error: [TypeError: value must be numeric], value: null }

safeDouble(4)
//=> { error: null, value: 8 } 
```

Enter fullscreen mode Exit fullscreen mode

**作业**:研究并学会使用任一单子。更改此代码以返回。

# 取 JSON 函数装饰器

当使用 fetch 时，经常会看到这样的代码散布在你的代码库中:

```
fetch(url)
  .then(response => response.json())
  .then(data => /* now we can use data! */) 
```

Enter fullscreen mode Exit fullscreen mode

为了得到那个 json，你必须首先调用`response.json()`。

```
// function decorator
const withJson = f => url =>
  f(url).then(response => response.json())

// decorated function
const fetchJson = withJson(fetch)

// now all your fetch calls are simplified
fetchJson(url)
  .then(data => /* we are ready to go! */) 
```

Enter fullscreen mode Exit fullscreen mode

# 逢迎

如果你熟悉像 Ramda 的`curry`这样的 currying 函数，那么你可能已经熟悉函数装饰器了。

```
// basic curry function decorator
const curry = f => (...args) =>
  args.length >= f.length
    ? f(...args)
    : curry(f.bind(undefined, ...args))

const multiply = curry((a, b) => a * b)
const double = multiply(2)

double(3)
//=> 6

multiply(3, 3)
//=> 9

multiply(4)(4)
//=> 16 
```

Enter fullscreen mode Exit fullscreen mode

注:我推荐用更成熟的[库里函数](http://ramdajs.com/docs/#curry)，像 Ramda 的那个。尽管这种方法可以很好地工作，但它只是作为示例提供的。

# Next.js 浏览器检查

在我创建的一个 Next.js 项目中，我不得不限制几个函数只能在浏览器端执行。我可以用一个简单的函数装饰器干净利落地做到这一点。

```
const onlyBrowser = f => () =>
  process.browser && f()

class MyComponent extends Component {
  componentWillMount = onlyBrowser(() =>
    console.log('This is the browser!')
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

# 多种方式装修

有几种方法可以修饰函数。你如何决定使用 decorators 将取决于你的用例。

```
// decorate the function
const add = decorator((a, b) => a + b)

// create a new decorated function
const add = (a, b) => a + b
const decoratedAdd = decorator(add)

// decorate just for the call
const add = (a, b) => a + b
decorator(add)(3, 4) 
```

Enter fullscreen mode Exit fullscreen mode

# 组合功能装饰器

因为每个装饰器也返回一个函数，所以函数装饰器可以很容易地组合起来创建一个大型函数。

```
// returns true if is numeric
const isNumeric = n => !isNaN(parseFloat(n)) && isFinite(n)

// function decorator for only numeric args
const onlyNumeric = f => (...args) => {
  const valid = args.filter(isNumeric)
  if (valid.length < f.length) {
    throw new TypeError('Argument must be numeric.')
  }
  return f(...valid)
}

// function decorator to catch exceptions
const tryCatch = f => (...args) => {
  try {
    return { error: null, value: f(...args) }
  } catch(err) {
    return { error: err, value: null }
  }
}

// our double function
const double = x => x * 2

// decorated double function
const safeDouble = tryCatch(onlyNumeric(double));

safeDouble('abc')
//=> { error: [TypeError: value must be numeric], value: null }

safeDouble(4)
//=> { error: null, value: 8 } 
```

Enter fullscreen mode Exit fullscreen mode

你也可以使用函数组合来组合装饰器

```
// function composer
const compose = (f, g) => x => f(g(x))

// returns true if is numeric
const isNumeric = n => !isNaN(parseFloat(n)) && isFinite(n)

// function decorator for only numeric args
const onlyNumeric = f => (...args) => {
  const valid = args.filter(isNumeric)
  if (valid.length < f.length) {
    throw new TypeError('Argument must be numeric.')
  }
  return f(...valid)
}

// function decorator to catch exceptions
const tryCatch = f => (...args) => {
  try {
    return { error: null, value: f(...args) }
  } catch(err) {
    return { error: err, value: null }
  }
}

// compose two decorators into one decorator
const tryCatchOnlyNumeric = compose(tryCatch, onlyNumeric)

// decorated double function
const double = tryCatchOnlyNumeric(x => x * 2)

double('abc')
//=> { error: [TypeError: value must be numeric], value: null }

double(4)
//=> { error: null, value: 8 } 
```

Enter fullscreen mode Exit fullscreen mode

# 做出反应

反应和整个生态系统充满了功能装饰。如果你用过 React，很有可能你已经用过函数装饰器了。`react-redux`的`connect`是一个函数装饰器。`redux`的`bindActionCreators`是一个函数装饰者。

# 结束

函数装饰器是用来增强现有函数的强大工具。它们不是什么新东西，如果你还没有使用过函数装饰器，你很可能会在不久的将来使用它们。

即使它们如此强大且易于创建，我也没有看到很多人在他们的代码中创建函数装饰器。这告诉我，函数装饰器是未被充分利用的工具，值得更多的探索。

别忘了做这篇文章里的作业！

我很想在下面的评论中听到你如何使用函数装饰器来改进你的代码库！😃

干杯！

# 跟着我

Twitter:[https://twitter.com/joelnet](https://twitter.com/joelnet)
LinkedIn:[https://www.linkedin.com/in/joel-thoms/](https://www.linkedin.com/in/joel-thoms/)T5】Medium:[https://medium.com/@joelthoms/latest](https://medium.com/@joelthoms/latest)T8】dev . to:[https://dev.to/joelnet](https://dev.to/joelnet)T11】Github:[https://github.com/joelnet](https://github.com/joelnet)