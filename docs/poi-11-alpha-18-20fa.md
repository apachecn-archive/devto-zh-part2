# 兴趣点 11 阿尔法 18

> 原文：<https://dev.to/egoist/poi-11-alpha-18-20fa>

我刚刚发布了 Poi 11 alpha 18，这可能是最后一个“alpha”版本。

您现在可以开始创建一个新项目，如下所示:

```
npm i -g create-poi-app@next
create-poi-app my-app 
```

Enter fullscreen mode Exit fullscreen mode

或者一次性使用:

```
npx create-poi-app@next my-app 
```

Enter fullscreen mode Exit fullscreen mode

**请注意，我们所有的版本 11 包目前都发布在 npm 上的`@next`频道下。**

[![2018-09-20 11 50 33](img/1366a6afb68d038704dc370539047112.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MIly7npm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/8784712/45830567-054fdd00-bd30-11e8-85ae-421ea424477a.png)

如果您选择所有的默认值，上面的命令将在`my-app`文件夹中生成一个最小的项目:

```
my-app
├── package.json
├── poi.config.js
├── public
│   └── favicon.ico
├── src
│   ├── components
│   │   └── App.js
│   └── index.js
└── yarn.lock 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止的`poi.config.js`真的很简单，这里我们需要用`src/index.js`代替默认的`index.js`作为 app 入口:

```
// poi.config.js
module.exports = {
  entry: 'src/index.js'
} 
```

Enter fullscreen mode Exit fullscreen mode

在新创建的项目中，还有一些您可以运行的内置 npm 脚本:

*   `yarn start`:调用`poi dev`命令启动开发服务器运行你的应用。
*   `yarn build`:调用`poi build`命令来构建和优化您的应用程序以供生产使用。
*   `yarn lint`:(通过`@poi/plugin-eslint`选择`eslint`功能时可用)调用`poi lint`对应用程序文件运行 ESLint，可选允许您在文件保存时运行 ESLint。

在未来的 beta 版本中，我们计划添加`@poi/plugin-jest`等来注入`poi test`命令进行单元测试。

* * *

## 为 Poi 10 用户

v10 和 v11 之间的主要差异列表:

*   现在您运行`poi dev`而不是`poi`来启动开发服务器
*   为了简化，对内部 API 进行了大量更改
*   生成器支持，您现在可以使用 Poi 来搭建一个新项目或向现有项目添加文件。
*   ...等等

* * *

欲了解更多信息，请:

*   见我们的网站(WIP):[https://poi.leptosia.org](https://poi.leptosia.org)
*   在推特上关注我们:[https://twitter.com/poi__js](https://twitter.com/poi__js)