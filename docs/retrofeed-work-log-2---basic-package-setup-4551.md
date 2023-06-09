# 反馈工作日志#2 -基本包装设置

> 原文：<https://dev.to/horia141/retrofeed-work-log-2---basic-package-setup-4551>

我们在[想法&基本设置](https://horia141.com/retrofeed.html)中留下了项目想法和总体技术计划。当前和接下来的两篇文章将涵盖任何互联网应用程序项目的一系列基础问题。更准确地说，我们将:

*   建立一个基本结构的存储库，建立各种流程，链接一些面向开发者的服务。这将在本文中讨论。
*   设置项目的应用程序部分。我们将讨论 web 框架、设置会话、迁移、认证、Docker、服务器端渲染等。这里有很多实质性的东西，即使应用程序本身最终不会做那么多，也会有很多砖块被砌好。这将在下一篇文章中讨论。
*   设置项目的基础设施部分。我们将处理设置 GCP，为部署构建一个“运行时环境”,并构建实际的构建管道来实现它。这将在本节的第三篇也是最后一篇文章中讨论。

在这个过程的最后，我们基本上会有一个非常简单的 web 应用程序，大概有两个屏幕——一个“hello world”主页和一个“log in”页面，显示用户名。然而，我希望所有的流程和基础问题都得到决定和解决。从如何在本地运行测试，到监控或回滚一个坏的发布。如前一篇文章所述，期待*现代工具，但保守的选择*。

既然那不在话下，那就让我们从合适的“第一部分”开始吧。

你可以在这里查看代码。它托管在 GitHub 上，是一个单一的存储库。它被标记为`v0.0.2`。将来我也会引用精确的版本，而不仅仅是提交。

概括一下[上一篇文章](https://horia141.com/retrofeed.html)，主要的架构选择是:

> 软件栈将会是 Node 和 TypeScript 作为后端，NestJs 和 Express 作为 web 框架，Raynor 和 Raynor RPC 作为 NestJs 的 RPC 类型的东西。我将使用 Postgres 作为数据存储，Redis 用于缓存。我将使用 Knex.js 作为数据访问库。我将使用 React &它是用于前端的生态系统。本地环境将在 Docker 上运行，使用 docker-compose。部署将在谷歌的云上完成。我将使用托管 SQL 和 Redis 产品，并在生产中运行 Docker。但是我不会走 Kubernetes 的路线，而只是用 docker run 在一个计算节点上运行服务。当然，基础设施是通过 terraform 的代码。我将使用的其他服务是代码托管 GitHub，Travis。Ci 用于构建，NPM 用于包托管，Codecov.io 用于覆盖报告，Cypress.io 用于端到端测试，Auth0 用于轻松认证，GitHub 的 API 用于存储库。代码将会在麻省理工学院的许可下发布，我会尽可能的公开我正在做的事情。

在这个阶段，已经可以在资源库中看到一堆这样的内容。文件结构看起来像这样:

```
.
├── .gitignore
├── .nvmrc
├── .travis.yml
├── LICENSE.txt
├── README.md
├── docker-compose.yml
├── package.json
├── scripts
│ ├── lint.sh
│ ├── send-coverage.sh
│ ├── serve-watch.sh
│ ├── serve.sh
│ ├── test-watch.sh
│ └── test.sh
├── src
│ └── index.ts
├── test
│ └── index-test.ts
├── tsconfig.json
├── tslint.json
├── tsnyc.json
└── yarn.lock 
```

事实上这没什么大不了的。`.gitignore`、`README.md`和`LICENSE.txt`都是非常典型的例子。我们使用`yarn`作为包管理器，`tslint`使用它默认的和非常严格的规则集，`istanbul` / `nyc`用于覆盖报告等。

我将重点介绍一些更有趣的文件和选择，但是您可以随意浏览，看看事情是如何发展的。

`package.json`看起来像这样:

```
{
    "name": "retrofeed",
    "version": "0.0.2",
    "description": "Manage updates for your project's dependencies",
    "author": "Horia Coman horia141@gmail.com",
    "scripts": {
        "serve": "./scripts/serve.sh",
        "serve:watch": "./scripts/serve-watch.sh",
        "test": "./scripts/test.sh",
        "test:watch": "./scripts/test-watch.sh",
        "lint": "./scripts/lint.sh"
    },
    "devDependencies": // Not shown
    "dependencies": // Not shown
} 
```

`scripts`部分很有趣。它只引用`./scripts`目录。这是我作为一个开发者可以做的所有*动作*的地方。理想情况下，你想做的一切——从运行测试，到进行部署，再到重新生成 [LetsEncrypt](https://letsencrypt.org/) 证书，都会在这里有一个脚本。目前的结构是:

```
scripts/
├── lint.sh
├── send-coverage.sh
├── serve-watch.sh
├── serve.sh
├── test-watch.sh
└── test.sh 
```

注意，并不是所有的键都映射到了`scripts`键。所以你可以说`yarn lint`，但不能说`yarn send-coverage`。这是故意的。它突出显示了您应该在本地使用的选项。虽然您可以运行其他应用程序，但它们是针对 CI 服务器或生产环境的。然而，反过来说，你不应该想运行任何没有脚本的东西。

比如，`serve-watch.sh`长得像(比`v0.0.2`稍微靠后一点):

```
#!/bin/bash

set -e # Fail on first command

source scripts/setup-env.sh # Sets up environment variables

npx ts-node-dev --inspect=${INSPECT_PORT} -- src/index.ts # Does the action 
```

请注意，`npx`是必需的，因为该脚本也应该在`yarn serve:watch`之外运行。

代码本身是最小的。只有一个带有单个函数的`src/index.ts`文件，以及一个相应的带有测试的`test/index-test.ts`文件。我们使用`mocha`、`chai`、`sinon`等进行测试，因为这些在 JavaScript 世界中是相当标准的。稍后会有更多的代码。

需要注意的一点是没有`compile.sh`或`build.sh`脚本。TypeScript 工具足够好，有相当于`node` ( `ts-node`)或`node-dev` ( `ts-node-dev`)的工具，允许完全跳过传统的编译步骤。因此，这消除了很多复杂性，并且看不出它有什么不好的地方——也许是生产中的性能。但我对此表示怀疑。

一件有趣的事情是我们所拥有的一系列整合。他们依次是:

*   [特拉维斯。CI](https://travis-ci.org/horia141/retrofeed) 作为构建工具。每个分支中的每个提交都会导致一个构建。但是只有带有版本号的标记提交才会被推送到 npm。检查构建配置的 [`.travis.yml`](https://github.com/horia141/retrofeed/blob/v0.0.2/.travis.yml) 。这是非常标准的——一些设置，一个`yarn lint`和`yarn test`的运行，一个向工作服发送覆盖结果的测试成功动作，以及一个被标记的向 NPM 推进的部署动作。
*   [NPM](https://www.npmjs.com/package/retrofeed) 作为一个包存储库。Travis 会自动推出一个绿色的带有标签的构建包。
*   用于管理覆盖率报告的。在设置中获得正确的*数据需要一点魔法。每一个成功的构建都会发送覆盖率数据，如果覆盖率不够好，在某些时候测试就会失败。*
*   [Cypress.io](https://dashboard.cypress.io/#/projects/a4ib2i/runs) 进行端到端测试。这种集成在这里还看不到，因为没有端到端的测试。
*   用于使当前服务器实例公开可用。同样，这里还没有设置，但它只是一个很好的工具。

反正现在就这样了。正如我所说的，没有太多的事情发生，但是为它的发生做了很多准备。在接下来的部分中，我们将实际上拥有一个 web 应用程序，以及一个完整的云部署。回头见！

点击查看系列[中的所有文章。](//https//horia141.com/retrofeed.html)