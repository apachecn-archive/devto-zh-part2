# 使用“esm”在没有 Babel/Webpack 的节点中使用 ES 模块

> 原文：<https://dev.to/hugo__df/use-es-modules-in-node-without-babelwebpack-using-esm-1obk>

Node 已经在本机实现越来越多的 ES6+ (ESNext)特性。实现时间最长的特性之一是模块。这样做的原因是 Node 和 npm 运行在所谓的 CommonJS 上，您可以使用`require(` `'` `module-name` `'` `)`从其他模块导入，并使用`module.exports`对象从模块中公开实体。

Node 的 CommonJS 实际上是 JavaScript 中最先被广泛采用的模块系统之一。人们可以轻松地捆绑 CommonJS，加上它在节点应用程序和工具中的广泛使用，这意味着 CommonJS 很快取代了 RequireJS 和 SystemJS，用于前端应用程序依赖和模块管理

CommonJS 有一些缺点，比如难以进行静态分析，这导致了臃肿的包。它也不是 ECMAScript 规范的一部分，而 ES 模块是。

对于那些仍然感到疑惑的人，ECMAScript(或 ES)模块使用带有`import thing from 'my-module';`或`import { something } from 'my-module'`的语法来导入内容，使用`export default`或`export something`来公开模块中的实体。

像 Webpack、Rollup 和 package 这样的捆扎机支持 ES 模块。对于节点服务器，我仍然倾向于用 CommonJS 风格编写，因为 Node 对大多数 ESNext 特性都有很好的现成支持(例如 rest/spread、async/await、destructuring、class、速记对象语法),而且我不喜欢搞乱 bundlers 和 transpilers。

我发现了 [esm](https://github.com/standard-things/esm) 模块，“今天的明天的 ECMAScript 模块！”由[约翰-大卫·道尔顿](https://github.com/jdalton)(洛达什😄).它允许您在 Node 中使用 ES 模块，而无需编译步骤。它很小，占用空间很小，并且附带了一些额外的好东西

以下是一些没有严格记录的使用方法。这涵盖了用例，如 es 模块的增量采用(即将一些模块转换为 ESM，但不是整个应用程序)。使用这个将帮助你分享

## 导入默认从 CommonJS 中的 ES 模块导出

```
const esmImport = require('esm')(module);
const foo = esmImport('./my-foo');
console.log(foo); 
```

## 从 CommonJS 中的 ES 模块导入命名导出

```
const esmImport = require('esm')(module);
const { bar, baz } = esmImport('./my-foo');
console.log(bar, baz); 
```

## 将 ES 模块重新导出为 CommonJS

这在文档中有记录，但我想为了完整起见我会把它包括在内

```
module.exports = require('esm')(module)('./my-es-module');
// see the docs
// https://github.com/standard-things/esm#getting-started 
```

## 使用 ES 模块加载整个应用

同样，这在文档中，但为了完整起见将其包括在内

```
node -r esm app.js
// see the docs
// https://github.com/standard-things/esm#getting-started 
```

## 使用顶级 await

假设我们有这个模块`cli.module.js`(取自[github.com/HugoDF/wait-for-pg](https://github.com/HugoDF/wait-for-pg/blob/master/wait-for-pg-cli.module.js#L35-L44)):

```
const waitForPostgres = () => Promise.resolve();

try {
  await waitForPostgres();
  console.log('Success');
  process.exit(0);
} catch (error) {
  process.exit(1);
} 
```

有趣的是，这是在没有使用`async`函数的情况下使用`await`。这是`esm`允许你做的事情。这可以通过在`package.json`中设置`"``esm``"``: {``"``await``"``: true }`来启用，但也可以在转换时间`cli.js` :
时启用

```
const esmImport = require('esm')(module, { await: true });
module.exports = esmImport('./cli.module'); 
```

瞧，它工作了:

```
$ node cli.js
Success 
```

这就结束了如何使用 ES 模块，而不需要翻译。通过一个模块/命令行界面的例子，可以更全面地了解这在 [ES6 中意味着什么。](https://dev.to/hugo__df/es6-by-example-a-modulecli-to-wait-for-postgres-in-docker-compose-902)

如果你对“JavaScript 模块、捆绑+依赖管理的历史”这篇文章感兴趣，请通过[订阅](https://buttondown.email/hugo)告诉我。