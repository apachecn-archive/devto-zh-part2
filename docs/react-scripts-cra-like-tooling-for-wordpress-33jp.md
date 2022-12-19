# 像 WordPress 工具一样的 react-scripts (CRA)

> 原文：<https://dev.to/swashata/react-scripts-cra-like-tooling-for-wordpress-33jp>

大家好，

这是我在这里的第一篇帖子，我想分享一个我在过去两周开发的工具，我相信它会给 WordPress 开发者一个更好的开发体验。

作为一个传统的 WordPress 开发者，我知道为每个新项目设置所有这些 webpack 的东西是一件痛苦的事情。最重要的是，如果我们要利用 webpack 的高级代码分割，那么无论如何，我们都需要将入口点中的所有块排队，设置`dynamicPublicPath`用于延迟加载(`import('').then()`)等等。

为了让事情变得更简单，我想到了在一个脚本中抽象构建工具的想法(很像`react-scripts`)。这是目前为止的结果。

[https://www.youtube.com/embed/8M8kCKnWIfY](https://www.youtube.com/embed/8M8kCKnWIfY)

这是回购协议。

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png)[【swashata】](https://github.com/swashata)/[WP 网页包脚本](https://github.com/swashata/wp-webpack-script)

### 💥🔥📦👩‍💻一个易于使用、预先配置、可黑客攻击的 webpack 设置和开发服务器，用于 WordPress 主题和插件。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](../Images/df5c2a64724e2c0552421c5be61777bb.png)](https://wpack.io) 
访问我们的网站 [wpack.io](https://wpack.io) 获取文档和用法

# wpack . io——WordPress 的现代 JavaScript 工具

[![Backers on Open Collective](../Images/109dc1b8c26bcf83e11fe1eee11d1d00.png)](https://raw.githubusercontent.com/swashata/wp-webpack-script/master/#backers)[![Sponsors on Open Collective](../Images/13467d5e6d90196abb4af5e8a62e36e9.png)](https://raw.githubusercontent.com/swashata/wp-webpack-script/master/#sponsors)[![Build Status](../Images/92c5604e8d16d3ce2188fd1e177122a0.png)](https://travis-ci.com/swashata/wp-webpack-script)[![codecov](../Images/96a97a0b15ee433ed908102d0da5ea56.png)](https://codecov.io/gh/swashata/wp-webpack-script)[![npm version](../Images/43d3558555fe6f2c5cebefe814150873.png)](https://badge.fury.io/js/%40wpackio%2Fscripts)[![npm download](../Images/7dd6628686d958bffd89041a67b5efb1.png)](https://www.npmjs.com/package/@wpackio/scripts)[![cypress dashboard](../Images/6b1690b0b7a1cc9ecd2a03c6d0266c4f.png)](https://dashboard.cypress.io/#/projects/r3p1vm/runs)

## 什么是 wpack.io？

简单地说，wpack.io 是一个基于 nodejs 的构建工具，可以简化在 WordPress 主题和插件中使用现代 javascript 的过程。它为开发者提供了良好的体验，并为你所有的捆绑需求提供了单一的依赖。

> 这是一个专门为 WordPress 主题和插件开发做的微调的 web pack/浏览器同步配置。

随着 Gutenberg editor 的兴起，现代 JavaScript 和 react 等库的使用迫在眉睫。该工具的目标是:

*   <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/2705.png">✅</g-emoji> 提供所有前端资产的现成编译和捆绑。
*   <g-emoji class="g-emoji" alias="white_check_mark" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/2705.png">✅</g-emoji> 提供一流的开发人员体验(DX)
    *   热模块更换和实时重装。
    *   保存时编译文件。
    *   在任何本地开发服务器上工作。
*   ✅支持现代有用的概念，如模块、树摇动、动态导入等。

并消除棘手问题，例如:

*   ❌ 样板文件…

</article>

[View on GitHub](https://github.com/swashata/wp-webpack-script)

一些功能包括:

👉支持开箱即用的热重装。
👉动态公共路径，由一个基于 WP 的 PHP 类生成，所以使用 import()，尽你所能拆分代码。
👉可以与任何本地服务器(vvv，wamp，mamp，docker)一起工作。
👉多编译器支持。
👉Browsersync 和 webpack 开发和热中间件的混合体，finest DX。
👉零配置、ES6+、reactjs、Sass、类型脚本支持。

我请求，如果你对 WordPress 开发感兴趣，请尝试一下。我还没有发布，所以你需要克隆它(有点痛苦)并运行这个例子。

你需要有`docker`，最好有`yarn`。

```
git clone git@github.com:swashata/wp-webpack-script.git
yarn
yarn build
cd examples/plugin
docker-compose up -d && docker-compose logs -f wordpress
yarn exstart 
```

我非常希望在完成第一版之前得到反馈😃。