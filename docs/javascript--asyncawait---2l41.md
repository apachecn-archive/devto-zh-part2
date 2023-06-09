# 一直误解了 JavaScript 的 async/await 的规格

> 原文：<https://dev.to/jgs/javascript--asyncawait---2l41>

虽然可能是很初步的事情，但是因为是以年为单位误解写代码的，所以为了忏悔也会写...

要说有什么不同，那就是 async function 的处理。 async function 也认为与`function`相同，以为如果不返回`Promise`，呼叫方就无法进行`await`。 因此，到目前为止是

```
const fn = () => new Promise(async (resolve, reject) => {...}) 
```

Enter fullscreen mode Exit fullscreen mode

像这样暂时返回了`Promise`。 但是，async function 调用后会回复`Promise`。

参照:[https://developer . Mozilla . org/ja/docs/Web/JavaScript/Reference/Statements/async _ function](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/async_function)

```
const fn = async () => {}

console.log(fn)
console.log(fn()) 
```

Enter fullscreen mode Exit fullscreen mode

在 Node.js v9.5 中确认了。

```
[AsyncFunction: fn]
Promise { undefined } 
```

Enter fullscreen mode Exit fullscreen mode

`Promise`返回... 仔细调查的话，在 async function 中`return`的话会将`Promise`定为`resolve`，而`throw`的话会将`Promise`定为`reject`。

```
const fn = async () => {
  return 'yo'
}

const main = async () => {
  const res = await fn()
  console.log(res)
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

=>

```
yo 
```

Enter fullscreen mode Exit fullscreen mode

```
const fn = async () => {
  throw new Error('err')
  return 'yo'
}

const main = async () => {
  try {
    const res = await fn()
    console.log(res)
  } catch (err) {
    console.log(err)
  }
}

main() 
```

Enter fullscreen mode Exit fullscreen mode

=>

```
Error: err 
```

Enter fullscreen mode Exit fullscreen mode

真的吗...。 我的`Promise`是什么...... 非常不好意思……