# 通过终端服务节点的类型脚本中的 CLI 应用程序

> 原文：<https://dev.to/bradymholt/cli-apps-in-typescript-via-ts-node--10f9>

我真的很喜欢 [ts 节点](https://github.com/TypeStrong/ts-node)项目。它允许您直接在 Node.js 中运行 TypeScript，而不必首先通过 TypeScript 编译器(tsc)运行文件。它通过注册一个文件扩展名为`.ts`或`.tsx`的文件的处理程序，并依靠 ts-node 动态地与 TypeScript 编译器来回编组来实现这一点。它还支持一个非常简洁的打字稿 REPL。

我主要在节点项目([示例](https://github.com/bradymholt/psqlformat/blob/master/package.json#L52))中使用它进行单元测试，以避免中间的构建步骤。此外，我越来越多地使用它在运行时直接在我自己的一些服务器端项目上执行 TypeScript。同样，这样做减少了构建时间并减少了摩擦。

最近，我越来越多地使用 Node 来编写 CLI 应用程序，并认为如果 ts-node 也能用于这一点，那该多好。事实证明这很简单。

首先，安装 ts-node:

```
npm install ts-node 
```

Enter fullscreen mode Exit fullscreen mode

然后，创建包含 CLI 逻辑的 TypeScript 模块文件。

```
export function run() {
  const message: string = "Hello from TypeScript!";
  console.log(message);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，创建您的入口脚本。这是您将从 shell 中运行的脚本。

**我的剧本**

```
#!/usr/bin/env node

require('ts-node').register();
require('./cli.ts').run(); 
```

Enter fullscreen mode Exit fullscreen mode

您需要首先使这个文件可执行，所以运行`chmod +x ./my-script`。

现在，您可以从您的 shell:
中运行`./my-script`

```
> ./my-script
Hello from TypeScript! 
```

Enter fullscreen mode Exit fullscreen mode

如果您对 cli.ts 文件进行了更改，这些更改将在您下次运行 my-script 时生效，因为最新的更改是通过 ts-node 运行的。