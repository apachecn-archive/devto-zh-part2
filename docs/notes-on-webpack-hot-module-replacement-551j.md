# Webpack 热模块更换说明

> 原文：<https://dev.to/kasparsd/notes-on-webpack-hot-module-replacement-551j>

我花了几天时间通读了 [webpack](https://github.com/webpack/webpack/tree/master/hot) 和 [webpack-dev-server](https://github.com/webpack/webpack-dev-server/tree/master/client-src/default) 源代码的相关部分，并弄清楚如何为任何不使用`webpack-dev-server`服务 JS 包的项目启用 [Webpack 热模块替换](https://webpack.js.org/concepts/hot-module-replacement/)。我想使用 <abbr title="Hot Module Replacement">HMR</abbr> 与流浪汉运行 Docker 的一个本地 WordPress 主题开发工作流程。

## 它是如何工作的？

<abbr title="Hot Module Replacement">HMR</abbr> 的主要需求是，当您编辑源文件时，当一个新的包文件被构建时，浏览器接收通知。不幸的是，`webpack/dev-server.js`中的默认通知逻辑依赖于本地 Node.js 事件上的[，而不是由`webpack-dev-server`发送的 websocket 消息。](https://github.com/webpack/webpack/blob/7f11210cffc58820b4cdd086934398306882c338/hot/dev-server.js#L51-L57)

因此， <abbr title="Hot Module Replacement">HMR</abbr> 将永远不会工作，如果捆绑包文件不是由同样的`webpack-dev-server`提供的话，它也会监视文件变化并触发构建。

## 本地节点事件和 Websockets

幸运的是，`webpack-dev-server`也[在`/sockjs-node`启动一个 websocket 服务器](https://github.com/webpack/webpack-dev-server/blob/ef5598440ebf6847ac02a2d44d7fec70efee0aa1/lib/Server.js#L569-L623)，并且[向监听客户端](https://github.com/webpack/webpack-dev-server/blob/ef5598440ebf6847ac02a2d44d7fec70efee0aa1/lib/Server.js#L601-L607)发送一些消息，以表明它将报告与 HMR 相关的变化。

因此，您的捆绑脚本只需要两件事情来处理来自 websocket 服务器的数据:

1.  对传入消息进行操作的 websocket 客户端，以及
2.  可以应用传入代码补丁的热模块替换逻辑。

`webpack-dev-server`带有[一个 websocket 客户端](https://github.com/webpack/webpack-dev-server/blob/ef5598440ebf6847ac02a2d44d7fec70efee0aa1/client-src/default/index.js) `webpack-dev-server/client`，如果你启用了`--hot`并且[没有显式地将](https://github.com/webpack/webpack-dev-server/blob/3a7f7d543889707725e5d60fc21fe2de975d627d/lib/util/addDevServerEntrypoints.js#L8-L39) `--inline`设置为`false`，那么[会自动注入到你的包](https://github.com/webpack/webpack-dev-server/blob/3a7f7d543889707725e5d60fc21fe2de975d627d/bin/webpack-dev-server.js#L375)中。websocket 客户端解析来自 dev 服务器的所有传入消息，并且[触发一个 native Node.js 事件](https://github.com/webpack/webpack-dev-server/blob/ab4eeb007283fdf3e8950ff1f3ff8150a4812061/client-src/default/index.js#L216) :

```
const hotEmitter = require('webpack/hot/emitter');
hotEmitter.emit('webpackHotUpdate', currentHash); 
```

并且[也发出一个 websocket 消息](https://github.com/webpack/webpack-dev-server/blob/ab4eeb007283fdf3e8950ff1f3ff8150a4812061/client-src/default/index.js#L219) :

```
if (typeof self !== 'undefined' && self.window) {
  // broadcast update to window
  self.postMessage('webpackHotUpdate${currentHash}', '*');
} 
```

然而，`webpack/hot/dev-server`中的 [HMR 客户端逻辑](https://github.com/webpack/webpack/blob/7f11210cffc58820b4cdd086934398306882c338/hot/dev-server.js)仅使用 native Node.js 事件`webpackHotUpdate`来应用补丁。这就是为什么如果不是由`webpack-dev-server`提供服务，您的包将永远不会收到关于 HMR 事件的通知，因为在节点环境之外不支持本地节点事件。

## 怎么修？

克隆`webpack/hot/dev-server`中的 HMR 客户端逻辑，让它监听 websocket 上的`webpackHotUpdate`消息，而不是本地节点事件。

```
window.addEventListener('message', (e) => {
  if (typeof e.data === 'string' && e.data.includes('webpackHotUpdate')) {
    var hmrHash = e.data.replace('webpackHotUpdate', '');
    // Run check() from webpack/hot/dev-server.js
  }
}); 
```