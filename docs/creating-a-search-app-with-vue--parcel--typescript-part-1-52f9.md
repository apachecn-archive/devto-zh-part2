# 使用 Vue.js + Parcel + TypeScript 创建搜索应用程序:第 1 部分，共 3 部分

> 原文：<https://dev.to/scottlepp/creating-a-search-app-with-vue--parcel--typescript-part-1-52f9>

在我之前的文章[这里](https://dev.to/scottlepp/extending-traditional-software-with-serverless-microservices-442m)中，我展示了如何“观察”AWS S3 桶的变化并实时更新我们的搜索索引。现在让我们使用 [vue.js](https://vuejs.org/) 、[包裹](https://parceljs.org/)、[打字稿](https://www.typescriptlang.org/)和 [sass](https://sass-lang.com/) 编写一个应用程序来显示我们的搜索结果

### 第 1 部分:应用程序设置

在这一部分，我将展示如何快速设置我们的应用程序，这在很大程度上要归功于 parcel . js:[https://parceljs.org/](https://parceljs.org/)

先决条件:安装 node . js:[https://nodejs.org/en/](https://nodejs.org/en/)

我们将安装什么:

*   包裹-我们的“零配置”捆扎机
*   vue.js -我们的 ui 框架
*   typescript——我们将在这里使用的编码语言
*   sass——我们的风格语言

让我们准备好摇滚吧。

**第一步**。创建一个本地目录来存放您的项目(我假设您知道如何做)。

**第二步**。安装包裹

*   打开一个终端/命令提示符和 cd 到您的新目录
*   粘贴或键入以下内容:`npm install -g parcel-bundler`

**第三步**。设置依赖关系。在新文件夹中创建一个 package.json 文件。这定义了我们所有的依赖关系。package 可以尝试为您做到这一点(尽管它对我不太适用)。相反，这里是你需要的文件。只需将内容复制到您的新包中。