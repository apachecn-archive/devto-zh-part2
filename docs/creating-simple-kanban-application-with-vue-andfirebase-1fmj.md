# 使用 Vue 和 Firebase 创建简单的看板应用程序

> 原文：<https://dev.to/juliancanderson/creating-simple-kanban-application-with-vue-andfirebase-1fmj>

* * *

[![](img/f9b695827bf68f5a2aa551436141ecf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pIU53Gmf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A2l82cBZyp1DgwybigOEQzw.png) 看板应用与 Vue 和 Firebase

# 使用 Vue 和 Firebase 创建简单的看板应用程序

在我们创建应用程序之前，我们应该知道我们想要用什么工具来构建应用程序。对于看板协作应用程序，我们需要一个具有可靠数据库的快速应用程序。当然，当我们想要协作时，我们需要一个实时数据库，这就是为什么我们使用 Firebase 作为我们的数据库。对于客户端，我们将使用 Vue JS。为了部署它，我们也将使用 Firebase 托管服务。

#### 什么是 Vue。JS？

> Vue(读作/vjuː/，类似视图)是一个用于构建用户界面的渐进式框架。与其他整体框架不同，Vue 从一开始就被设计成可增量采用的。核心库只关注视图层，很容易获取并与其他库或现有项目集成。另一方面，当与[现代工具](https://vuejs.org/v2/guide/single-file-components.html)和[支持库](https://github.com/vuejs/awesome-vue#components--libraries)结合使用时，Vue 也完全能够支持复杂的单页面应用程序。

#### 什么是 Firebase 实时数据库？

> 它为应用程序开发人员提供了一个 API，允许应用程序数据跨客户端同步并存储在 Firebase 的云上。

### 步骤:

#### 1。正在准备 Vue 应用程序

在构建应用程序时，我们将使用 Vue CLI 来加快开发速度。要安装 Vue CLI，您可以在终端中键入:

```
npm install -g @vue/cli# ORyarn global add @vue/cli 
```

完成安装 Vue CLI 后，我们现在可以通过键入以下命令来创建应用程序:

```
$ vue create 
```

您可以为您的应用程序使用任何您想要的名称，我将把我的命名为看板-firebase。当我们第一次创建项目时，我们需要设置一些东西。这是我对应用程序的配置:

[![](img/155a31fac42d886c42feda98497a5f1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_X_m4j5i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AVkyZdpDlv7NUBjhIDdggYg.png) 检视 JS 组态

创建应用程序可能需要一段时间，完成后它会在您的终端上显示出来。(我这里用的是纱线，不是 npm)

不要忘记在您的项目中安装 firebase:

```
cd kanban-firebase
yarn add firebase
or
npm install --save firebase
```

[![](img/8bc32875b5fc2c88d378aac0cbb94f04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ixbGJGjm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ABAa6pp3q_evb1OlB64O6cw.png) 创建完应用程序

恭喜你，你已经有了一个 Vue 应用程序

```
yarn serve
or
npm run serve
```

[![](img/2c560116a63c273be828515747e9390a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cyss7xGC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AkT0CysdYni2S-4viZIto9w.png) 本地主机上的 view js template:8080

恭喜你，你已经有了一个 Vue 应用程序

#### 2。正在准备 Firebase 数据库

我们需要建立的第二件事是来自 Firebase 的实时数据库。转到[https://console.firebase.google.com/](https://console.firebase.google.com/)并创建一个新项目。

[![](img/ce4d413af48168c2aacfa37211126153.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mTIzlXby--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AblxVWCssJRiovqmghHq_uw.png)

完成应用程序初始化后，进入数据库并选择实时数据库。并在测试模式下选择开始。然后在你的仪表板上点击网络。复制所有内容，并将配置文件放在 src/assets/config.js 中。

***(别忘了把这个配置放到你的。*git ignore file)**

[![](img/3c8bb318df76c45ebe3e7b6c137e3d94.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SaPH8p5M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ADNavHNp-e5PNG_0cXmN6gw.jpeg)

恭喜你，你的 Firebase 实时数据库已经运行了。

#### 3。准备 Vue 组件

接下来我们应该做的是构建我们需要的组件列表，这样组件就可以重用了。我将总共制作 3 个组件和 1 个视图组件来展示应用程序。组件将是:内容卡、看板卡和应用程序的主标题，视图组件是 home。

[![](img/ab8006d48a2fdc8254816bc6c3c49e8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3A2-lStF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A1H_5yIrjzQR5K5y2sGRNBw.png) 我的文件结构 [![](img/3fb113b78b6eefbb0c7f9efdc4cb1b86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IuHWRsU9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AzXjYs_fqt0qN_5nR6H6kTA.png) [![](img/b059d1868af57972f367a211dc875321.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vk6rqNWM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AvhQmd0Yj2vkAwu42QVrLtw.png) 左:看板卡，右:内容卡

#### **4。从 Firebase 获取数据**

接下来要做的是从 firebase 获取数据。有两种方法可以从 firebase 获取数据，你可以监听一次，也可以随着数据的变化监听数据。因为我们需要实时数据库，所以我们将使用。on()方法来获取数据，我将把数据放在 Home.vue 上。