# 使用 TypeScript 和 microbundle 编写 npm 模块

> 原文：<https://dev.to/hugo__df/writing-an-npm-module-with-typescript-and-microbundle-1b73>

对于那些希望编写一个包并将其发布到 npm 的人来说，TypeScript + microbundle 是构建高质量库的一种低成本方式。

## 为什么打字稿？

[TypeScript](https://www.typescriptlang.org/) 是一个 JavaScript 超集，向其中添加了静态类型。

它的其他特性也非常接近 ECMAScript 规范(当前和未来)。

对于库的作者来说，这意味着你为库的消费者，甚至是那些不使用 TypeScript 的人，提供了更多关于预期类型的细节，对于一些集成了 TypeScript 的编辑器/ide(比如 Visual Studio 代码)来说，这意味着更好的自动完成。TypeScript 还可以作为内联文档，当您传递一些不应该传递的内容时，它会向您大声疾呼，当您几个月后重新发现您的代码时，它会派上用场。

## 为什么微束？

[Microbundle](https://github.com/developit/microbundle) 是一款“微型模块零配置捆绑器”。它是一个围绕 [rollup](https://rollupjs.org/guide/en) 的包装器，具有相同的默认值(包括缩小/压缩)，良好的输出大小统计，多种目标格式(ES 模块，CommonJS，UMD)。最重要的是，在这篇文章的范围内，它有现成的类型支持(*实际上*没有配置，甚至没有一个`tsconfig.json`)。

它非常容易设置，并且允许库作者专注于构建一个伟大的库，而不是设置管道来从 ES6/TypeScript 或其他编译到 JS 的工具发布 JavaScript 库🙂。

# 零配置捆绑

首先，我们必须创建设置包运行`npm init`并完成所有提示。

下一次运行:`npm i --save-dev microbundle`。

让我们创建一个 src 和 dist 文件夹:`mkdir src && mkdir dist`

并添加第一个 TypeScript 文件:`touch src/index.ts`。

让我们给 index.ts 添加一个类，这样我们就不只是编译空文件:
`echo "export class MyMainClass {}" >> src/index.ts`

微绑定查看`package.json` `"main"`和`"source"`字段(编译入口点和源入口点)，在我们的例子中是`dist/index.js`(还不存在)和`src/index.ts`。

让我们对其进行编辑，使其在`package.json`中包含以下内容:

```
{
  "main": "dist/index.js",
  "source": "src/index.ts"
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着 microbundle 现在知道如何编译我们的库了，运行:`npx microbundle`(在 npm < 5.x 的版本上，也可以运行`./node_modules/.bin/microbundle`)。

这将把你的`src/index.ts`编译到`dist`文件夹中。如果你看看`dist`文件夹的内容，你会发现微捆绑为你做了多少工作:

```
$ ls dist
index.d.ts       index.js.map     index.m.js.map   index.umd.js.map
index.js         index.m.js       index.umd.js 
```

Enter fullscreen mode Exit fullscreen mode

让我们解释一下这些都是什么:

*   `index.js`是 CommonJS 模块。这是节点使用的模块类型，看起来像`const myModule = require(` `'` `my-module` `'` `)`
*   `index.m.js`是 ECMAScript 模块，在 ES6 中定义，看起来像`import MyModule from` `'` `my-module` `'`
*   `index.umd.js`是 UMD 模块
*   `index.d.ts`是 TypeScript 类型声明文件

然后有一个匹配的`.map`文件映射回每个文件的 TypeScript 源文件。

看一看里面`index.js`:

```
$ cat dist/index.js
var n=function(){return function(){}}();exports.MyMainClass=n;
//# sourceMappingURL=index.js.map 
```

Enter fullscreen mode Exit fullscreen mode

我们的`class MyMainClass {}`语句被编译成它的 ES5 等价物，导出成 CommonJS 导出。

`index.d.ts`同样有趣的是:

```
$ cat dist/index.d.ts
export declare class MyMainClass {
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得 TypeScript 项目能够将正确的类型信息分配回包中……这是一种迂回的方式，因为 TypeScript 项目应该能够导入文件。独立的类型声明意味着非 TypeScript 项目也可以理解模块的公共 API(例如，代码编辑器可以对看不见的 npm 包进行智能自动完成)。

microbundle 还可以观察变化:`npx microbundle watch`。

为了便于使用，我们可以将观察和构建任务作为 npm 脚本放在`package.json`中:

```
{

  "scripts": {

    "dev": "microbundle watch",

    "build": "microbundle"

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

# 
  
发布模块

通过利用 microbundle，我们可以将模块发布为 CommonJS 模块(标准 npm 模块)，也可以发布为 ES 模块和 UMD 模块，为此，请遵循位于[https://github . com/develop it/micro bundle # specifying-builds-in-package JSON](https://github.com/developit/microbundle#specifying-builds-in-packagejson)的指南。

**TL；博士**

*   `"main": "dist/index.js"`
*   `"umd:main": "dist/index.umd.js"`
*   `"module": "dist/index.m.js"`
*   `"source": "src/index.ts"`

有了这个`package.json`，你可以用`npm publish`发布到 npm。
如果你喜欢使用[微捆绑](https://github.com/developit/microbundle)，一定要继续使用它，但也要在 GitHub 上给它一颗[星，让杰森·米勒(又名](https://github.com/developit/microbundle) [@_developit](https://twitter.com/_developit) 知道你是 Twitter 的粉丝。