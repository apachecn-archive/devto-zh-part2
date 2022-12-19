# 将 Redux 引入您的队列逻辑:使用 ES6 和 bull 队列的快速设置

> 原文：<https://dev.to/hugo__df/bring-redux-to-your-queue-logic-an-express-setup-with-es6-and-bull-queue-56d1>

在 web 应用程序的生命周期中，总会有一个时刻，操作最好在后台执行，这就是队列出现的地方。

Node 中有一些排队解决方案。没有一个是可笑的占优，比如 [Kue](https://github.com/Automattic/kue) ， [RSMQ](https://github.com/smrchy/rsmq) ，[蜜蜂队列](https://github.com/bee-queue/bee-queue)，[公牛](https://github.com/OptimalBits/bull)。Kue、RSMQ 和 Bee Queue 的问题在于它使用了一个回调函数作为推荐的 API。

bull[https://github.com/OptimalBits/bull](https://github.com/OptimalBits/bull)是一个高级队列包，用于处理 NodeJS 中的作业和消息。它由 Redis 支持，功能非常丰富。最重要的是，它利用了基于承诺的处理 API，这意味着`async/await`。

我们将通过一个应用程序向一组 URL 发送带有给定有效负载的 webhooks。

你可以在 https://github.com/HugoDF/express-bull-es6 找到完整的代码内容。

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

*   【Redis 和工作人员的快速申请🏃‍♀️
*   [建立公牛🐮](#setting-up-bull)
*   [用 Redis 持久化 webhook 数据](#persisting-webhook-data-with-redis)
    *   [接受有效载荷并在](#accepting-payloads-and-forwarding-them-on)转发
    *   [坚持 webhook 数据要重写💾](#persisting-webhook-data-to-redis)
    *   [使用新的数据库模块保存帖子数据](#saving-post-data-using-the-new-db-module)
*   [排队工作🏭](#queuing-jobs)
*   [加工岗位⚙️](#processing-jobs)
*   在你发货之前还有一些其他的事情要做🚢

## 一个带有 Redis 和一个 worker 的 Express 应用🏃‍♀️

我们将从使用 docker-compose 的 Node/Redis/Express 设置开始(完整的演练可以在
[https://codewithhugo . com/setting-up-Express-and-Redis-with-docker-compose/](https://codewithhugo.com/setting-up-express-and-redis-with-docker-compose/)找到)，
应用程序将使用 ES 模块编写(通过使用 [esm](https://github.com/standard-things/esm) 包)。

首先，我们将使用下面的`docker-compose.yml` :

```
version: '2'
services:
    app:
        build: .
        container_name: my-app
        environment:
            - NODE_ENV=development
            - PORT=3000
            - REDIS_URL=redis://my-cache
        command: "sh  -c  'npm  i  &&  npm  run  dev'"
        volumes:
            - .:/var/www/app
        links:
            - redis
        ports:
            - "3000:3000"

    worker:
        build: .
        container_name: my-worker
        environment:
            - NODE_ENV=development
            - PORT=3000
            - REDIS_URL=redis://my-cache
        command: "sh  -c  'npm  i  &&  npm  run  worker:dev'"
        volumes:
            - .:/var/www/app
        links:
            - redis

    redis:
        image: redis
        container_name: my-cache
        expose:
            - "6379" 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要一个`package.json`，如下所示:

```
{  "name":  "express-bull-es6",  "version":  "1.0.0",  "description":  "An Express setup with Redis, bull and ES6",  "main":  "server.js",  "scripts":  {  "start":  "node -r esm server.js",  "dev":  "nodemon -r esm server.js",  "worker":  "node -r esm worker.js",  "worker:dev":  "nodemon -r esm worker.js"  },  "author":  "Hugo Di Francesco",  "license":  "MIT",  "dependencies":  {  "esm":  "^3.0.67",  "express":  "^4.16.3",  "nodemon":  "^1.18.1"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

答`server.js` :

```
 import express from 'express';

const app = express();

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`)
}); 
```

Enter fullscreen mode Exit fullscreen mode

和一个`worker.js` :

```
console.log('Worker doing nothing'); 
```

Enter fullscreen mode Exit fullscreen mode

在命令行中运行下面的代码应该会得到一些输出(如果需要安装依赖项的话，过一会儿之后):

```
$ docker-compose up 
```

Enter fullscreen mode Exit fullscreen mode

最终:

```
my-worker | [nodemon] 1.18.1
my-worker | [nodemon] to restart at any time, enter `rs`
my-worker | [nodemon] watching: *.*
my-worker | [nodemon] starting `node -r esm worker.js`
my-app    | [nodemon] 1.18.1
my-app    | [nodemon] to restart at any time, enter `rs`
my-app    | [nodemon] watching: *.*
my-app    | [nodemon] starting `node -r esm server.js`
my-worker | Worker doing nothing
my-app    | Server listening on port 3000 
```

Enter fullscreen mode Exit fullscreen mode

## 设置公牛🐮

接下来，我们想添加`bull`来设置一些队列。
我们还将设置`bull-arena`作为 web UI 来监控这些队列。

首先安装`bull`和`bull-arena` :

```
npm i --save bull bull-arena 
```

Enter fullscreen mode Exit fullscreen mode

让我们在一个`queues.js`文件中创建一些队列:

```
import Queue from 'bull';

export const NOTIFY_URL = 'NOTIFY_URL';

export const queues = {
  [NOTIFY_URL]: new Queue(
    NOTIFY_URL,
    process.env.REDIS_URL
  )
}; 
```

Enter fullscreen mode Exit fullscreen mode

并更新`server.js`以包含`bull-arena` UI 和`import`队列`NOTIFY_URL`。

```
import url from 'url';

import express from 'express';
import Arena from 'bull-arena';

import { queues, NOTIFY_URL } from './queues';

const app = express();

function getRedisConfig(redisUrl) {
  const redisConfig = url.parse(redisUrl);
  return {
    host: redisConfig.hostname || 'localhost',
    port: Number(redisConfig.port || 6379),
    database: (redisConfig.pathname || '/0').substr(1) || '0',
    password: redisConfig.auth ? redisConfig.auth.split(':')[1] : undefined
  };
}

app.use('/', Arena(
  {
    queues: [
      {
        name: NOTIFY_URL,
        hostId: 'Worker',
        redis: getRedisConfig(process.env.REDIS_URL)
      }
    ]
  },
  {
    basePath: '/arena',
    disableListen: true
  }
));

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`)
}); 
```

Enter fullscreen mode Exit fullscreen mode

在保存时，我们将能够打开[http://localhost:3000/arena](http://localhost:3000/arena)并看到以下内容:

[![Screenshot of Arena in browser](../Images/3eccbc1df2b1a0ea503ad16827e71de1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--583poWHr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codewithhugo.com/img/20180721-arena-screenshot.jpg)

## 用 Redis 持久化 webhook 数据

### 接受有效载荷并转发它们

我们的 API 的形状如下:
一个`POST /webhooks`端点，它将接受一个带有`payload`和`urls`数组的 JSON POST 主体，它将响应下面的请求:

```
curl -X POST \
  http://localhost:3000/webhooks \
  -H 'Content-Type: application/json' \
  -d '{
    "payload": {
        "hello": "world"
    },
    "urls": [
        "http://localhost:3000/example",
        "http://localhost:3000/example"
    ]
}' 
```

Enter fullscreen mode Exit fullscreen mode

一个`POST /webhooks/notify`端点，它将接受一个带有`id`字段的 JSON POST 主体，该主体将响应如下请求:

```
curl -X POST \
  http://localhost:3000/webhooks/notify \
  -H 'Content-Type: application/json' \
  -d '{
    "id": "e5d9f99f-9641-4c0a-b2ca-3b0036c2a9b3"
}' 
```

Enter fullscreen mode Exit fullscreen mode

我们还将有一个`POST /example`端点来检查我们的 webhooks 是否真的被触发了。

这意味着我们需要`body-parser` :

```
npm install --save body-parser 
```

Enter fullscreen mode Exit fullscreen mode

`server.js`将如下所示:

```
import url from 'url';
import express from 'express';
import bodyParser from 'body-parser';
import Arena from 'bull-arena';

import { queues, NOTIFY_URL } from './queues';

const app = express();

app.use(bodyParser.json());

app.post('/webhooks', (req, res, next) => {
  const { payload, urls } = req.body;
  res.json({
    payload,
    urls
  });
});

app.post('/webhooks/notify', async (req, res, next) => {
  const { id } = req.body;
  res.sendStatus(200);
});

app.post('/example', (req, res) => {
  console.log(`Hit example with ${JSON.stringify(req.body)}`);
  return res.sendStatus(200);
});

function getRedisConfig(redisUrl) {
  const redisConfig = url.parse(redisUrl);
  return {
    host: redisConfig.hostname || 'localhost',
    port: Number(redisConfig.port || 6379),
    database: (redisConfig.pathname || '/0').substr(1) || '0',
    password: redisConfig.auth ? redisConfig.auth.split(':')[1] : undefined
  };
}

app.use('/', Arena(
  {
    queues: [
      {
        name: NOTIFY_URL,
        hostId: 'Worker',
        redis: getRedisConfig(process.env.REDIS_URL)
      }
    ]
  },
  {
    basePath: '/arena',
    disableListen: true
  }
));

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`)
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 坚持 web 手册数据要重写💾

将选择`ioredis`(节点的 Redis 客户端)来利用`bull`在幕后使用`ioredis`:

```
npm install --save ioredis 
```

Enter fullscreen mode Exit fullscreen mode

为了生成唯一的标识符，我们还将安装`uuid`包:

```
npm install --save uuid 
```

Enter fullscreen mode Exit fullscreen mode

一个新的模块，`db.js`看起来如下:

```
import Redis from 'ioredis';
import { v4 as uuidV4 } from 'uuid';

const redis = new Redis(process.env.REDIS_URL);

const WEBHOOK_PREFIX = 'webhook:';
const PAYLOAD_PREFIX = `${WEBHOOK_PREFIX}payload:`;
const URLS_PREFIX = `${WEBHOOK_PREFIX}urls:`;

const makePayloadKey = id => `${PAYLOAD_PREFIX}${id}`;
const makeUrlsKey = id => `${URLS_PREFIX}${id}`;

async function setWebhook(payload, urls) {
  const id = uuidV4();
  const transaction = redis.multi()
    .hmset(makePayloadKey(id), payload)
    .lpush(makeUrlsKey(id), urls)
  await transaction.exec();
  return id;
}

async function getWebhook(id) {
  const transaction = redis.multi()
    .hgetall(makePayloadKey(id))
    .lrange(makeUrlsKey(id), 0, -1);
  const [[_, payload], [__, urls]] = await transaction.exec();
  return {
    payload,
    urls
  };
}

export const db = {
  setWebhook,
  getWebhook
}; 
```

Enter fullscreen mode Exit fullscreen mode

有效载荷和 URL 分别被建模为`webhook:payload:<some-uuid>`和`webhook:urls:<some-uuid>`。

有效负载是 Redis 散列(因为有效负载是 JSON 对象)，URL 是 Redis 列表(因为我们处理的是字符串列表)。

我们遇到了一个问题，我们希望确保同时设置/获取`payload`和`urls`，因此使用了`multi()`。

允许我们构建事务(应该自动执行的操作)。
以这种规模(没有交通😄)，考虑到我们只添加(从不更新)并且我们使用 UUIDs，我们也可以有*而不是*用过的事务，
，但是我们会是优秀的工程师，无论如何都会继续使用它们。

更涉及的台词:

```
const transaction = redis.multi()
  .hgetall(makePayloadKey(id))
  .lrange(makeUrlsKey(id), 0, -1);
const [[_, payload], [__, urls]] = await transaction.exec(); 
```

Enter fullscreen mode Exit fullscreen mode

需要一个解释:

1.  `hgetall`获取散列中的所有键值对，
2.  `lrange`获取列表的值，当使用`1`作为开始，`-1`作为结束时，获取整个列表
3.  `const output = await multi().op1().op2().exec()`

    *   将输出设置为来自`op1`、`op2`的返回值数组
    *   换句话说`output = [ [ errorOp1, replyOp1 ], [ errorOp2, replyOp2 ] ]`
    *   为了反映这一点，我们忽略错误(这不是一个好的做法),只得到回复
    *   更好的解决方案是:

    ```
    const [[errPayload, payload], [errUrls, urls]] = await transaction.exec();
    if (errPayload) {
      throw errPayload;
    }
    if (errUrls) {
      throw errUrls
    } 
    ```

### 使用新的数据库模块保存发布数据

在`server.js`中现在看起来如下:

```
import url from 'url';
import express from 'express';
import bodyParser from 'body-parser';
import Arena from 'bull-arena';

import { db } from './db';
import { queues, NOTIFY_URL } from './queues';

const app = express();

app.use(bodyParser.json());

app.post('/webhooks', async (req, res, next) => {
  const { payload, urls } = req.body;
  try {
    const id = await db.setWebhook(payload, urls);
    return res.json({
      id
    });
  } catch (error) {
    next(error);
  }
});

app.post('/webhooks/notify', async (req, res, next) => {
  const { id } = req.body;
  try {
    const { payload, urls } = await db.getWebhook(id);
    return res.sendStatus(200);
  } catch (error) {
    next(error);
  }
});

app.post('/example', (req, res) => {
  console.log(`Hit example with ${JSON.stringify(req.body)}`);
  return res.sendStatus(200);
});

function getRedisConfig(redisUrl) {
  const redisConfig = url.parse(redisUrl);
  return {
    host: redisConfig.hostname || 'localhost',
    port: Number(redisConfig.port || 6379),
    database: (redisConfig.pathname || '/0').substr(1) || '0',
    password: redisConfig.auth ? redisConfig.auth.split(':')[1] : undefined
  };
}

app.use('/', Arena(
  {
    queues: [
      {
        name: NOTIFY_URL,
        hostId: 'Worker',
        redis: getRedisConfig(process.env.REDIS_URL)
      }
    ]
  },
  {
    basePath: '/arena',
    disableListen: true
  }
));

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`)
}); 
```

Enter fullscreen mode Exit fullscreen mode

主要更新有:

```
app.post('/webhooks', async (req, res, next) => {
  const { payload, urls } = req.body;
  try {
    const id = await db.setWebhook(payload, urls);
    return res.json({
      id
    });
  } catch (error) {
    next(error);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

还有:

```
app.post('/webhooks/notify', async (req, res, next) => {
  const { id } = req.body;
  try {
    const { payload, urls } = await db.getWebhook(id);
    return res.sendStatus(200);
  } catch (error) {
    next(error);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到,`POST /webhooks/notify`处理程序实际上仍然没有通知任何事情或任何人🙈。

## 排队作业🏭

为了对作业进行排队，我们使用了`queue.add`方法，并将我们希望出现在`job.data` :
中的内容传递给它

```
queues[NOTIFY_URL].add({
  payload,
  url,
  id
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们想要向每个 URL 独立发送一个请求(这是整个队列设置的要点)，这意味着我们想要:

```
app.post('/webhooks/notify', async (req, res, next) => {
  const { id } = req.body;
  try {
    const { payload, urls } = await db.getWebhook(id);
    urls.forEach(url => {
      queues[NOTIFY_URL].add({
        payload,
        url,
        id
      });
    });
    return res.sendStatus(200);
  } catch (error) {
    next(error);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

其中值得注意的变化是:

```
urls.forEach(url => {
  queues[NOTIFY_URL].add({
    payload,
    url,
    id
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经完成了，如果我们创建一个新的 webhook:

```
curl -X POST \
  http://localhost:3000/webhooks \
  -H 'Content-Type: application/json' \
  -d '{
        "payload": {
                "hello": "world"
        },
        "urls": [
                "http://localhost:3000/example",
                "http://localhost:3000/example"
        ]
}'
{"id":"5fc395bf-ca2f-4654-a7ac-52f6890d0deb"} 
```

Enter fullscreen mode Exit fullscreen mode

`{"id":"5fc395bf-ca2f-4654-a7ac-52f6890d0deb"}`确保将 id 复制到以下命令中:

```
curl -X POST \
  http://localhost:3000/webhooks/notify \
  -H 'Content-Type: application/json' \
  -d '{
    "id": "5fc395bf-ca2f-4654-a7ac-52f6890d0deb"
}'
OK 
```

Enter fullscreen mode Exit fullscreen mode

作业已经被添加到队列中，我们可以通过打开[的`bull-arena` UI 来检查:](http://localhost:3000/arena/Worker/NOTIFY_URL/waiting)

[![Screenshot of waiting jobs on the NOTIFY_URL queue](../Images/2ad65152860ce8bde9ded3c4da0512b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sIyIb6jO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codewithhugo.com/img/20180721-waiting-jobs.jpg)

通过单击其中一个`__default__`作业，我们可以看到有效负载、URL 和 id 被正确地传入:

[![Data content of job in queue](../Images/9fbfa676d35b9553a1dd6a7ce0873a6f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CSNZxjYF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codewithhugo.com/img/20180721-job-payload.jpg)

## 处理岗位⚙️

我们现在想要实际处理排队的作业，即 ping 一些带有数据的 URL。

为此，我们引入`axios`作为 HTTP 客户端:

```
npm install --save axios 
```

Enter fullscreen mode Exit fullscreen mode

创建一个`processors.js`文件:

```
import { NOTIFY_URL } from './queues';
import axios from 'axios';

export const processorInitialisers = {
  [NOTIFY_URL]: db => job => {
    console.log(`Posting to ${job.data.url}`);
    return axios.post(job.data.url, job.data.payload);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于某些上下文，我们使用`db => job => Promise`类型签名的原因是
,尽管我们目前不需要 DB，以说明我如何将数据库或任何其他依赖项传递给 processorInitialiser。

其他一些处理器初始化器可能如下:

```
const myOtherProcessorInitialiser = db => async job => {
  const webhook = await db.getWebhook(job.data.id);
  return Promise.all(
    webhook.urls.map(
      url => axios.post(url, webhook.payload)
    )
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要将处理器连接到队列，这是使用`queue.process`完成的，所以在`worker.js`中，我们现在将有:

```
import { queues } from './queues';
import { processorInitialisers } from './processors';
import { db } from './db';

Object.entries(queues).forEach(([queueName, queue]) => {
  console.log(`Worker listening to '${queueName}' queue`);
  queue.process(processorInitialisers[queueName](db));
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过创建一个指向`http://localhost:3000/example`的 webhooks 来测试它的工作，使用`/webhook/notify`触发它并检查日志，类似于:

```
my-worker | Posting to http://localhost:3000/example
my-app    | Hit example with {"hello":"world"}
my-worker | Posting to http://localhost:3000/example
my-app    | Hit example with {"hello":"world"} 
```

Enter fullscreen mode Exit fullscreen mode

## 在你发货之前还有一些其他的事情要做🚢

我们真的应该*而不是*向公众公开`bull-arena` UI，所以如果你打算在托管环境中使用这个设置，要么做:

```
if (process.env.NODE_ENV !== 'product') {
  // Bull arena logic
} 
```

Enter fullscreen mode Exit fullscreen mode

或者使用某种中间件向其添加 HTTP 基本身份验证。

你可以阅读一篇关于使用 Docker Compose、Redis 和 Node/Express 的更深入的文章:[https://codewithhugo . com/setting-up-Express-and-Redis-with-Docker-Compose/](https://codewithhugo.com/setting-up-express-and-redis-with-docker-compose/)。

关于使用 [esm](https://github.com/standard-things/esm) 的更多信息，请参见:[https://codewithhugo . com/es6-by-example-a-module/CLI-to-wait-for-postgres-in-docker-compose/](https://codewithhugo.com/es6-by-example-a-module/cli-to-wait-for-postgres-in-docker-compose/)。

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

[迈克尔·帕苏斯基](https://unsplash.com/@mparzuchowski?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)