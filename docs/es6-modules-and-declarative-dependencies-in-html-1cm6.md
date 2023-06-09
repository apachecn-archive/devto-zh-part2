# HTML 中的 ES6 模块和声明性依赖关系

> 原文：<https://dev.to/ibrahimtanyalcin/es6-modules-and-declarative-dependencies-in-html-1cm6>

# ES6 模块和 HTML 中的声明性依赖

[![Cables](img/c5926e3bf59d4462db17068ef9abf6b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EcU2a2I6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/750/1%2AMWzgHGuxOoiH8RSxHap1eA.jpeg)

> 这是我之前工作的后续跟进:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[Ibrahim 对话框](https://github.com/IbrahimTanyalcin) / [ taskq](https://github.com/IbrahimTanyalcin/taskq)

### 异步模块加载器，具有 HTML 中的简化依赖关系管理以及与 ES6 导入/导出的兼容性

<article class="markdown-body entry-content container-lg" itemprop="text">

# 工作

[![Build Status](img/7c0086a7f0145ba8bea30f736959f39f.png)](https://travis-ci.org/IbrahimTanyalcin/taskq)[![Patreon donate](img/8d632e583dadcc725119186722213124.png)](https://www.patreon.com/ibrahimTanyalcin "Patreon donate")[![Codacy Badge](img/914ce24a6e2ba6da9d81c52abf0b3d0e.png)](https://www.codacy.com/app/IbrahimTanyalcin/taskq?utm_source=github.com&utm_medium=referral&utm_content=IbrahimTanyalcin/taskq&utm_campaign=Badge_Grade "Codacy")[![Npm Badge](img/66681017d5691b66df098d7af7263c58.png)](https://www.npmjs.com/package/@ibowankenobi/taskq "Npm")[![DOI](img/ef6c9ee8b8e1ebf6a9563a182bb3a450.png)](https://zenodo.org/badge/latestdoi/108156384)

* * *

# 支持带控制流的 ES5 和 ES6 的异步模块

## ⇩第一，你的 1 分钟小抄⇩

[![cheatsheet](img/127e23d00f058d08640b88d940978613.png)T2】](https://raw.githubusercontent.com/IbrahimTanyalcin/taskq/master/./cheatsheet.png)

[![](img/551aee0b2ed044485be852cb87230e5c.png)T2】](https://camo.githubusercontent.com/328d32d2b883c9a63de6392c8c8102587721db0d96e6ebf7bb7e5e7ebf7f2631/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f362f36612f44657461696c5f6465725f52656368656e6d61736368696e655f766f6e5f4a6f68616e6e5f48656c66726963685f4d2543332542436c6c65722e6a7067)

# 航行

如果你愿意你可以直接跳到 [**的例子**](https://raw.githubusercontent.com/IbrahimTanyalcin/taskq/master/#examples-) 。

*   ## [Advantage](https://raw.githubusercontent.com/IbrahimTanyalcin/taskq/master/#advantages-)

*   ## [API](https://raw.githubusercontent.com/IbrahimTanyalcin/taskq/master/#api-)T3】

*   ## [reading](https://raw.githubusercontent.com/IbrahimTanyalcin/taskq/master/#reading-)

*   ## What does it do?

*   ## [Uses](https://raw.githubusercontent.com/IbrahimTanyalcin/taskq/master/#usage-)

*   ## [Change log](https://raw.githubusercontent.com/IbrahimTanyalcin/taskq/master/#changelog-)

*   Example

    ## 

## 优势 [⏎](https://raw.githubusercontent.com/IbrahimTanyalcin/taskq/master/#advantages)

*   0 依赖关系
*   不需要聚合填充
*   不需要运输文件。
*   没有配置文件等。
*   最小化时约 6kB
*   将致力于 ie9+。
*   会很好地配合你在页面中使用的其他技术/模式
*   非渲染分块
*   您可以暂停/恢复主线程
*   了解文档状态(隐藏、最小化等)。)
*   对所有导入脚本的执行进行细粒度控制。
*   将承诺与 requestAnimationFrame(rAF)结合使用。在较老的浏览器(ie 等)上退回到 rAF。
*   支持嵌套/单个动态导入。您的主线程将等待，直到一个模块完成动态导入其他模块。
*   支持 *then* ， *catch* 短语进行动态导入。
*   你可以直接做你用 ES6 做不到的事情

    ```
     taskq.load("./scriptDynamic1.js")
        .then(function(res){
            res.init
            setTimeout(function(){
    ```

    …

</article>

[View on GitHub](https://github.com/IbrahimTanyalcin/taskq)

它试图推理将您的依赖项在 HTML 中声明为属性会是什么样子。

[**Taskq.js**](http://taskq.ibrahimtanyalcin.com/) 不是新的，我在 2017 年 10 月发布了它，从那以后我一直用它来维护一个相当大的项目，并在我认为合适的时候给它添加新的东西。

您可能知道，js 领域最热门的争论之一是模块加载器/打包器的比较以及它们之间的优势。问题很明显:它们彼此不兼容，至少在没有组装另一个“中间人”构建时工具的情况下是不兼容的。

模块的消费者不想处理依赖关系管理是可以理解的，但是如果你想想你自己的项目，你总是想保留一些应用程序的部分，如果不是完全控制的话。你可以捆绑你的应用程序的某些部分，或者做任何你想做的事情，同时仍然可以控制新添加部分的流程。这就是 Taskq 的设计目的，创建一个未来兼容的(与 ES6 导入/导出相结合)模块系统，该系统使用异步脚本(parallel + HTTP2 减轻了对捆绑的需求)运行，并让您控制它们的执行顺序，同时减少您管理依赖关系的工作(指向依赖关系树中的关键叶，Taskq 将完成其余部分)。

今天我想做一个小练习，我们将有一个最小的 html，有 3 个相互依赖的异步脚本，我们将在 HTML 中嵌入依赖关系(ie11 的版本将会更详细)。您可以通过添加几个具有逗号分隔的几层依赖关系的脚本来使这变得复杂，但我在这里将保持简单。这是我们的 html: