# 异步函数与返回承诺的函数

> 原文：<https://dev.to/mywebstuff_hq/async-function-vs-a-function-that-returns-a-promise-3lpo>

在一个仅仅返回承诺的函数和一个用关键字`async`声明的函数之间有一个很小但非常重要的区别。

看看下面的片段:

```
function fn(obj) {
  const someProp = obj.someProp
  return Promise.resolve(someProp)
}

async function asyncFn(obj) {
  const someProp = obj.someProp
  return Promise.resolve(someProp)
}

asyncFn().catch(err => console.error('Catched')) // => 'Catched'
fn().catch(err => console.error('Catched')) // => TypeError: Cannot read property 'someProp' of undefined 
```

正如你所看到的，上面的两个函数有相同的主体，在这两种情况下，我们试图访问一个参数的属性。这两个函数之间唯一的区别是`asyncFn`是用`async`关键字声明的。

这意味着 Javascript 将确保`asnycFn`返回一个承诺(解决或拒绝),即使其中出现错误，在我们的例子中调用我们的`.catch()`块。

然而对于`fn`函数，引擎还不知道该函数将返回一个`Promise`，因此它不会调用我们的`catch()`块。

## 更真实的版本

我知道你现在在想什么:

> "我到底什么时候犯过这样的错误？"

对吗？

让我们创建一个简单的应用程序来完成这个任务。

假设我们有一个使用 MongoDB 的节点的带有 MongoDB 的 express app。JS 驱动。如果你不相信我，我已经把所有的代码放在这个 github repo 上，所以你可以在本地克隆和运行它，但是我也会把所有的代码复制粘贴到这里。

这是我们的`app.js`文件:

```
// app.js
'use strict'

const express = require('express')
const db = require('./db')

const userModel = require('./models/user-model')
const app = express()

db.connect()

app.get('/users/:id', (req, res) => {
  return userModel
    .getUserById(req.params.id)
    .then(user => res.json(user))
    .catch(err => res.status(400).json({ error: 'An error occured' }))
})

app.listen(3000, () => console.log('Server is listening')) 
```

好好看看路由定义中的那个`.catch`块！那是魔法将会(很可能不会)发生的地方。

`db.js`文件可用于连接到 mongo 数据库并获得 db 连接:

```
'use strict'

const MongoClient = require('mongodb').MongoClient

const url = 'mongodb://localhost:27017'
const dbName = 'async-promise-test'

const client = new MongoClient(url)

let db

module.exports = {
  connect() {
    return new Promise((resolve, reject) => {
      client.connect(err => {
        if (err) return reject(err)
        console.log('Connected successfully to server')

        db = client.db(dbName)
        resolve(db)
      })
    })
  },
  getDb() {
    return db
  }
} 
```

最后，我们有用户模型文件，它现在只有一个名为`getUserById`的函数:

```
// models/user-model.js
'use strict'

const ObjectId = require('mongodb').ObjectId
const db = require('../db')

const collectionName = 'users'

module.exports = {
  /**
   * Get's a user by it's ID
   * @param {string} id The id of the user
   * @returns {Promise<Object>} The user object
   */
  getUserById(id) {
    return db
      .getDb()
      .collection(collectionName)
      .findOne({ _id: new ObjectId(id) })
  }
} 
```

如果你回头看一下`app.js`文件，你会发现在访问 url 为`localhost:3000/users/<id>`的站点时，我们会调用用户模型文件中定义的 getUserById 函数，并传入请求的`id`参数。

假设你访问了以下网址:`localhost:3000/users/1`。你认为会发生什么？

如果你回答:“我将从 mongo 客户端得到一个巨大的错误”，你是对的。确切地说，您将得到这样一个错误:

```
Error: Argument passed in must be a single String of 12 bytes or a string of 24 hex characters 
```

你怎么看，这个(通过评论强调)`.catch`块会被称为？

```
// app.js

// ... stuff ...

app.get('/users/:id', (req, res) => {
  return userModel
    .getUserById(req.params.id)
    .then(user => res.json(user))
    .catch(err => res.status(400).json({ error: 'An error occured' })) // <=== THIS ONE HERE!
})

// ... stuff ... 
```

没有。

一点也不。

如果你把函数声明改成这样会怎么样？

```
module.exports = {
  // Note that async keyword right there!
  async findById(id) {
    return db
      .getDb()
      .collection(collectionName)
      .findOne({ _id: new ObjectId(id) })
  }
} 
```

是的，你找到窍门了。我们的`.catch()`块将被调用，我们将用一个漂亮的 json 错误来响应用户。

## 离别的思绪

我希望这些信息对你们中的一些人来说是新的。然而，请注意，在这篇文章中，我并不是想让你总是使用`async`函数——尽管它们非常棒。他们有他们的用例，但是他们仍然只是承诺的语法糖。

我只是想让你知道，有时候多一点小心这些承诺会有很大的帮助，当你遇到类似上面的错误时，你可能会知道问题出在哪里。