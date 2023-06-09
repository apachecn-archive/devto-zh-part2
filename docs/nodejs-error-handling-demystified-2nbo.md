# 去神秘化的 Node.js 错误处理模式(有例子)

> 原文：<https://dev.to/fullstackcafe/nodejs-error-handling-demystified-2nbo>

[![Node.js Error Handling Demystified](img/8c2fa2c3ed9ad65fd4b881bc73c5981f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TePiqW55--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/620335/pexels-photo-620335.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26h%3D350)

异步语言中的错误处理以一种独特的方式工作，并带来许多挑战，有些是意想不到的。Node.js 中有七种主要的错误处理模式，让我们简单地检查一下。

> 最初发表于 [FullStack。永远不要再错过你的技术面试](https://www.fullstack.cafe)

## **错误返回值**

不异步工作的最简单模式。考虑:

```
var validateObject = function (obj) {
    if (typeof obj !== 'object') {
        return new Error('Invalid object');
    }
}; 
```

## **错误投掷**

建立良好的模式，在这种模式下，一个函数做它该做的事情，如果出现错误情况，它只是抛出一个错误。会让你处于不稳定状态。抓住它们需要额外的工作。此外，将异步调用包装在 try/catch 中也无济于事，因为错误是异步发生的。为了解决这个问题，我们需要*域*。域提供了异步尝试...接住。

```
var validateObject = function (obj) {
    if (typeof obj !== 'object') {
        throw new Error('Invalid object');
    }
};

try {
    validateObject('123');
}
catch (err) {
    console.log('Thrown: ' + err.message);
} 
```

## **错误回调**

通过回调返回错误是 Node.js 中最常见的错误处理模式。处理错误回调可能会变得一团糟(回调地狱或末日金字塔)。

```
var validateObject = function (obj, callback) {
    if (typeof obj !== 'object') {
        return callback(new Error('Invalid object'));
    }
    return callback();
};

validateObject('123', function (err) {
    console.log('Callback: ' + err.message);
}); 
```

## **错误发射**

当发出错误时，错误被广播给任何感兴趣的订阅者，并按照订阅的顺序在同一个进程节拍内处理。

```
var Events = require('events');
var emitter = new Events.EventEmitter();

var validateObject = function (a) {
    if (typeof a !== 'object') {
        emitter.emit('error', new Error('Invalid object'));
    }
};

emitter.on('error', function (err) {
    console.log('Emitted: ' + err.message);
});

validateObject('123'); 
```

## **承诺**

用于异步错误处理的承诺。考虑:

```
doWork()
.then(doWork)
.then(doError)
.then(doWork)
.catch(errorHandler)
.then(verify); 
```

## **试试...异步捕捉/等待**

ES7 Async/await 允许我们作为开发人员编写看起来同步的异步 JS 代码。

```
async function f() {

  try {
    let response = await fetch('http://no-such-url');
  } catch(err) {
    alert(err); // TypeError: failed to fetch
  }
}

f(); 
```

## **wait-to-js 库**

Javascript 中不带 try-catch 块的 async/await 的变体。考虑:

```
import to from 'await-to-js';

async function main(callback) {
    const [err,quote] = await to(getQuote());
    if(err || !quote) return callback(new Error('No Quote found');
    callback(null,quote);
} 
```

🔗**来源:**【gist.github.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *查看更多 FullStack 面试问题&答案上👉 [www.fullstack.cafe](https://www.fullstack.cafe)*