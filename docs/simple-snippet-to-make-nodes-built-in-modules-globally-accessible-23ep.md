# 使节点的内置模块可以全局访问的简单代码片段

> 原文：<https://dev.to/jochemstoel/simple-snippet-to-make-nodes-built-in-modules-globally-accessible-23ep>

我非常懒惰，不想在我正在做的每一件小事和每一个临时文件中键入相同的 *fs = require('fs')* 这只是达到目的的一种手段，永远不会在生产中使用。

我决定分享这个小片段，它迭代 Node 的内部(内置)模块，并且只全球化有效的*模块。无效的是那些你不能或者不应该直接要求的，比如内部和‘子模块’(包含一个'/')。简单地包括 *globals.js* 或者从下面复制粘贴。*

[camelcase](https://github.com/sindresorhus/camelcase/blob/master/index.js) 函数的作用只是将*子进程*转换成*子进程*。如果你更喜欢没有 NPM 依赖，那么只需从 GitHub 复制粘贴函数，或者*完全省去*，因为 camelcasing 只是可爱而不是必要的。

#### globals.js

```
/* https://github.com/sindresorhus/camelcase/blob/master/index.js */
const camelCase = require('camelcase')

Object.keys(process.binding('natives')).filter(el => !/^_|^internal/.test(el) && [
    'freelist',
    'sys', 
    'worker_threads', 
    'config'
].indexOf(el) === -1 && el.indexOf('/') == -1).forEach(el => {
    global[camelCase(el)] = require(el) // global.childProcess = require('child_process')
}) 
```

Enter fullscreen mode Exit fullscreen mode

只要求某处和所有内置模块都是全局的。

```
require('./globals')

fs.writeFileSync('dir.txt', childProcess.execSync('dir')) 
```

Enter fullscreen mode Exit fullscreen mode

这些是暴露于全局范围的模块(节点 v10.10.0)

```
asyncHooks
assert
buffer
childProcess
console
constants
crypto
cluster
dgram
dns
domain
events
fs
http
http2
https
inspector
module
net
os
path
perfHooks
process
punycode
querystring
readline
repl
stream
stringDecoder
timers
tls
traceEvents
tty
url
util
v8
vm
zlib 
```

Enter fullscreen mode Exit fullscreen mode

> 嗯。我建议我们开始使用 [#snippet](https://dev.to/t/snippet) 标签来互相分享片段。=)