# 构造 React 原生 git 项目的最佳方式？

> 原文：<https://dev.to/elaziziyoussouf/react-native-best-git-structure-and-workflow--53hf>

嗨，开发者们，在过去的几周里，我一直在做一个 react native 项目，在这一点上，我想我需要认真思考一下构建我的 git repo 的最佳方式，尤其是在实现了一个用于发布 App 的 circleCi 之后。

如你所知，我们选择 react native 来构建跨平台应用程序(android 和 ios ),因此我们有多种构建类型

*   android 测试版(部署在 fabric.io 上)
*   IOS 测试版和生产版(使用 testflight)
*   android 产品版本(google play)

CircleCI 只能基于分支运行构建。

我正在寻找构建我的 git repo(分支)和 git 工作流的最佳方式。