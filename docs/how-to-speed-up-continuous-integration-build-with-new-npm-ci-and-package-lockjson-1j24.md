# 如何用新 NPM CI 和包锁. json 加速持续集成构建

> 原文：<https://dev.to/tomastrajan/how-to-speed-up-continuous-integration-build-with-new-npm-ci-and-package-lockjson-1j24>

尽管很有争议，最近的 npm 版本 5.7.0 带来了一些惊人的特性，这些特性将对您的开发工作流产生显著的积极影响！

> 新的 npm ci 命令仅从您的锁定文件安装。如果您的 package.json 和您的锁文件不同步，那么它将报告一个错误。它的工作原理是扔掉你的 node_modules 并从头开始重新创建它。除了保证您只能获得锁定文件中的内容之外，它的速度也快得多(2-10 倍！)而不是 npm 安装

# 生成并使用 package-lock.json 文件

我很难猜测已经有多少开发人员在使用 package-lock.json。当第一次介绍时，它的表现并不直观，我想很多人为了防止不必要的麻烦而不断删除这个文件。

[阅读更多...](https://medium.com/@tomastrajan/how-to-speed-up-continuous-integration-build-with-new-npm-ci-and-package-lock-json-7647f91751a)