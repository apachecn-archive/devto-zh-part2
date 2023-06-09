# 用 Docker Compose 设置 Express 和 Redis

> 原文：<https://dev.to/hugo__df/setting-up-express-and-redis-with-docker-compose-aaa>

Redis 和 Express 是为他们的问题域提供一种简单而清晰的方法的工具。

可在以下位置找到 rest:https://github . com/hugodf/express-redis 停靠站。

[Redis](https://redis.io/) 是“一个开源的、内存中的数据结构存储，用作数据库、缓存和消息代理”。它就像数据库一样简单，它以其作为键值存储的性能和简单性而闻名。它具有很大的灵活性，还可以用作消息队列、循环缓冲区(以及开发人员除了关系数据库之外可以想到的任何东西)。

Express 是一个“快速的、非个性化的、极简的 Node.js 网络框架”。换句话说，它是节点服务器的包装器，提供了一种编写所谓的“中间件”的方式，以跨 HTTP 端点共享和组合功能，并定义所述端点。

让 Redis 和 Express 一起工作是微不足道的。让 Redis 和 Express 以一种简单的、面向未来的、可跨本地和部署环境复制的方式协同工作，要稍微困难一些。这就是 Docker 和 Docker Compose 的用武之地。

Docker 是一个容器化系统，Docker Compose 是一种定义多个 Docker 容器如何交互的方法。在 Node web 应用程序开发的上下文中，Docker 倾向于用来定义一个具有所需系统级依赖关系的容器(例如，节点版本、任何额外的数据库驱动程序)。Docker Compose 将用于定义节点应用程序之外的依赖关系，例如数据库。

*   [缩写快递🚅](https://codewithhugo.com/setting-up-express-and-redis-with-docker-compose#initialising-express)
*   [Docker 内部的运行节点🌊](https://codewithhugo.com/setting-up-express-and-redis-with-docker-compose#running-node-inside-of-docker)
*   [增加歌词](https://codewithhugo.com/setting-up-express-and-redis-with-docker-compose#adding-redis)
*   [创建 blob 存储📒](https://codewithhugo.com/setting-up-express-and-redis-with-docker-compose/#creating-a-blob-store)

[订阅](https://buttondown.email/hugo)在你的收件箱里获得最新的帖子(比任何人都要早)。

# 初始化快递🚅

首先，我们应该创建一个新目录/初始化 npm:

```
mkdir express-redis
cd express-redis
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以安装 Express:

```
npm i --save express 
```

Enter fullscreen mode Exit fullscreen mode

并创建一个类似如下的`server.js`文件:

```
// server.js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    return res.send('Hello world');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server listening on port ${PORT}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

可以使用
运行

```
node server.js 
```

Enter fullscreen mode Exit fullscreen mode

我们可以检查它是否按预期工作

```
curl http://localhost:3000/
Hello world 
```

Enter fullscreen mode Exit fullscreen mode

# Docker 内部运行节点🌊

首先，你需要安装 [Docker 社区版](https://www.docker.com/community-edition)([https://www.docker.com/community-edition](https://www.docker.com/community-edition))。
然后我们可以加一个`Dockerfile`和一个`docker-compose.yml` :

```
# Dockerfile
FROM node:9-alpine
# Or whatever Node version/image you want
WORKDIR '/var/www/app' 
```

Enter fullscreen mode Exit fullscreen mode

```
# docker-compose.yml
app:
    build: ./
    volumes:
    - ./:/var/www/app
    ports:
    - 3000:3000
    environment:
    - NODE_ENV=development
    - PORT=3000
    command:
    sh -c 'npm i && node server.js' 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 Docker/Docker Compose:
中运行应用程序

```
docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

并检查它是否还能工作

```
curl http://localhost:3000/
Hello world 
```

Enter fullscreen mode Exit fullscreen mode

一些额外的背景:

*   `Dockerfile`定义了应用程序将在哪个容器中运行(这里是构建在 alpine 之上的 Node 9 容器)
*   `docker-compose.yml`:
    *   `build`解释了哪个映像应该被`app`服务定义使用(在本例中，它指向运行`Dockerfile`将会创建的内容)
    *   `volumes`定义什么应该挂载在哪里(在这种情况下，我们将整个目录挂载为`/var/www/app`
    *   `ports`将主机系统的端口映射到容器内部的端口
    *   `environment`为容器设置环境变量
    *   `command`决定了容器启动时将运行什么，这里它运行`npm install`,后跟服务器启动命令

# 增删台词

要将 Redis 添加到我们的 Express 应用程序中，我们应该使用`redis`包:

```
npm install --save redis 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可能应该包装所有我们想要使用的基于回调的方法(参见 redis 包的 api 文档，[https://github.com/NodeRedis/node_redis#api](https://github.com/NodeRedis/node_redis#api))。
让我们使用`redis-client.js`模块:
来做这件事

```
// redis-client.js
const redis = require('redis');
const {promisify} = require('util');
const client = redis.createClient(process.env.REDIS_URL);

module.exports = {
  ...client,
  getAsync: promisify(client.get).bind(client),
  setAsync: promisify(client.set).bind(client),
  keysAsync: promisify(client.keys).bind(client)
}; 
```

Enter fullscreen mode Exit fullscreen mode

在 Docker Compose 中运行 Redis，使我们的应用程序可以访问它:

```
# docker-compose.yml
# Add this top-level entry
redis:
    image: redis
    container_name: cache
    expose:
    - 6379

app:
    # some definitions
    links:
    - redis
    environment:
    - REDIS_URL=redis://cache
    # rest of the environment definitions 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以从应用程序容器:
访问 Redis 客户端

```
docker-compose run app node
> require('./redis-client') // doesn't crash 
```

Enter fullscreen mode Exit fullscreen mode

# 创建 blob 存储📒

我们现在可以创建一个 HTTP API，它将允许我们使用查询参数存储数据，并使用 get 请求检索它们(这一点也不 RESTful，但是很好🙂 ).

```
// server.js
// imports and app definition

const redisClient = require('./redis-client');
app.get('/store/:key', async (req, res) => {
    const { key } = req.params;
    const value = req.query;
    await redisClient.setAsync(key, JSON.stringify(value));
    return res.send('Success');
});
app.get('/:key', async (req, res) => {
    const { key } = req.params;
    const rawData = await redisClient.getAsync(key);
    return res.json(JSON.parse(rawData));
});

// code that starts the app... 
```

Enter fullscreen mode Exit fullscreen mode

如果你对以上代码有任何疑问，请告诉我 [@hugo__df](https://twitter.com/hugo__df) 。它使用了一些更高级的特性，比如 async/await 和 destructuring，以及 Express 特性来获取查询和路径参数(参见 https://expressjs.com/en/api.html#req.query、[https://expressjs.com/en/api.html#req.params](https://expressjs.com/en/api.html#req.params))。

让应用程序再次运行:`docker-compose up`

1.  存储一些数据

```
curl http://localhost:3000/store/my-key\?some\=value\&some-other\=other-value
Success 
```

Enter fullscreen mode Exit fullscreen mode

1.  检索数据:

```
curl http://localhost:3000/my-key
{
    "some": "value",
    "some-other": "other-value"
} 
```

Enter fullscreen mode Exit fullscreen mode

> 更高级的 Express 用户和 API 设计大师请注意。
> 我使用老式的基于查询参数的系统的原因是为了避免在 POST 请求中使用`body-parser`的样板文件

完整储存库可用于:https://github . com/hugodf/express-redis 停靠点

[订阅](https://buttondown.email/hugo)在你的收件箱里获得最新的帖子(比任何人都要早)。
本·库伦格韦尔在 [Unsplash](https://unsplash.com/search/photos/boats?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的封面照片