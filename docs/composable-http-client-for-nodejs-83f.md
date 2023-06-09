# NodeJS 的可组合 HTTP 客户端

> 原文：<https://dev.to/simov/composable-http-client-for-nodejs-83f>

所以我做了这个 NodeJS 的 HTTP 客户端:

```
var compose = require('request-compose') 
```

Enter fullscreen mode Exit fullscreen mode

> 顺便说一句，如果你想跳过这一步，马上沉浸在 FP 的美好中- [不要再看了](https://github.com/simov/request-compose)

又是如何使用的？

```
var {res, body} = await compose.client({
  url: 'https://api.github.com/users/simov',
  headers: {
    'user-agent': 'request-compose'
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

> 哦哇！真的！？又一个 NodeJS 的 HTTP 客户端！
> A M A Z I N G！！！

# ..API，API..到处

作为一个最终用户，如果我想在别人的模块中修改、更改或添加一些东西，该怎么办？我有什么选择？

*   在 GitHub 上打开一个问题并请求它
*   自己实现并提交一个拉取请求
*   搜索我需要的替代模块
*   重复

原因是因为模块作者为您提供了一个 API，告诉您可以做什么，不可以做什么。你实际上被锁在里面了。作者们还极力保护他们项目的范围，不让无关的东西侵入。

但是，如果我们有更强大的原语，允许我们更进一步，优雅地组成我们自己的东西，会怎么样呢？就我们自己而言，完全绕过了对方解决方案中出现的 API 和范围瓶颈。

# 作文

幸运的是有这样一个原语叫做[功能组合](https://en.wikipedia.org/wiki/Function_composition_(computer_science)):

> 在计算机科学中，**函数组合**(不要与对象组合混淆)是将简单函数组合起来构建更复杂函数的行为或机制。就像数学中通常的函数合成一样，每个函数的结果作为下一个函数的自变量传递，最后一个函数的结果就是整体的结果。

事实上，[请求撰写](https://github.com/simov/request-compose)揭露的正是[也就是](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises#Composition) :

```
var compose = (...fns) => (args) =>
  fns.reduce((p, f) => p.then(f), Promise.resolve(args)) 
```

Enter fullscreen mode Exit fullscreen mode

在其核心[请求-组合](https://github.com/simov/request-compose)甚至不是一个客户端，它是一个函数式编程模式，一个想法，一个简单的命令行程序来帮助你组合你自己的东西。

有了它，你可以编写任何函数，无论是否异步:

```
var sum = compose(
  (x) => x + 1,
  (x) => new Promise((resolve) => setTimeout(() => resolve(x + 2), 1000)),
  (x) => x + 3,
  async (x) => (await x) + 4
)
await sum(5) // 15 (after one second) 
```

Enter fullscreen mode Exit fullscreen mode

或者稍微深入一点——构建自己的 HTTP 客户端:

```
var compose = require('request-compose')
var https = require('https')

var request = compose(
  (options) => {
    options.headers = options.headers || {}
    options.headers['user-agent'] = 'request-compose'
    return options
  },
  (options) => new Promise((resolve, reject) => {
    https.request(options)
      .on('response', resolve)
      .on('error', reject)
      .end()
  }),
  (res) => new Promise((resolve, reject) => {
    var body = ''
    res
      .on('data', (chunk) => body += chunk)
      .on('end', () => resolve({res, body}))
      .on('error', reject)
  }),
  ({res, body}) => ({res, body: JSON.parse(body)}),
)

var {res, body} = await request({
  protocol: 'https:',
  hostname: 'api.github.com',
  path: '/users/simov',
}) 
```

Enter fullscreen mode Exit fullscreen mode

> 你能找到 API 吗？没有。
> 这都是你的，你自己的基于承诺的 HTTP 客户端。
> 祝贺你！

# 实用性

这很酷，但不太实用。毕竟，我们通常试图将代码提取到模块中，而不是在一个地方编写所有的代码。

如果你必须自己做所有的工作，为什么你还要费心使用[请求-撰写](https://github.com/simov/request-compose)。

答案很简单:

> 你可以选择你想要使用的，扩展它，或者不要全部使用-从头开始构建你自己的东西。

然而，有一堆函数被巧妙地命名为中间件，封装了你可能会发现有用的 HTTP 客户端逻辑:

```
var compose = require('request-compose')
var Request = compose.Request
var Response = compose.Response

var request = compose(
  Request.defaults({headers: {'user-agent': 'request-compose'}}),
  Request.url('https://api.github.com/users/simov'),
  Request.send(),
  Response.buffer(),
  Response.string(),
  Response.parse(),
)

var {res, body} = await request() 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，这些中间件只是一个可能实现的例子。**我自己的实现。**但是你没有被锁定，因为它没有隐藏在 API 墙后面。

你可以自由创作你自己的东西:

```
var compose = require('request-compose')
var Request = compose.Request
var Response = compose.Response

var request = (options) => compose(
  Request.defaults(),
  // my own stuff here - yay!
  ({options}) => {
    options.headers['user-agent'] = 'request-compose'
    options.headers['accept'] = 'application/vnd.github.v3+json'
    return {options}
  },
  // base URL? - no problem!
  Request.url(`https://api.github.com/${options.url}`),
  Request.send(),
  Response.buffer(),
  Response.string(),
  Response.parse(),
)(options)

var {res, body} = await request({url: 'users/simov'}) 
```

Enter fullscreen mode Exit fullscreen mode

> 把它放在 NPM 的一个模块里，然后就到此为止。

# 满圆

拥有独立的中间件，我们可以随心所欲地安排和扩展，这很好，但是我们的代码能更有表现力，更简洁吗？

这就是 [compose.client](https://github.com/simov/request-compose#client) 接口存在的唯一目的:

```
var {res, body} = await compose.client({
  url: 'https://api.github.com/users/simov',
  headers: {
    'user-agent': 'request-compose'
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

> 正如你可能已经猜到的，传递给 [compose.client](https://github.com/simov/request-compose#client) 的选项仅仅是使用完全相同的[内置中间件](https://github.com/simov/request-compose#bundled-middlewares)在幕后组成 HTTP 客户端。

# 变大

让我们看看硬币的另一面——不要老盯着 HTTP 内部——我们可以问自己:

> 我们如何使用功能组合来构建更大的东西？

如何构建一个高阶的 HTTP 客户端:

```
var compose = require('request-compose')

var search = ((
  github = compose(
    ({query}) => compose.client({
      url: 'https://api.github.com/search/repositories',
      qs: {q: query},
      headers: {'user-agent': 'request-compose'},
    }),
    ({body}) => body.items.slice(0, 3)
      .map(({full_name, html_url}) => ({name: full_name, url: html_url})),
  ),
  gitlab = compose(
    ({query, token}) => compose.client({
      url: 'https://gitlab.com/api/v4/search',
      qs: {scope: 'projects', search: query},
      headers: {'authorization': `Bearer ${token}`},
    }),
    ({body}) => body.slice(0, 3)
      .map(({path_with_namespace, web_url}) =>
        ({name: path_with_namespace, url: web_url})),
  ),
  bitbucket = compose(
    ({query}) => compose.client({
      url: 'https://bitbucket.org/repo/all',
      qs: {name: query},
    }),
    ({body}) => body.match(/repo-link" href="[^"]+"/gi).slice(0, 3)
      .map((match) => match.replace(/repo-link" href="\/([^"]+)"/i, '$1'))
      .map((path) => ({name: path, url: `https://bitbucket.org/${path}`})),
  ),
  search = compose(
    ({query, cred}) => Promise.all([
      github({query}),
      gitlab({query, token: cred.gitlab}),
      bitbucket({query}),
    ]),
    (results) => results.reduce((all, results) => all.concat(results)),
  )) =>
    Object.assign(search, {github, gitlab, bitbucket})
)()

var results = await search({query: 'request', {gitlab: '[TOKEN]'}}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，您有了一个 HTTP 客户端，它可以同时搜索 GitHub、GitLab 和 BitBucket 中的存储库。它还将结果整齐地打包到数组中，供您的前端应用程序使用。

> 将其封装在 Lambda 中，并部署在云上。
> 这就是你的无服务器！

# 结论

如果我们有不把我们锁在里面的模块呢？如果没有 API，或者没有完全可选和可扩展的 API，那该怎么办？如果我们有工具让我们自己成为作者，创造出最适合我们自己的东西，会怎么样？

[请求-组合](https://github.com/simov/request-compose)背后的想法正是如此，而且它是一个功能齐全的功能(明白吗？)NodeJS 的 HTTP 客户端。或者我应该说:它包含了一个捆绑在其中的固执己见的 HTTP 客户端。它涵盖了你可能遇到的大部分用例，它远不是一个玩具项目，也不是我的第一个 HTTP 客户端。

> 不是说这是最好的一个，但只是让你知道:)

编码快乐！