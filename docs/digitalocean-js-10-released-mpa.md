# 数字海洋 JS 1.0 发布！

> 原文：<https://dev.to/johnbwoodruff/digitalocean-js-10-released-mpa>

去年我一直在用业余时间写一个库，DigitalOcean JS。我开始它是因为我正在使用 Ionic 构建一个数字海洋移动应用程序，用于从你的手机管理你的数字海洋资源。在查看可供我使用的各种库时，我不喜欢它们有几个原因，包括使用回调函数而不是承诺，只能在 Node 中使用一些而不能在浏览器中使用，等等。由于这些问题，我决定构建我自己的，我想如何使用它，心中有一些关键目标:

*   能够在节点或浏览器环境中使用，在用法上没有区别。
*   使用承诺而不是回调，这样客户就可以利用`async` / `await`。
*   内置于 TypeScript 中，因此该库的使用者可以受益于具有 TypeScript 定义的优秀智能感知。
*   提供可靠的文档，包括用法示例。

为此，我开始开发 DigitalOcean JS。我没有快速构建它，而是决定构建它，因为我正在构建的应用程序需要它。正如我的副业项目一样，(见[我之前的帖子](https://dev.to/johnwoodruff91/rip-side-projects-2j34))它暂时被搁置了。写完那篇博文后，我决定不让这个项目夭折。我实际上只剩下一组端点需要实现。

我很高兴终于宣布数字海洋 JS 正式发布 1.0 版。看看下面的回购，继续战斗，让你的副业活下去！

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [约翰伍德拉夫](https://github.com/johnbwoodruff) / [数字海洋-js](https://github.com/johnbwoodruff/digitalocean-js)

### 数字海洋 API 的 JavaScript 库

<article class="markdown-body entry-content container-lg" itemprop="text">

[![digitalocean-js](img/c7bd887da6d4a0a52921d74f3d9cd33e.png)T2】](https://user-images.githubusercontent.com/5883616/126348407-dd1e694d-64a9-402e-b8df-f59e67686014.png)

# 数字海洋 JS

[![CI](img/bb3a58f3d770e0f7ab7e657eac90da06.png)](https://github.com/johnbwoodruff/digitalocean-js/actions/workflows/main.yml)[![npm](img/607cdbf913ef1581f1476fa0655f0442.png)](https://www.npmjs.com/package/digitalocean-js)[![npm](img/adc8b51b1fcde906a9d9c8fafc804a2f.png)](https://www.npmjs.com/package/digitalocean-js)[![npm](img/0db686713e6cd48d9cb7ddb50e4c693f.png)T11】](https://www.npmjs.com/package/digitalocean-js)

数字海洋 API 的 JavaScript 库。用于节点或浏览器。

## 目标

这个库的构建有几个目标:

*   能够在节点或浏览器环境中使用，在用法上没有区别。
*   使用承诺而不是回调，这样客户就可以利用`async` / `await`。
*   内置于 TypeScript 中，因此该库的使用者可以受益于具有 TypeScript 定义的优秀智能感知。
*   提供可靠的文档，包括用法示例。

## 使用

要使用该库，请从 npm 存储库安装。

```
$ npm install --save digitalocean-js
# Alternatively install with yarn
$ yarn add digitalocean-js
```

Enter fullscreen mode Exit fullscreen mode

只需导入客户机并用 API 令牌初始化它:

```
import { DigitalOcean } from 'digitalocean-js';

const client = new DigitalOcean('my-api-token');
```

Enter fullscreen mode Exit fullscreen mode

要查看所有可用的服务，请查看[文档](https://johnbwoodruff.github.io/digitalocean-js/)。

</article>

[View on GitHub](https://github.com/johnbwoodruff/digitalocean-js)