# 用更好的“dx”处理 JavaScript 错误

> 原文：<https://dev.to/nucleartide/javascript-error-handling-with-better-dx-1a57>

JavaScript 希望您用`try...catch`来处理错误，但是语法有些不方便:

#### 1.你必须从你的函数调用中分别声明结果变量*和*。

```
const userInput = 'fail'

let json
try {
  json = JSON.parse(userInput)
} catch (err) {
  console.error(err.stack)
}

// Do something with `json`... 
```

因为我们分别声明了`json`，所以我们不能将`json`声明为一个`const`绑定。

#### 2.`try...catch`促进全面错误处理。

```
try {
  // A bunch of stuff happens...
  // ...
  // ...
} catch (err) {
  console.log('welp')
  console.error(err.stack)
} 
```

您可能希望以更细粒度的方式处理错误。但是你会遇到问题 1 的冗长。

## 输入`dx`

`dx`是一个微型实用程序(只有几行代码),它解决了上面的两个棘手问题。

```
import { dx } from '@nucleartide/dx'

const [res, err] = dx(JSON.parse)('invalid json')
if (err) {
  console.error(err.stack)
} 
```

它允许您将声明和函数调用放在同一行。并且它促进了一种粒度的、*按功能*的错误处理风格。

它也适用于异步函数:

```
import { dx } from '@nucleartide/dx'

function asyncHello(name) {
  return Promise.reject(`hello ${name}`)
}

;(async () => {
  const [res, err] = await dx(asyncHello)('jesse')
  if (err) {
    console.error(err.stack)
  }
}) 
```

## 深信不疑？

试试吧，让我知道你的想法！也可以随意查看[源](https://github.com/nucleartide/dx)。

```
npm install @nucleartide/dx 
```