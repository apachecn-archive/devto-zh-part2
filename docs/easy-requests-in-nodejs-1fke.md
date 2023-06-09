# NodeJS 中的简单请求

> 原文：<https://dev.to/nulldreams/easy-requests-in-nodejs-1fke>

⚠:在你开始阅读之前，这是我的第一篇文章，我没有太多的博客经验，所以请给我一个关于这篇文章的反馈，☄

[![follow the rabbit](img/501a4db8ec912188b8a7b5ffb9e54200.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vj5OBk8Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tex6ej8ecu75ppoxa8ez.jpg)

### 嗨！

今天我将与你分享我如何使用 async/await 来使我的 http/https 请求更容易。

### 什么是 async/await？

简单的回答，当我们使用`await`表达式时，一个有`async`声明的函数可以被“暂停”。

### 让我们看看它是如何工作的……

[https://www.youtube.com/embed/plfMjbnM2Ek](https://www.youtube.com/embed/plfMjbnM2Ek)

在《黑客帝国》(1999)中，三位一体给尼欧发了一条信息，上面写着`Folow the white rabbit`。在这个例子中，Trinity 遇到了异步 javascript 的问题:

```
function sendMessageToNeo() {
    partOne()
    partTwo()
}
function partOne() {
    setTimeout(() => {
        console.log('follow the')
    }, 1000)
}
function partTwo() {
    setTimeout(() => {
        console.log('white rabbit')
    }, 500)
}

sendMessageToNeo() 
```

输出

```
white rabbit
follow the 
```

利用`async/await`我们可以帮助三一

```
async function sendMessageToNeo() {
    await partOne()
    await partTwo()
}
async function partOne() {
    await wait(1000)
    console.log('follow the')
}
async function partTwo() {
    await wait(500)
    console.log('white rabbit')
}

async function wait(ms = 0) {
    return new Promise(r => setTimeout(r, ms))
}

sendMessageToNeo() 
```

输出

```
follow the
white rabbit 
```

🐰🐰🐰

### 让我们开始我们的项目吧

在项目文件夹中创建三个文件

*   package.json
*   请求. js
*   app.js

### package.json

```
{  "dependencies":  {  "cheerio":  "^1.0.0-rc.2",  "request":  "^2.87.0"  }  } 
```

*   `cheerio`:用于 html 操作
*   `request`:提出要求

### [T1】request . js](#requestjs)

```
const request = require('request')

async function get (url) {
  return new Promise((resolve, reject) => {
    request({ url, method: 'GET' }, (error, response, body) => {
      if (error) return reject(error)

      return resolve({ body, response })
    })
  })
}

async function post (url, data) {
  return new Promise((resolve, reject) => {
    request({ url, method: 'POST', data }, (error, response, body) => {
      if (error) return reject(error)

      return resolve({ body, response })
    })
  })
}

module.exports = {
  get,
  post
} 
```

在`request.js`中，我们为请求创建了一个同步逻辑，参见`async`声明和`Promises`。

### app.js

```
const request = require('./request')
const cheerio = require('cheerio')

async function sendRequest () {
  let { response, body } = await request.get(`https://www.google.com.br/search?q=node+js`)

  if (response.statusCode !== 200) {
      return error(response, body)
  }

  success(response, body)
}

function success (response, body) {
    const $ = cheerio.load(body)

    console.log(`Status: ${response.statusCode}`)
    console.log(`Message: ${response.statusMessage}`)
    console.log(`Request: ${$('.g').length} results found!`)
}
function error (response) {
    console.log(`Status: ${response.statusCode}`)
    console.log(`Message: ${response.statusMessage}`)
}

sendRequest() 
```

在`app.js`中，我们将向 Google 发出请求，如果我们有成功的响应，我们将从结果中打印一个计数。

输出

```
Status: 200
Message: OK
Request: 10 results found! 
```

### 见两者不同

无异步/等待

```
function sendRequest() {
    request({ url: '_url', method: 'GET' }, (error, response, body) => {
      if (error) return console.log(error)

      console.log(body)
      console.log(response)
    })  
}
sendRequest() 
```

带异步/等待

```
async function sendRequest() {
    let { response, body } = await request.get(`_url`)

    console.log(body)
    console.log(response)
}
sendRequest() 
```

## 结论

在这篇文章中，我们可以看到`async/await`如何让我们的代码变得更漂亮，有了这个家伙，我们就不会有著名的回调地狱的问题

回调地狱

```
function hell() {
    functionOne('one', (err, one) => {
        console.log(one)
        functionTwo('two', (err, two) => {
            console.log(two)
            functionThree('three', (err, three) => {
                console.log(three)
                functionFour('four', (err, four) => {
                    console.log(four)
                })
            })
        })
    })
} 
```

带异步/等待

```
async function heaven() {
    let one = await functionOne('one')
    let two = await functionTwo('two')
    let three = await functionThree('three')
    let four = await functionFour('four')

    console.log(one)
    console.log(two)
    console.log(three)
    console.log(four)
} 
```

所以，如果你对 async/await 有问题，或者你对我有什么建议，请在这篇文章中发表评论。

再见！回头见！

[![bye](img/f8555732a8d27df95474cd47629539e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KARXg9Zw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1slphnrscfe2b8mpw89w.gif)