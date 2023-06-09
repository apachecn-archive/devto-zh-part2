# 启动 Clix:清理和处理推动器应用程序状态

> 原文：<https://dev.to/kayis/startup-clix-cleanup--fighting-with-pusher-application-state-2ilc>

昨晚和今天，我在一些 build util 脚本的帮助下修复了一些打包问题。

## `node_modules`为每一个λ函数

我为每个 Lambda 函数添加了一个`package.json`,这样我就不必在每次修改时复制后端的 base `node_modules`文件夹。

然后我使用这个脚本在每个 Lambda 函数目录中运行`npm i`:

```
const { readdirSync, statSync } = require("fs");
const { join, resolve } = require("path");
const { execSync } = require("child_process");

const functionsDir = resolve(__dirname + "/../functions/");

const dirs = p =>
  readdirSync(p).filter(f => statSync(join(p, f)).isDirectory());

console.log("Running 'npm i' for Lambda functions:");
dirs(functionsDir)
  .map(d => {
    console.log(" - " + d);
    return d;
  })
  .map(d => join(functionsDir, d))
  .forEach(d => execSync(`cd ${d} && npm i`)); 
```

Enter fullscreen mode Exit fullscreen mode

## Lambda 函数之间共享代码

我添加了一个`shared`文件夹，它被复制到每个 Lambda 函数中。不是很漂亮，但它现在工作。

```
const { readdirSync, statSync } = require("fs");
const { join, resolve } = require("path");
const { ncp } = require("ncp");

const sharedDir = resolve(__dirname + "/../shared/");
const functionsDir = resolve(__dirname + "/../functions/");

const dirs = p =>
  readdirSync(p).filter(f => statSync(join(p, f)).isDirectory());

console.log("Copy shared code to Lambda Functions:");
dirs(functionsDir)
  .map(d => {
    console.log(" - " + d);
    return d;
  })
  .map(d => join(functionsDir, d, "shared"))
  .forEach(d => ncp(sharedDir, d)); 
```

Enter fullscreen mode Exit fullscreen mode

## κ为异步λ函数

我目前只分享一个助手，叫做 kappa，它解决了我遇到的一个问题，基本的 Lambda 函数交互。

```
const lambda = require("../index.js");

exports.handler = (event, context, callback) =>
  lambda(event, context)
    .then(r => {
      if (r && r.body) r.body = JSON.stringify(r.body);
      callback(null, r);
    })
    .catch(callback); 
```

Enter fullscreen mode Exit fullscreen mode

这个函数成为实际的 Lambda 处理程序，并允许你异步编写 Lambda 函数。如果响应体存在，它还会对响应体进行字符串化。

```
exports.handler = (event, context, callback) => {
  callback(null, JSON.stringify({result: event.body + "!"}));
} 
```

Enter fullscreen mode Exit fullscreen mode

现在变成了

```
module.exports = async (event, context) => ({
  result: event.body + "!"
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 推杆应用状态

然后，在所有的维护工作完成后，我试着让服务器上的一些游戏/频道加入逻辑运行起来。

这个想法是，当有人想加入一个频道时，他们必须通过`GET /getgamechannel`请求一个频道 ID。

服务器会根据开放频道和里面有多少玩家来计算一个

这个`/getgamechannel`端点，加上`/pusherauth`端点，应该会阻止玩家加入完整的或已经运行的游戏。

我了解到我可以在服务器端查询我的 Pusher 应用程序的应用程序状态，但不知何故，这并不总是有效。

```
pusher.get({
  path: "/channels",
  params: {
    info: ["user_count"]
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我的客户端从 Pusher 获取状态更新，我可以在每个客户端上看到每个客户端，但是当我在后端运行这个查询时，通常会得到一个空的列表`channels`或其他什么。

要么应用状态最终一致(但不知道什么时候？)或者我的代码中有一些错误，这不会让:D 感到太惊讶

## 结论

总之，这是一个有趣的项目，我已经了解了很多关于 Pusher 和 AWS 的知识:)