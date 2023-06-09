# 重新加载不带 nodemon 的 express 服务器

> 原文：<https://dev.to/akshendra/reloading-the-express-server-without-nodemon-ak5>

自从我开始编写后端 NodeJS 代码以来，我一直使用`nodemon`来重新加载 express server 和任何其他 NodeJs 代码。它确实像标签上说的那样，而且做得很好。然而，`nodemon`的问题是缺乏控制，而且它似乎扼杀了进程。你写一个`console.log`语句，它将重启你的整个服务器，如果你的服务器启动得很快，这一切都很好。但是当重启服务器意味着重新连接到大量外部服务时，情况就变得令人沮丧了。

### 代码解释，我在说什么

我们从一个非常简单的项目开始，其目录结构如下

```
.
├── boot.js
├── index.js
├── package.json
├── routes
│   └── index.js
└── server.js 
```

Enter fullscreen mode Exit fullscreen mode

`index.js`是主脚本。我们在这里调用`boot()`来连接外部服务。一旦我们连接上，我们启动`server()`并在端口`3000`监听。

```
const boot = require('./boot');
const server = require('./server');
const debug = require('debug')('app');

boot()
  .then(() => {
    const app = server();
    app.listen(3000, () => {
      debug('Started on 3000');
    });
  })
  .catch((err) => {
    debug(err);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

`boot.js`连接到外部服务，可以是数据库或队列。为了模拟这种情况，我使用了一个将在 10 秒内解决的承诺。

```
const debug = require('debug')('app');

module.exports = function boot() {
  debug('Connecting to the satellites...');
  return new Promise((resolve) => {
    setTimeout(() => {
      debug('Connected to satellites...');
      resolve();
    }, 10000);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

`server.js`创建一个`express`应用程序，添加所有需要的中间件，然后简单地返回应用程序。

```
const express = require('express');

const routes = require('./routes');

module.exports = function () {
  const app = express();

  app.get('/', (req, res) => {
    res.send('Nothing here...');
  });

  app.use('/', routes);

  return app;
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在是`route/index.js`，一条简单的路线，看起来很傻。

```
const express = require('express');

const router = new express.Router();

router.get('/silly', (req, res) => {
  res.send('Now, I’ve noticed a tendency for this programme to get rather silly');
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 我喜欢你的代码，但是现在呢？

嗯，要启动服务器，我们可以使用`nodemon index.js`。

[![Frustation](img/afc58c5b27317685ef03b553c17119e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YK3QbdUh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A8el_nMWQac1GXywI0kapow.gif)

显而易见，每次对代码进行任何更改(由 nodemon 记录)时，应用程序都会连接到外部服务(卫星)，这需要 10 秒以上重启服务器所需的额外时间。

### 现解

要构建在代码更改时可以重启服务器的东西，我们需要一种监听文件更改的方法。NodeJS `fs`模块确实提供了监视文件的功能，但是还有更好的功能， [`chokidar`](https://github.com/paulmillr/chokidar) 。

使用`chokidar`，我们将监听`routes/index.js`的任何变化。

```
chokidar.watch('./routes').on('all', (event, at) => {
  if (event === 'add') {
    debug('Watching for', at);
  }
  if (event === 'change') {
    debug('Changes at', at);
    restart(); // assume that this exists
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

很简单。现在我们需要想出在重启的情况下该做什么。我想到的第一件事是一个快速服务器的方法。如`index.js`所示，我们正在`3000`港启动一个快递 app。我们肯定不能再在`3000`港启动快递 app 了。我们需要先停止此应用程序。

从`express`的文档来看，`app.listen`基本就是这么做的

```
app.listen = function() {
  var server = http.createServer(this);
  return server.listen.apply(server, arguments);
}; 
```

Enter fullscreen mode Exit fullscreen mode

所以，`http.Server`是我们需要停止的。承蒙上帝的恩典，我们有一个`close`方法。让我们看看文件，

> 停止服务器接受新连接并保留现有连接。
> 该函数是异步的，当所有连接结束时，服务器最终关闭，服务器发出一个“ [close](https://nodejs.org/api/net.html#net_event_close) 事件。

哦，所以在我们试图关闭服务器之前，所有的连接都需要被关闭。好吧，我们需要一种方法来监控所有的连接并在需要时手动破坏它们。我们将使用`server.on('connection')`来访问所有的连接。

现在我们有一点点状态要维护，我们将使用这个非常简单的对象，

```
const state = {
  server: null,
  sockets: [],
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们会这样启动服务器(记住`server.js`会返回`express()` app)。

```
function start() {
  state.server = require('./server')().listen(3000, () => {
    debug('Started on 3000');
  });
  state.server.on('connection', (socket) => {
    debug('Add socket', state.sockets.length + 1);
    state.sockets.push(socket);
  });
}
At the end will destroy all the sockets.
state.sockets.forEach((socket, index) => {
  debug('Destroying socket', index + 1);
  if (socket.destroyed === false) {
    socket.destroy();
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

*另行通知之前，函数`start`* 里面的`require('./server')`

### 这样做是为了避免需要缓存

我们还需要注意`require` (CommonJS)缓存。作为一种优化，`require`在模块级缓存你的代码。一旦遇到`require`，它将编译文件中的代码，并将结果放入缓存中。下次遇到相同的`require`时，它将使用保存在缓存中的结果。

这打破了我们所有的计划。因为更改后的代码将不再被加载。我们应该使缓存无效，这基本上就像删除缓存的结果一样简单。

```
function pathCheck(id) {
  return (
    id.startsWith(path.join(__dirname, 'routes')) ||
    id.startsWith(path.join(__dirname, 'server.js'))
  );
}

Object.keys(require.cache).forEach((id) => {
  if (pathCheck(id)) { // delete selectively
    debug('Reloading', id);
    delete require.cache[id];
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

基本上就这样了，我们已经准备好了所有的材料。我们现在要做的就是把它们按正确的顺序排列。

```
const path = require('path');
const debug = require('debug')('app');
const chokidar = require('chokidar');

const boot = require('./boot');

const state = {
  server: null,
  sockets: [],
};

function start() {
  state.server = require('./server')().listen(3000, () => {
    debug('Started on 3000');
  });
  state.server.on('connection', (socket) => {
    debug('Add socket', state.sockets.length + 1);
    state.sockets.push(socket);
  });
}

function pathCheck(id) {
  return (
    id.startsWith(path.join(__dirname, 'routes')) ||
    id.startsWith(path.join(__dirname, 'server.js'))
  );
}

function restart() {
  // clean the cache
  Object.keys(require.cache).forEach((id) => {
    if (pathCheck(id)) {
      debug('Reloading', id);
      delete require.cache[id];
    }
  });

  state.sockets.forEach((socket, index) => {
    debug('Destroying socket', index + 1);
    if (socket.destroyed === false) {
      socket.destroy();
    }
  });

  state.sockets = [];

  state.server.close(() => {
    debug('Server is closed');
    debug('\n----------------- restarting -------------');
    start();
  });
}

boot()
  .then(() => {
    start();
    chokidar.watch('./routes').on('all', (event, at) => {
      if (event === 'add') {
        debug('Watching for', at);
      }

      if (event === 'change') {
        debug('Changes at', at);
        restart();
      }
    });
  })
  .catch((err) => {
    debug(err);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

结果，
[![Better](img/95a572dca3cc614709b81b912c58d04d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--APLl5Re_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AmzFub3kTFhEcLOHR9z6Bjg.gif)

> 这没有错误处理，所以如果你的应用崩溃了，它就崩溃了。nodemon 确实处理崩溃。您可以通过让 nodemon 忽略我们手动监控的文件来轻松地组合 node mon。
> 如果你使用猫鼬，你可能会遇到类似`error recompiling model`的错误。猫鼬也有一个模型缓存。你也可以使它无效，只需初始化对象为空对象`mongoose.connection.models = {}; mongoose.models = {};`。