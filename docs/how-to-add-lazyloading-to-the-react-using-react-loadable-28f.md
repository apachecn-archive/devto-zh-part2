# 如何添加🦄反应迟钝🔔使用 React Loadable

> 原文：<https://dev.to/sait/how-to-add-lazyloading-to-the-react-using-react-loadable-28f>

React Loadable 使延迟加载变得非常容易，这样你就不必担心下载大量的字节。React Loadable 将你的代码分割成块，这样你就可以只提供需要的字节。

首先克隆我的反应路由器样板文件

```
git clone git@github.com:saigowthamr/React-router-v4-boilerplate.git reactlazy

cd reactlazy

npm  i // to install dependencies

npm start // to start dev server 
```

Enter fullscreen mode Exit fullscreen mode

我已经将头文件和路由器配置文件清楚地分开，以便于阅读。

[![](img/f22c9c3d4ab08543c61b539e5870574d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fUBlQe3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s8i6n6hng7j05nidrnqr.png)

应用程序文件夹结构应该如上图所示。

现在让我们安装可反应加载的包

```
npm i --s react-loadable 
```

Enter fullscreen mode Exit fullscreen mode

在我们的样板文件中，我添加了两条路线。

但是这两个路径中没有内容，所以我现在在 app.js 文件中添加一些
虚拟内容。