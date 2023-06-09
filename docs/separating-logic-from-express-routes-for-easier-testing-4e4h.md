# 将逻辑从快速路线中分离出来，以便于测试

> 原文：<https://dev.to/ccleary00/separating-logic-from-express-routes-for-easier-testing-4e4h>

***原载于 [coreycleary.me](https://www.coreycleary.me/separating-logic-from-express-routes-for-easier-testing/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我还定期发送备忘单、其他优秀教程的链接(由其他人提供)和其他免费赠品。*

您是否曾经困惑于如何以一种可测试的方式构建您的 Express 应用程序？

与 Node.js 世界中的大多数事情一样，有许多方法可以编写和构造 Express 应用程序。

不过，最好的起点通常是规范的“Hello World”示例，这里有一个来自 [Express 文档](https://expressjs.com/en/starter/hello-world.html) :

```
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(port, () => console.log(`Example app listening on port ${port}!`)) 
```

Enter fullscreen mode Exit fullscreen mode

行`app.get('/', (req, res) => res.send('Hello World!'))`是提供响应的实际路径。

因此，如果我们想要添加一个新的 HTTP 路由，遵循在对`.get()`或`.post`方法的回调中添加路由处理代码的相同模式似乎是有意义的。

如果我们有一个 web 论坛应用程序，并且想要创建一个用户，那么代码可能看起来像:

```
app.post('/api/user', async (req, res) => {
  const userName = req.body.user_name
  const userType = req.body.user_type
  try {
    await insert(userType, userName)
    res.sendStatus(201)
  } catch(e) {
    res.sendStatus(500)
    console.log(e)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

...它遵循示例“Hello World”结构

但是到了实际测试的时候呢？我们如何端到端地测试路由，以及对路由处理程序中包含的实际用户创建逻辑进行单元测试？

按照目前的情况，一个测试可能看起来像:

```
describe('POST /api/user', () => {
  before(async () => {
    await createTable('admin')
    await createTable('member')
  })

  after(async () => {
    await dropTable('admin')
    await dropTable('member')
  })

  it('should respond with 201 if user account created successfully', async () => {
    const response = await request(app)
      .post('/api/user')
      .send({user_name: "ccleary00", user_type: "admin"})
      .set('Accept', 'application/json')

      expect(response.statusCode).to.equal(201)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在用户创建逻辑在回调中，所以我们不能只“导出”回调。为了测试这个逻辑，我们必须通过向服务器发送一个请求来测试它，这样它实际上会到达 POST /api/user 路径。

这就是我们在上面所做的，使用 [supertest](https://github.com/visionmedia/supertest) 发送一个请求，并对来自服务器的结果响应执行断言。

### 空气中的气味

但是这件事有点不对劲...

为应该作为一个单元进行测试的东西编写这样的端到端测试感觉很奇怪。

如果用户创建逻辑开始变得更加复杂，比如需要调用电子邮件服务来发送用户注册电子邮件，需要检查用户帐户是否已经存在，等等，该怎么办呢？？我们必须测试伴随代码的所有不同的逻辑分支，用 supertest 从头到尾进行端到端测试会很快变得非常烦人。

幸运的是，使其可测试的方法非常简单。更不用说它通过将我们的 HTTP 代码与我们的业务逻辑代码分离，帮助我们实现更好的关注点分离。

### 从路线中提取逻辑

使这个路径可测试的最简单的方法是将当前回调中的代码放入它自己的函数:

```
export default async function createUser (req, res) => {
  const userName = req.body.user_name
  const userType = req.body.user_type
  try {
    await insert(userType, userName)
    res.sendStatus(201)
  } catch(e) {
    res.sendStatus(500)
    console.log(e)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后将*即*导入到快捷路线:

```
const createUser = require('./controllers/user')
app.post('/api/user', createUser) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们仍然可以使用与以前相同的测试代码为该路线编写端到端测试，但是我们也可以将`createUser()`功能更多地作为*单元*来测试。

### 一点一点地砌

例如，如果我们有验证/转换逻辑来禁止响亮的、全大写的用户名，我们可以在中添加并断言存储在数据库中的名称确实是小写的:

```
export default async function createUser (req, res) => {
  const userName = req.body.user_name.toLowerCase() // QUIETER!!
  const userType = req.body.user_type
  try {
    await insert(userType, userName)
    res.sendStatus(201)
  } catch(e) {
    res.sendStatus(500)
    console.log(e)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

验证/转换逻辑可能会变得更加复杂，比如需要在创建用户之前删除用户名中的空白，或者检查是否有冒犯性的名称，等等。你明白了。

在这一点上，我们可以将该逻辑提取到它自己的功能中，并作为一个单元测试*和*。

```
export function format(userName) {
  return userName.trim().toLowerCase()
}

describe('#format', () => {
  it('should trim white space from ends of user name', () => {
    const formatted = format(' ccleary00 ')
    expect(formatted).to.equal('ccleary00')
  })

  it('should convert the user name to all lower case', () => {
    const formatted = format('CCLEARY00')
    expect(formatted).to.equal('ccleary00')
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们可以将路由回调中的所有逻辑分解成单个单元，以便更容易测试，而不必模拟出很多东西。

虽然我们*可以*使用我们原始的方式编写这些测试，向 Express route 发送请求，但是这样做要困难得多。当编写测试很困难时，它们往往根本不会被编写...

### 包扎

构造 Express 应用程序的方法有很多，您可以通过将核心用户创建逻辑提取到一个“服务”中，同时让路由控制器处理验证，从而进一步分解这种方法。

但是现在，关键是要避免在路由回调中加入逻辑。您将使自己在将来更容易测试和重构。

测试应该是容易的，而不是困难的。如果你发现为你的应用程序编写测试是痛苦的，这通常是你需要重组或重写部分代码的第一个暗示。有时候你甚至意识不到这一点，直到你已经写了很多代码，而进行重构会更加痛苦。

我发现避免这种情况的最好方法是使用测试驱动开发(TDD)——它最终让我多次避免了编写糟糕的代码(比如我在这篇文章中用作起始示例的 Express user route 代码)。

首先写测试，然后写代码，这可能感觉很奇怪，但是如果你想得到一些指导，采取一种有助于“点击”的心态，[在这里查看我在 TDD 上写的另一篇文章](https://www.coreycleary.me/tried-tdd-and-didnt-realize-the-benefits-try-it-the-next-time-you-get-writers-block/)。

此外，我正在编写许多新的内容来帮助简化 JavaScript(以及一般的 JavaScript)测试。更容易，因为我觉得不需要像有时候那么复杂。如果你不想错过这些新帖子中的一个，这里有一个链接[来订阅我的时事通讯！](https://www.coreycleary.me/about/)