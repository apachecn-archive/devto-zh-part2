# 使用节点获取 HTTP 请求正文数据

> 原文：<https://dev.to/flaviocopes/get-http-request-body-data-using-node-2i5p>

下面是如何在请求体中提取作为 JSON 发送的数据。

如果您使用的是 Express，那很简单:使用`body-parser`节点模块。

例如，获取这个请求的主体:

```
const axios = require('axios')

axios.post('https://flaviocopes.com/todos', {
  todo: 'Buy the milk'
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是匹配的服务器端代码:

```
const bodyParser = require('body-parser')

app.use(bodyParser.urlencoded({
  extended: true
}))

app.use(bodyParser.json())

app.post('/endpoint', (req, res) => {
  console.log(request.body.todo)
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你没有使用 Express，而你想在 vanilla Node 中实现，当然，你需要做更多的工作，因为 Express 为你抽象了很多。

需要理解的关键是，当您使用`http.createServer()`初始化 HTTP 服务器时，回调是在服务器获得所有 HTTP 头而不是请求体时调用的。

连接回调中传递的`request`对象是一个[流](https://flaviocopes.com/nodejs-streams/)。

因此，我们必须监听要处理的正文内容，而且是成块处理的。

我们首先通过监听流`data`事件获取数据，当数据结束时，调用流`end`事件，一次:

```
const server = http.createServer((req, res) => {
  // we can access HTTP headers
  req.on('data', chunk => {
    console.log(`Data chunk available: ${chunk}`)
  });
  req.on('end', () => {
    //end of data
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

所以要访问数据，假设我们期望接收一个字符串，我们必须把它放入一个数组:

```
const server = http.createServer((req, res) => {
  let data = []
  req.on('data', chunk => {
    data.push(chunk)
  });
  req.on('end', () => {
    JSON.parse(data).todo // 'Buy the milk'
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode