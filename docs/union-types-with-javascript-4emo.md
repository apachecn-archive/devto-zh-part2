# 使用 Javascript 的联合类型

> 原文：<https://dev.to/avalander/union-types-with-javascript-4emo>

*(封面图片:**灯笼**，作者安娜·桑切斯·英格拉达。原图[此处](http://annasanchez.tumblr.com/post/124411134425/lanterns-2013-h%E1%BB%99i-an-vietnam-h%E1%BB%99i-an-at-night) )*

最近我一直在学习榆树，我完全被它的 T2 工会类型吸引住了。在本文中，我将展示一种在 Javascript 中实现联合类型的方法，并通过例子解释它们是如何有用的。

# 什么是工会类型？

联合类型也称为代数数据类型(或 ADT ),是一种表达复杂数据的方式，这些数据可以采用多种形式。我不会深入研究联合类型理论，但是这篇[维基百科文章](https://en.wikipedia.org/wiki/Algebraic_data_type)很好地解释了它们。

现在您需要知道的是，union 类型是一种允许我们以多种形式表示和分类数据的类型，很像 *enum* ，但是更强大。

# 如何在 Javascript 中实现联合类型

在研究联合类型为什么有用以及如何使用它们之前，让我们尝试用 Javascript 实现它们。这里我实现了一个助手函数，我称之为`union`。它接收类型名列表，并返回描述联合类型的对象。

```
const union = types =>
    types.reduce((prev, type) => ({
        ...prev,
        [type]: data => ({
            match: fns => fns[type](data),
        }),
    }), {}) 
```

如果你不熟悉`reduce`是如何工作的，你应该看看[这个视频](https://www.youtube.com/watch?v=Wl98eZpkp-c)，但是这里有一个使用 for 循环的大致相当的版本。

```
const union = types => {
    const result = {}
    for (let type of types) {
        result[type] = data => ({
            match: fns => fns[type](data),
        })
    }
    return result
} 
```

这个函数为`types`数组中的每个名字创建一个类型的对象。每种类型都是一个工厂，可以接收一些数据并使用方法`match`返回一个对象。方法`match`将接收一个对象，该对象带有每个可用类型的函数，然后执行该对象所属的特定类型的函数。

现在我们可以使用`union`助手来创建联合类型。

让我们用一个愚蠢的例子来说明这是如何工作的。想象一下，我们需要能够处理关于小马的数据。众所周知，有三种不同的小马:土马、佩加斯马和独角兽。每种类型都有一些特定的能力。例如，pegasi 可以飞，独角兽可以使用魔法。

```
const Ponies = union([
    'EarthPony',
    'Pegasus',
    'Unicorn',
])

const twilight = Ponies.Unicorn({
    name: 'Twilight Sparkle',
    spell: 'Levitation',
})
const rainbow = Ponies.Pegasus({
    name: 'Rainbow Dash',
    speed: 20,
})

twilight.match({
    EarthPony: ({ name }) => `${name} is a peaceful earth pony.`,
    Pegasus: ({ name, speed }) => `${name} flies at a speed of ${speed}!`,
    Unicorn: ({ name, spell }) => `${name} uses ${spell}!`,
}) // -> 'Twilight Sparkle uses Levitation!' 
```

我们可以使用方法`match`来执行特定的逻辑，这取决于我们拥有的 pony 的种类。类似于我们如何在 Java 中的`enum`上使用`switch`语句，但是额外的好处是每种类型可以关联不同类型的数据。

# 用法举例

让我们看几个不那么愚蠢的例子来了解如何在实际应用程序中使用联合类型。

## 例 1:处理节点中的错误

让我们假设我们正在使用 node 和 express.js 构建一个 REST API。我们的 API 有一个端点，它通过 id 从数据库返回一个 pony。

我们的 express 应用程序看起来像这样。

```
const mongodb = require('mongodb')
const express = require('express')

const app = express()

mongodb.MongoClient.connect(DB_URL)
    .then(client => client.db(DB_NAME))
    .then(db => {
        app.get('/ponies/:id', /* here be our endpoint */)
        app.listen(3000, () => 'Server started.')
    }) 
```

如果你不熟悉 express，不要担心。您需要知道的是，我们将实现一个函数，它将接收一个请求对象(我们称之为`req`)和一个响应对象(我们称之为`res`)，并且该函数还将访问一个名为`db`的数据库连接。

我们的函数将检查用户是否经过认证，因为我们的 pony 数据库保存着非常敏感的信息。然后，它将从路径中读取`id`参数，并从数据库中获取具有该 id 的 pony。最后，它将在响应中发回 pony 数据。

至少有三件事可能出错。

1.  用户会话可能已过期，或者用户可能试图在没有有效令牌的情况下访问 API。
2.  数据库中可能没有给定 id 的 pony。
3.  我们可能会有意想不到的失败。例如，数据库可能会关闭。

让我们创建一个联合类型来模拟这三种类型的错误。

```
const ApiError = union([
    'InvalidCredentials',
    'NotFound',
    'Other',
]) 
```

如果用户没有被正确认证，我们将返回一个`InvalidCredentials`错误。如果数据库中不存在小马，我们将返回一个`NotFound`。我们将在`Other`中对所有意外错误进行分组。

我们来看第一步。假设我们有一个名为`authorise`的函数，它检查用户令牌，如果有效则返回`true`，否则返回`false`，并且我们有一些中间件，它从一个头或 cookie 中读取用户令牌并将其存储在`req.bearer`中。我们将把对`authorise`的调用包装在一个承诺中，因为我们有一些异步操作，我们希望通过承诺的拒绝分支来处理所有错误。

```
app.get('/ponies/:id', (req, res) =>
    new Promise((resolve, reject) => {
        if (authorise(req.bearer)) return resolve()
        return reject(ApiError.InvalidCredentials())
    })
) 
```

到目前为止一切顺利。如果用户没有得到适当的认证，承诺将被拒绝，我们不会执行链的其余部分。否则，我们现在可以从数据库中读取小马。我们将把对数据库的调用包装在另一个承诺中，如果我们在数据库中找到任何数据，就用数据来解决它，否则我们将拒绝一个`NotFound`错误。

```
app.get('/ponies/:id', (req, res) =>
    new Promise((resolve, reject) => {
        if (authorise(req.bearer)) return resolve()
        return reject(ApiError.InvalidCredentials())
    })
    .then(() => new Promise((resolve, reject)) =>
        db.collection('ponies').findOne({ id: req.params.id }, (err, data) => {
            if (err) {
                return reject(ApiError.Other(err))
            }
            if (data == null) {
                return reject(ApiError.NotFound(`Pony ${req.params.id} not found.`))
            }
            return resolve(data)
        })
    )
) 
```

如果出现问题，节点回调会返回一个错误，所以如果参数`err`中有任何内容，我们将拒绝带有`Other`错误的承诺。如果操作成功，如果数据库中没有记录，我们可能仍然得不到任何数据，然后我们会用一个`NotFound`错误拒绝这个承诺。否则，我们将有一些数据，我们可以用它来解决承诺。

下一步是，如果一切顺利，就在响应中发送回数据，否则我们希望根据出错的地方发送一个 HTTP 错误。

```
app.get('/ponies/:id', (req, res) =>
    new Promise((resolve, reject) => {
        if (authorise(req.bearer)) return resolve()
        return reject(ApiError.InvalidCredentials())
    })
    .then(() => new Promise((resolve, reject)) =>
        db.collection('ponies').findOne({ id: req.params.id }, (err, pony) => {
            if (err) {
                return reject(ApiError.Other(err))
            }
            if (pony == null) {
                return reject(ApiError.NotFound(`Pony ${req.params.id} not found.`))
            }
            return resolve(pony)
        })
    )
    .then(pony => res.json(pony))
    .catch(err => err.match({
        InvalidCredentials: () => res.sendStatus(401),
        NotFound: message => res.status(404).send(message),
        Other: e => res.status(500).send(e)
    }))
) 
```

仅此而已。如果我们在拒绝分支中得到一个错误，我们可以使用方法`match`发送一个相关的 HTTP 状态代码和一个不同的消息。

老实说，这并不令人印象深刻。我们可以用类似 enum 的对象做完全相同的事情。即使我认为类型匹配相当优雅，但与一个好的 ol' `switch`语句相比，这并没有太大的区别。

你可以在这个 [GitHub repo](https://github.com/Avalander/union-types-example/tree/master/examples/node) 中查看完整的例子。

## 示例 2:在 React 组件中获取远程数据

那么，让我们试一个不同的例子。假设我们有一个 React 组件，它从远程服务器加载一些数据。仔细想想，这个组件可能有四种状态之一:

1.  **未问**。还没有向服务器请求数据。
2.  **待定**。已向服务器请求数据，但尚未收到响应。
3.  **成功**。已从服务器接收到数据。
4.  **故障**。通信过程中某处发生了错误。

让我们用 union 类型对此建模。

```
const RemoteData = union([
    'NotAsked',
    'Pending',
    'Success',
    'Failure',
]) 
```

我们走吧。现在我们想要创建一个 React 组件，它将加载状态`NotAsked`并根据状态呈现不同的内容。

```
class Pony extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            data: RemoteData.NotAsked()
        }
    }
} 
```

我们已经创建了一个组件，它将保存一些数据，并从状态`NotAsked`开始。让我们呈现那个状态。我们可能需要一个告诉用户加载数据的文本和一个触发服务器调用的按钮。

```
class Pony extends React.Component {
    // previous code here...
    render() {
        return this.state.data.match({
            NotAsked: () => (
                <div>
                    <h1>Press "load"</h1>
                    <button onClick={this.fetchData}>Load!</button>
                </div>
            )
        })
    }
} 
```

你可能已经注意到了`button`中的`onClick={this.fetchData}`。当用户按下按钮时，我们希望触发对服务器的请求，所以我们需要给组件添加一个`fetchData`方法。但是首先，让我们创建一个函数来模拟对服务器的调用，因为我们没有实际的服务器可以调用。

```
const fetchPony = () => new Promise((resolve, reject) =>
    setTimeout(() => {
        if (Math.random() > 0.2) {
            return resolve({
                name: 'Twilight Sparkle',
                type: 'Unicorn',
                element: 'Magic',
            })
        }
        return reject({
            message: `I just don't know what went wrong.`,
        })
    },
    500)
) 
```

函数`fetchPony`返回一个在 500 毫秒内解决的承诺，以模拟到服务器的往返，并给我们一些时间来观察状态变化。此外，它会在 20%的情况下返回一个错误，这样我们也可以看到那个状态。

现在让我们在`Pony`组件中实现`fetchData`方法。

```
class Pony extends React.Component {
    constructor(props) {
        // previous code here...
        this.fetchData = this.fetchData.bind(this)
    }

    fetchData() {
        this.setState({ data: RemoteData.Pending() })
        fetchPony()
            .then(pony => this.setState({ data: RemoteData.Success(pony) }))
            .catch(err => this.setState({ data: RemoteData.Failure(err) }))
    }

    // render method here...
} 
```

我们的方法`fetchData`会首先将状态改为`Pending`，然后模拟对服务器的调用。当 promise 解析时，它会根据收到的数据将状态更改为`Success`。如果发生错误，它会改为将状态更改为`Failure`，并将错误传递下去。

最后一步是渲染三个缺失的状态。

```
class Pony extends React.Component {
    // previous code here...

    render() {
        this.state.data.match({
            NotAsked: () => (
                <div>
                    <h1>Press "load"</h1>
                    <button onClick={this.fetchData}>Load!</button>
                </div>
            ),
            Pending: () => (
                <div>
                    <h1>Loading...</h1>
                </div>
            ),
            Success: ({ name, type, element }) => (
                <div>
                    <p><strong>Name:</strong> {name}</p>
                    <p><strong>Type:</strong> {type}</p>
                    <p><strong>Element of Harmony:</strong> {element}</p>
                    <button onClick={this.fetchData}>Reload</button>
                </div>
            ),
            Failure: ({ message }) => (
                <div>
                    <p>{message}</p>
                    <button onClick={this.fetchData}>Retry</button>
                </div>
            )
        })
    }
} 
```

我们完事了。我们有一个组件可以通知用户对服务器的调用发生了什么，而不用到处使用混乱的布尔标志。

你可以在这个 [GitHub repo](https://github.com/Avalander/union-types-example/tree/master/examples/react) 中查看完整的例子。

# 此实现的局限性

如果您将这个实现与 Elm 中的联合类型进行比较，您会发现它相当有缺陷。Elm 是一种强类型语言，编译器会告诉我们是否忘记处理联合类型的分支，或者是否匹配了错误的数据类型。此外，只要数据的特殊性不同，Elm 允许多次匹配一种类型。有了 Javascript，我们就没有这些了。

说实话，使用这种实现，我们甚至不会从代码编辑器中获得任何自动完成的帮助。然而，这可以通过更详细的实现或者使用 TypeScript 类型化来解决。

# 结论

在这篇文章中，我想探索如何在 Javascript 中实现联合类型，以及使用它们是否会产生更简洁、更易于扩展的代码。我不得不说，我对此百感交集。我喜欢这种模式，我认为它成功地产生了易于推理和扩展的代码。另一方面，我们错过了从静态类型语言中获得的所有安全性，这是问题的一半。我们还没有真正实现任何我们仅仅通过代码中的一些合理结构无法完成的事情。

你怎么想呢?除了迎合函数式编程的审美偏好之外，联合类型还有什么用处吗？我很乐意在评论区看到你的想法和意见。