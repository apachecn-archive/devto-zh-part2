# 你可能不需要巴贝尔

> 原文：<https://dev.to/dandv/why-you-dont-really-need-babel-4k2h>

从版本 8.5.0 开始(发布于 2017 年 9 月[，Node.js 原生支持 ES 模块，如果你传递了`--experimental-modules`标志并使用了。所有相关文件的 mjs 扩展名。这意味着我们不再需要像巴别塔这样的 transpiler！](https://github.com/nodejs/node/blob/master/doc/changelogs/CHANGELOG_V8.md#8.5.0)

**lib . mjs**T2】

```
export const hello = 'Hello world!'; 
```

Enter fullscreen mode Exit fullscreen mode

**index.mjs**

```
import { hello } from './lib';
console.log(hello); 
```

Enter fullscreen mode Exit fullscreen mode

运行方式:

```
node --experimental-modules index.mjs 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您已经编写并使用了 ECMAScript 模块，没有使用 Babel 或任何 transpilers。

# 如何发布原生 ES 模块

要将一个 ES 模块发布到 [NPM](https://npmjs.org) 以便可以直接导入，而不需要通天塔，只需将您的`package.json`中的主字段指向`.mjs`文件，但省略扩展名:

```
{  "name":  "mjs-example",  "main":  "index"  } 
```

Enter fullscreen mode Exit fullscreen mode

这是唯一的变化。通过省略扩展名，如果使用`--experimental-modules`运行，Node 将首先查找 mjs 文件。否则它将退回到。js 文件，所以您现有的支持旧节点版本的 transpilation 过程将像以前一样工作——只需确保将 Babel 指向。mjs 文件。

这是我发布给 NPM 的一个本机 ES 模块的源代码，它向后兼容 Node < 8.5.0。你现在就可以使用它，不需要巴别塔或其他任何东西。

安装模块:

```
yarn add local-iso-dt
# or, npm install local-iso-dt 
```

Enter fullscreen mode Exit fullscreen mode

创建一个测试文件`test.mjs` :

```
import { localISOdt } from 'local-iso-dt';
console.log(localISOdt(), 'Starting job...'); 
```

Enter fullscreen mode Exit fullscreen mode

使用- experimental-modules 标志运行节点(v8.5.0+):

```
node --experimental-modules test.mjs 
```

Enter fullscreen mode Exit fullscreen mode

# 
 [T3】
结论](#conclusion) 

向 Node.js 包中添加本地 ES 模块支持非常容易。只需将您的 ES6+文件重命名为。mjs 并更新`package.json`中的主条目，省略扩展。这样，您的模块可以直接在带有`--experimental-modules`标志的节点 v8.5.0+中使用。

虽然支持现在是试验性的(2018 年 2 月)，但它不太可能发生重大变化，Node 计划在 v10 中取消标志要求。

保持你的 transpilation 脚本向后兼容，并随时分叉我的例子原生 ES 模块 repo。

# 进一步阅读

*   [在 Node.js 中原生使用 ES 模块](http://2ality.com/2017/09/native-esm-node.html)
*   [设置多平台 npm 包](http://2ality.com/2017/04/setting-up-multi-platform-packages.html)
*   [StackOverflow 问题](https://stackoverflow.com/questions/46418010/publish-es-module-mjs-to-npmjs-with-backwards-compatibility-for-node-8-5-0)(归功于亚历山大·奥马拉)