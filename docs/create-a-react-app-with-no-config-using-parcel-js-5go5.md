# 使用 Parcel js 创建一个没有配置的 React 应用程序

> 原文：<https://dev.to/sait/create-a-react-app-with-no-config-using-parcel-js-5go5>

您是 Reactjs 的新手，还是仍然对使用 web pack 配置感到困惑？

对于新手来说，react webpack 代码看起来很难理解，所以今天我将展示如何使用 [Parceljs](https://parceljs.org/api.html) 来构建 react 应用。

**Parceljs 也是一个类似于 webpack 的模块捆绑器，通过使用 Parceljs
你可以从零开始构建一个 react app，无需任何配置。**

众所周知，首先我们需要安装一些软件包

```
mkdir react-parcel
cd react-parcel 
```

Enter fullscreen mode Exit fullscreen mode

```
npm init -y
npm i --save-dev parcel-bundler
npm i --save react react-dom 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要安装一些 babel 包。