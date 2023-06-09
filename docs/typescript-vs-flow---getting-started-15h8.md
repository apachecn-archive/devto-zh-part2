# TypeScript 与 Flow -入门

> 原文：<https://dev.to/michaeljota/typescript-vs-flow---getting-started-15h8>

> 最初发布在[我的博客](https://michaeljota.github.io/typescript-vs-flow-getting-started/)

# 简介

我不会隐瞒我是全职 Typescript 开发人员的事实，但我会努力给 Flow 一个机会，因为我真的希望他们之间有一个公平的比较。我知道这些年来已经有好几个了，但是这一个，我会试着每年至少更新两次。

我第一次见到 Typescript 是在我的 BD 期末项目中。然后，我有很强的 Java 和 C#知识，但我不太了解 Javascript，所以 Typescript 是我立即感兴趣的东西。无论如何，当时我认为预编译器和构建工具对我来说太多了，所以我直到 2016 年中期 Angular 问世时才开始使用它。

去年，当我在 React 项目中工作时，我遇到了 Flow。我们想在项目中添加静态类型检查，因为它变得越来越大，而我们不能很好地扩展。当时我尝试了这两种方法，但我真想使用 Typescript，因为它有全新的语法，所以我不太相信 Flow。

我记得我们，作为团队，选择了 Typescript，因为它集成了几个 ide。我想事情改变了，我想给心流一个机会，一个真正的机会。

我试着用 1 到 10 分的尺度来评价他们两个的几个特点。最后，我会把所有的分数相加。然而，我不认为最后的分数会是一个更好的指标，如果不是的话，我为什么要给分数呢？。

* * *

# 它们是什么？

Flow 是 JavaScript 的静态类型检查器，Typescript 是一种用可选的静态类型严格超集 JavaScript 的语言。这是他们之间的一个微妙的区别，我希望你能注意到这一点。

# 入门

## 流量

从 Flow 开始，我们需要安装一个编译器来删除文件的类型注释。这将是 Babel，配置有流量预设，或`flow-remove-types`。我会选择第一个，因为我认为现在大多数项目都在使用 Babel。

1.  我们初始化项目，并添加 Babel dev 依赖项和流预置。

```
$ ~/flow# npm init -y
$ ~/flow# npm i -D babel-cli babel-preset-env babel-preset-flow 
```

Enter fullscreen mode Exit fullscreen mode

1.  在`package.json`中配置 Babel 并添加脚本。

`.babelrc`

```
{  "presets":  ["env",  "flow"]  } 
```

Enter fullscreen mode Exit fullscreen mode

`package.json`(节选)

```
{  "scripts":  {  "build":  "babel src/ -d lib/"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

1.  安装和配置流程

```
$ ~/flow# npm i -D flow-bin 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你有`npm` (^5.3.0)的最新版本，你可以使用`npx`，就像我一样。如果没有，您应该将 Flow 作为脚本添加到您的`package.json`中，并使用`npm run ${flow script}`命令。

```
$ ~/flow# npx flow init
$ ~/flow# npx flow 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个包含空配置的`.flowconfig`,它使用类似于`.INI`文件的定制格式。

```
[ignore]

[include]

[libs]

[lints]

[options]

[strict] 
```

Enter fullscreen mode Exit fullscreen mode

1.  开始流动

```
$ ~/flow# npx flow 
```

Enter fullscreen mode Exit fullscreen mode

该命令将在后台进程中以观察模式开始流程，这将使该命令花费很长时间来运行。有那么一会儿，我以为它在检查。当它跑第二次时，它会跑得更快。

> 使用`npx flow stop`停止该过程。

1.  编写一个流文件

`src/foo.js`

```
// @flow

function foo(x: ?number): string {
  if (x) {
    return x;
  }
  return 'default string';
} 
```

Enter fullscreen mode Exit fullscreen mode

需要使用`// @flow`注释来说明流应该检查哪些文件。注意，即使这个文件是 Javascript，它也不再有有效的 Javascript 语法，所以它不能在任何浏览器上运行，这就是为什么我们需要一个工具来删除类型注释。

> 如果你想检查所有的文件，不管它们有没有注释，你必须运行`npx flow check --all`。否则，只需运行`npx flow`即可。

1.  检查代码

我们再次运行`npx flow`，它会告诉我们代码中有错误:

```
$ ~/flow# npx flow 
```

Enter fullscreen mode Exit fullscreen mode

```
Error ------------------------------------------------------ foo.js:5:12

Cannot return `x` because number [1] is incompatible with string [2].

 foo.js:5:12
 5|     return x;
               ^

References:
 foo.js:3:18
 3| function foo(x: ?number): string {
                     ^^^^^^ [1]
 foo.js:3:27
 3| function foo(x: ?number): string {
                              ^^^^^^ [2]

Found 1 error 
```

Enter fullscreen mode Exit fullscreen mode

1.  编译代码

在本指南中，我将使用 Babel，但正如我之前所说，你也可以使用`flow-remove-types`工具。

```
$ ~/flow# npm run build 
```

Enter fullscreen mode Exit fullscreen mode

输出:`lib/foo.js`

```
'use strict';

function foo(x) {
  if (x) {
    return x;
  }
  return 'default string';
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，即使有类型错误，Babel 也会发出代码。

## 打字稿

要使用 Typescript，您不需要设置任何其他东西，因为 Typescript 包括它自己的发射器。然而，如果你愿意，你可以用 Babel 7 设置 Typescript，但是我不会这样做，因为常见的用例是 Typescript 本身。

1.  初始化项目并添加 Typescript 作为开发依赖项。

```
$ ~/flow# npm init -y
$ ~/flow# npm i -D typescript 
```

Enter fullscreen mode Exit fullscreen mode

> 出于本指南的目的，我将安装目前在`RC`中的`Typescript 2.8`。

1.  配置 Typescript

```
$ ~/flow# npx tsc --init 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个 Typescript 配置文件。这是一个 JSON 标准文件，具有良好的 JSON 模式支持。

`tsconfig.json`(节选)

```
{  "compilerOptions":  {  "target":  "es5",  "module":  "commonjs",  "strict":  true,  "esModuleInterop":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

`tsconfig.json`文件将包含几乎所有的编译器选项，其中大部分被注释掉了。

1.  开始打字

```
$ ~/flow# npx tsc 
```

Enter fullscreen mode Exit fullscreen mode

该命令将检查并编译所有的 Typescript 文件，并将编译后的 Javascript 文件放在旁边。我们可以在配置中改变这种行为，设置一个源文件夹，一个目标文件夹，甚至在出现类型错误时阻止发出。

> 要在监视模式下启动 Typescript，只需添加`-w`标志。

1.  更新 Typescript 配置

```
{  "compilerOptions":  {  "target":  "es5",  "module":  "commonjs",  "outDir":  "./lib",  "rootDir":  "./src",  "lib":  ["dom",  "es2018"],  "noEmitOnError":  true,  "strict":  true,  "esModuleInterop":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

有了这个配置，我们将有一个类似于 Babel 的行为，但是如果在输入中有任何错误，它不会发出。

1.  编写一个类型脚本文件

`src/foo.ts`

```
function foo(x?: number): string {
  if (x) {
    return x;
  }
  return 'default string';
} 
```

Enter fullscreen mode Exit fullscreen mode

任何有效的 Javascript 代码都是有效的类型脚本代码，但是为了被认为是类型脚本代码，您需要将它放在类型脚本文件中，扩展名为`.ts`。这听起来像 Flow 中的注释，默认情况下，Typescript 编译器将只检查 Typescript 文件。

1.  检查和编译您的代码

Typescript 有自己编译器，它处理类型检查和代码编译。我们再次运行`npx tsc`，它告诉我们代码有问题。

```
$ ~/flow# npx tsc 
```

Enter fullscreen mode Exit fullscreen mode

```
src/foo.ts(3,5): error TS2322: Type 'number' is not assignable to type 'string'. 
```

Enter fullscreen mode Exit fullscreen mode

如果你看，没有`lib`文件夹，因为它没有完成发射。描述更短，但信息非常相似。

# 结论

我只是向您展示了如何设置工具的简单用法，但在实际项目中，您可能会使用一些代码捆绑器，如 Webpack 或 Rollup。

在设置和获取中，我会给他们两个相同的分数，8。虽然我觉得 Typescript 更容易设置，因为如果您想使用 Flow，您也需要设置 Babel，但您可能已经安装并配置了 Babel。

我不会给代码分析任何东西，因为代码太简单了。

然而，我会在编译中给 Typescript 打 9 分，因为我觉得它阻止了一些好的特性，但我不得不说我更喜欢流消息，这就是我给它打 8 分的原因。

# 当前得分

| 特征 | 以打字打的文件 | 流动 |
| --- | --- | --- |
| 安装 | eight | eight |
| 汇编 | nine | eight |
| 总数 | Seventeen | Sixteen |