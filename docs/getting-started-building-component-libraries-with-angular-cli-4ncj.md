# 开始使用 Angular CLI 构建组件库

> 原文：<https://dev.to/nieds/getting-started-building-component-libraries-with-angular-cli-4ncj>

[![](img/49689e51e9fd4b860e47195812362ed7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J4UkNc5G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIQ9QA3Sy1kX7XdmZmFwlYg.jpeg) 

<figcaption>照片由[塞缪尔·泽勒](https://unsplash.com/photos/JuFcQxgCXwA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于 [Unsplash](https://unsplash.com/search/photos/package?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

在 Angular 生态系统中，进入门槛一直较高的事情之一是创建其他 Angular 应用程序可以使用的组件库。Angular 团队发布了类似于 [Angular Package Format](https://docs.google.com/document/d/1CZC2rcpxffTDfRDs6p1cfbmKNLA6x5O-NtkJglDaBVs/edit) 的指南，并由此社区创建了一些奇妙的工具，如[生成器-Angular 2-库](https://github.com/jvandemo/generator-angular2-library)，使之变得更容易。然而，从来没有一种工具可以像在正常项目工作流程中使用 Angular CLI 的开发人员所习惯的那样，生成 Angular 包格式的自以为是的实现。

随着 Angular CLI 版本 6 的最新发布，我们现在可以访问一些工具来帮助我们构建库，同时还可以利用 CLI 的其他强大功能，例如 schematics，而无需离开我们习惯的工作流！我将向您展示如何通过 CLI 开始构建您自己的组件库。

我们将涵盖:

*   使用 Angular CLI 生成库项目
*   为您的库构建组件
*   在其他应用程序中使用您的库
*   发布您的库供他人使用

### 用 Angular CLI 生成一个库项目

最重要的是。我们需要建立我们的项目。如果您没有最新版本的 CLI，请从 npm 获取。