# 如何使用新的反应上下文 API 详细

> 原文：<https://dev.to/sait/how-react-context-api-works-detailed-41gk>

[![context api](../Images/4175bd2a8042f1e5f6d2f7c5bfb78bf0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1Tt8lGd7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w3z7iicoq435jopfx2lh.png)

React16.3 中提供了 react 上下文 API，因此让我们使用
上下文 API 构建一个计数器。

Context API 帮助我们将数据传递给组件，而不需要在每个组件上都使用 props。

在我们的柜台 app 里，我们只向下传递一级。因此，让我们使用 create-react-app
安装一个 react 应用程序

```
npm install -g create-react-app
create-react-app newContext
cd newContext
npm start  //to start dev server 
```

Enter fullscreen mode Exit fullscreen mode

现在用代码编辑器打开。

让我们创建一个新的文件 context.js 添加这些代码。