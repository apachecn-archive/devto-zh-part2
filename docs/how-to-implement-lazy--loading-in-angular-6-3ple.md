# 如何在 Angular 中实现延迟加载

> 原文：<https://dev.to/sait/how-to-implement-lazy--loading-in-angular-6-3ple>

Angular CLI 是一个命令行界面工具，可以创建项目、添加文件和执行各种正在进行的开发任务，如测试、捆绑和部署。

如果您需要更新 Angular CLI，可以通过重新安装来轻松更新。

```
npm install -g @angular/cli 
```

Enter fullscreen mode Exit fullscreen mode

```
ng new PROJECT-NAME
cd PROJECT-NAME
ng serve --open 
```

Enter fullscreen mode Exit fullscreen mode

ng new 用于生成有角度的样板文件。

为了在 Angular 中实现延迟加载，我们需要创建一个路由模块和一个组件模块，如下图所示。

[![folder structure](../Images/3e6f1715c0a87a2d5ad28fbac7d9b214.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FdjpFxx5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m62asv5gbg40jriivt4t.png)

在上图中，你见过 **posts-routing.module.ts 和 posts.module.ts** 吗

现在让我们看看我们需要编码什么。

#### post-routing . module . ts