# 使用 SuperTest、moxios 和 Jest 测试 Express 应用程序

> 原文：<https://dev.to/hugo__df/testing-an-express-app-with-supertest-moxios-and-jest-4f44>

测试是软件开发过程中至关重要的部分。它有助于捕捉错误，避免回归，并记录软件的行为。

Express 是用 JavaScript 构建后端应用程序最广泛的库之一。
下面是如何为这样的应用程序建立有效的单元测试策略的总结，以及
在尝试测试时你可能会面临的几种情况。

完整的代码示例可以在[https://github.com/HugoDF/express-supertest-moxios](https://github.com/HugoDF/express-supertest-moxios)找到。

*   [一款简单的快递应用🎁](https://codewithhugo.com/testing-an-express-app-with-supertest-moxios-and-jest#a-simple-express-app)
    *   [测试策略🕵️‍](https://codewithhugo.com/testing-an-express-app-with-supertest-moxios-and-jest#testing-strategy)
*   [测试不太简单的快递应用程序📚](https://codewithhugo.com/testing-an-express-app-with-supertest-moxios-and-jest#testing-a-less-simple-express-app)
    *   [测试策略🛠](https://codewithhugo.com/testing-an-express-app-with-supertest-moxios-and-jest#testing-strategy)
    *   Redis 被嘲笑的地方🤡
*   [离别的思念🦋](https://codewithhugo.com/testing-an-express-app-with-supertest-moxios-and-jest#parting-thoughts)

这是周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

# 一个简单的快递 app🎁

假设我们有一组如下所示的快速路由处理程序:

`hugo.js` :

```
const {Router} = require('express');
const axios = require('axios');
module.exports = (router = new Router()) => {
  router.get('/hugo', async (req, res) => {
    const { data: userData } = await axios.get(
      'https://api.github.com/users/HugoDF'
    );
    const {
      blog,
      location,
      bio,
      public_repos,
    } = userData
    return res.json({
      blog,
      location,
      bio,
      publicRepos: public_repos,
    });
  });
  return router;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这将消耗在一个主要的`server.js`像这样:

```
const express = require('express');
const app = express();
const hugo = require('./hugo');

app.use(hugo());

app.listen(3000, () => {
  console.log(`Server listening on port 3000`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

要运行该程序，需要以下依赖关系:

```
npm i --save express axios 
```

Enter fullscreen mode Exit fullscreen mode

它可以使用
运行

```
node server.js 
```

Enter fullscreen mode Exit fullscreen mode

点击`/hugo`将返回一些从我的 GitHub 配置文件中提取的 JSON 数据:

```
curl http://localhost:3000/hugo
{"blog":"https://codewithhugo.com","location":"London","bio":"Developer, JavaScript.","publicRepos":39} 
```

Enter fullscreen mode Exit fullscreen mode

## 试探策略🕵️‍

测试是关于定义一些输入和断言输出。

现在，如果我们跳过关于测试单元是什么的讨论，我们真正关心的是这个 API 的
当我们点击`/hugo`时，我们得到正确的响应，使用`jest`这里是一个测试套件可能看起来像什么:

`hugo.test.js`

```
const hugo = require('./hugo');
const express = require('express');
const moxios = require('moxios');
const request = require('supertest');

const initHugo = () => {
  const app = express();
  app.use(hugo());
  return app;
}

describe('GET /hugo', () => {
  beforeEach(() => {
    moxios.install();
  });
  afterEach(() => {
    moxios.uninstall();
  });
  test('It should fetch HugoDF from GitHub', async () => {
    moxios.stubRequest(/api.github.com\/users/, {
      status: 200,
      response: {
        blog: 'https://codewithhugo.com',
        location: 'London',
        bio: 'Developer, JavaScript',
        public_repos: 39,
      }
    });
    const app = initHugo();
    await request(app).get('/hugo');
    expect(moxios.requests.mostRecent().url).toBe('https://api.github.com/users/HugoDF');
  });
  test('It should 200 and return a transformed version of GitHub response', async () => {
    moxios.stubRequest(/api.github.com\/users/, {
      status: 200,
      response: {
        blog: 'https://codewithhugo.com',
        location: 'London',
        bio: 'Developer, JavaScript',
        public_repos: 39,
      }
    });
    const app = initHugo();
    const res = await request(app).get('/hugo');
    expect(res.body).toEqual({
      blog: 'https://codewithhugo.com',
        location: 'London',
        bio: 'Developer, JavaScript',
        publicRepos: 39,
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

要运行上面的，首先，添加所需的依赖项:

```
npm install --save-dev jest moxios supertest 
```

Enter fullscreen mode Exit fullscreen mode

使用
运行测试

```
npx jest 
```

Enter fullscreen mode Exit fullscreen mode

我们正在利用[超级测试](https://github.com/visionmedia/supertest)，并将 express 应用程序传递给它。
SuperTest 类似 fetch 的 API 是熟悉的，并且是`await`可以使用的。

[moxios](https://github.com/axios/moxios) 是一个“模拟 axios 测试请求”的包。
我们能够在观察模式下运行我们的单元测试，而不会淹没上游的 REST API。
`moxios`需要安装和卸载，我们分别在每次测试前后做这个。
这是为了避免一种边缘情况，即如果在`moxios.uninstall`被调用之前
发生错误，由于 moxios 没有被拆除和重新正确设置，一个失败的测试可能会导致其他测试失败。

应该向`stubRequest`方法传递两个参数:

*   第一个是什么将被拦截，这可以是一个字符串(这将需要一个完整的网址)，或一个正则表达式。
*   第二个参数是一个响应配置对象，我们使用的主键是`status`和`response`。状态将是`axios`获取响应中的状态，而`response`将是`axios`获取响应中的`data`。

# 测试一个不太简单的快递 app📚

假设我们有一个应用程序，它是一个 blob store，由 Redis(一个简单的键值存储)支持:

`blob-store.js` :

```
const {Router} = require('router');

module.exports = (redisClient, router = new Router()) => {
  router.get('/store/:key', async (req, res) => {
    const { key } = req.params;
    const value = req.query;
    await redisClient.setAsync(key, JSON.stringify(value));
    return res.send('Success');
  });
  router.get('/:key', async (req, res) => {
    const { key } = req.params;
    const rawData = await redisClient.getAsync(key);
    return res.json(JSON.parse(rawData));
  });
  return router;
}; 
```

Enter fullscreen mode Exit fullscreen mode

`server.js` :

```
const express = require('express');
const app = express();

// For the sake of simplicity, 
// redisClient isn't in another module
const redis = require('redis');
const {promisify} = require('util');
const client = redis.createClient(process.env.REDIS_URL);

const redisClient = {
  getAsync: promisify(client.get).bind(client),
  setAsync: promisify(client.set).bind(client)
};

const hugo = require('./hugo');
const blobStore = require('./blob-store');

app.use(hugo());
app.use(blobStore(redisClient));

app.listen(3000, () => {
  console.log(`Server listening on port 3000`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

对于上述示例的演练，请参见[使用 Docker compose](https://dev.to/setting-up-express-and-redis-with-docker-compose#creating-a-blob-store) 设置 Express 和 Redis。

要启动并运行它:

*   克隆人[https://github.com/HugoDF/express-supertest-moxios](https://github.com/HugoDF/express-supertest-moxios)
*   运行 Docker 社区版
*   运行`docker-compose up`

应用程序运行后，我们可以执行以下操作:

1.  存储一些数据:

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

## 试探策略🛠

我们现在要做一个决定:

1.  摩克·里兹
2.  别再嘲笑我了

不模仿 Redis 意味着运行一个完整的 Redis 实例，并在每个测试套件之前设置一些测试数据。这意味着你依赖于某种测试排序，如果不运行多个 Redis 实例来避免数据问题，你就无法并行化。

对于单元(ish)测试，我们希望在开发过程中一直运行，这是一个问题。
另一个选择是嘲讽 Redis，具体来说就是`redisClient`。

## 雷迪斯被嘲笑的地方🤡

`blob-store.test.js`

```
const blobStore = require('./blob-store');
const express = require('express');
const moxios = require('moxios');
const request = require('supertest');

const initBlobStore = (
  mockRedisClient = {
    getAsync: jest.fn(() => Promise.resolve()),
    setAsync: jest.fn(() => Promise.resolve())
  }
) => {
  const app = express();
  app.use(blobStore(mockRedisClient));
  return app;
}

describe('GET /store/:key with params', () => {
  test('It should call redisClient.setAsync with key route parameter as key and stringified params as value', async () => {
    const mockRedisClient = {
      setAsync: jest.fn(() => Promise.resolve())
    };
    const app = initBlobStore(mockRedisClient);
    await request(app).get('/store/my-key?hello=world&foo=bar');
    expect(mockRedisClient.setAsync).toHaveBeenCalledWith(
      'my-key',
      '{\"hello\":\"world\",\"foo\":\"bar\"}'
    );
  });
});

describe('GET /:key', () => {
  test('It should call redisClient.getAsync with key route parameter as key', async () => {
    const mockRedisClient = {
      getAsync: jest.fn(() => Promise.resolve('{}'))
    };
    const app = initBlobStore(mockRedisClient);
    await request(app).get('/my-key');
    expect(mockRedisClient.setAsync).toHaveBeenCalledWith(
      'my-key',
    );
  });
  test('It should return output of redisClient.getAsync with key route parameter as key', async () => {
    const mockRedisClient = {
      getAsync: jest.fn(() => Promise.resolve('{}'))
    };
    const app = initBlobStore(mockRedisClient);
    const response = await request(app).get('/my-key');
    expect(response.body).toEqual({});
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，我们设置了我们的测试，因此我们可以传递一个任意的`redisClient`对象，在那里我们可以模拟方法本身。

# 离别的思念🦋

测试一个 Express 应用程序就是要找到嘲讽开始和结束的界限。

这是 API 设计中的一个练习，如何在尽可能大的单元中测试东西(例如，整个端点)，
而不必携带整个数据库/持久层的行李。

例如，Redis 客户端测试的另一种方法是创建一个模拟客户端，以某种方式维护
状态(即在内部写入一个对象)，并注入/检查该状态(分别在测试代码之前和之后)。

完整的代码示例见[https://github.com/HugoDF/express-supertest-moxios](https://github.com/HugoDF/express-supertest-moxios)。

这是周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

封面图片[不旋转](https://unsplash.com/photos/eofm5R5f9Kw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[不飞溅](https://unsplash.com/search/photos/clowns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)