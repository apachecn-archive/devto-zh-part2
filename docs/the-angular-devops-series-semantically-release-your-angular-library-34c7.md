# Angular DevOps 系列:语义上释放你的 Angular 库

> 原文：<https://dev.to/angular/the-angular-devops-series-semantically-release-your-angular-library-34c7>

# Angular devo PS 系列:语义释放你的 Angular 库

你是不是和我一样，厌倦了手动发布你的 Angular 库？如何保持更新日志呢？在这篇文章中，我将带你一起踏上我的 ngx 测试库的全自动化过程之旅！

## 棱角分明的 DevOps 系列

本帖是 Angular DevOps 系列的第一帖。该系列还将包括另一篇由 Angular 深度作者 Todd Palmer 撰写的帖子，他将带您详细了解如何使用 Travis CI 将 Angular 应用程序部署到 GitHub pages。

## 简介

作为一点背景知识，ngx-testing-library 是用 ng generate library 命令创建的，它包含了库中编写的测试以及使用它的示例应用程序中的测试。为了构建和测试我的更改，我已经使用 CircleCI 作为我的持续集成(CI)服务器。

在我们开始之前，让我们列出在推送提交后我们试图完成的任务:

🔲仅在构建通过时发布
🔲仅在对主分支
进行推送时释放🔲向国家预防机制发布新版本
🔲发布新版本到 GitHub
🔲保持 date️的更新日志

[阅读更多...](https://blog.angularindepth.com/the-angular-devops-series-semantically-release-your-angular-library-7d78afb4c845)