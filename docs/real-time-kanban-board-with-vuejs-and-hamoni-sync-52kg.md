# 带有 Vue.js 和 Hamoni Sync 的实时看板板

> 原文：<https://dev.to/pmbanugo/real-time-kanban-board-with-vuejs-and-hamoni-sync-52kg>

[看板](https://leankit.com/learn/kanban/kanban-board/)是一个工作流程可视化工具，可以让你优化工作流程。在这篇文章中，我将向您展示如何在 Vue 中构建实时看板。

要继续学习，您需要了解一些关于 [Vue](https://vuejs.org/) 和它的[单个文件组件](https://vuejs.org/v2/guide/single-file-components.html)的知识。您还需要以下工具:

1.  Node.js & npm(点击此[链接](https://nodejs.org/)为您的操作系统下载安装程序)
2.  Vue CLI。如果你没有这个，在命令行中运行`npm install -g vue-cli`来安装它。

如果你没有关于 Vue 的知识，你可以在继续这篇文章之前阅读我的前一篇文章。

# 设置项目

我们将使用 Vue CLI 设置项目，这是一个用于搭建 Vue.js 项目的简单命令行界面(CLI)。打开终端，运行`vue init webpack-simple realtime-kanban-board`。你会得到一些提示，当询问你是否想使用`saas`时，按下回车键接受默认值并选择`y`。在运行该命令之前，您可以将`realtime-kanban-board`更改为您喜欢的项目名称。设置完成后，运行以下命令从脚手架模板安装依赖项:

```
cd realtime-kanban-board
npm install 
```

我们将使用 [vue-kanban](https://github.com/BrockReece/vue-kanban) 作为具有拖放功能的看板组件。我们还将使用 [Hamoni Sync](https://www.hamoni.tech/) 来实时存储和同步应用程序状态。它们都在 npm 上，我们将通过运行以下命令来安装它们:

```
npm install vue-kanban hamoni-sync 
```

我们需要一些额外的包裹给巴别塔。通过运行以下命令来安装它们:

```
npm install --save-dev babel-plugin-transform-runtime babel-preset-stage-2 
```

# 添加视图看板

我们已经搭建了应用程序，现在它包含了运行应用程序所必需的文件。用文本编辑器(或 IDE)打开文件`src/App.vue`,并用以下内容替换其内容:

```
<template>
  <div id="app">
    <kanban-board :stages="stages" :blocks="blocks" @update-block="updateBlock">
      <div v-for="block in blocks" :slot="block.id" :key="block.id">
        <div>
          <strong>id:</strong> {{ block.id }}
        </div>
        <div>
          {{ block.title }}
        </div>
      </div>
    </kanban-board>
  </div>
</template>

<script>
import Vue from "vue";
import vueKanban from "vue-kanban";
import Hamoni from "hamoni-sync";

Vue.use(vueKanban);

export default {
  name: "app",
  data() {
    return {
      stages: ["on-hold", "in-progress", "needs-review", "approved"],
      blocks: [],
      listPrimitive: null
    };
  },
  methods: {
    updateBlock(id, status) {
      //TODO: add code to update the block
    }
  },
};
</script>

<style lang="scss">
@import "./assets/kanban.scss";
</style> 
```

上面的代码使用了带有两个道具`stages`和`blocks`的`kanban-board`组件。`stages`道具定义了板子的列，`blocks`包含了代表放置在列上的任务项的数据。当块从一个阶段移动到另一个阶段时，触发`@update-block`事件。我们将利用这一事件实时更新公告板。

# Add Hamoni Sync

我们已经添加了必要的代码来将看板呈现到页面上。在本节中，我们将设置 [Hamoni Sync](https://www.hamoni.tech/) 来存储和同步`blocks`的数据。Hamoni Sync 是一项让您能够实时同步应用程序状态的服务。

要使用 Hamoni 同步，我们需要初始化它并连接到 Hamoni 服务器。为此，我们需要一个帐户和应用程序 ID。按照以下步骤在 Hamoni 中创建一个应用程序。

1.  注册并登录 Hamoni [仪表盘](https://dashboard.hamoni.tech/)
2.  在文本字段中输入您首选的应用程序名称，然后单击创建按钮。这将创建应用程序，并将其显示在应用程序列表部分。
3.  展开**账户 ID** 卡获取您的账户 ID

[![Hamoni dashboard](img/4e41967431b35548bc44f1dd5b14ddc5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yUCiqUkM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.hamoni.tech/assets/dashboard.png)

将以下代码添加到第 35 行的`App.vue`。

```
mounted: async function() {
  const accountId = "YOUR_ACCOUNT_ID";
  const appId = "YOUR_APP_ID";
  let hamoni;

  const response = await fetch("https://api.sync.hamoni.tech/v1/token", {
    method: "POST",
    headers: {
      "Content-Type": "application/json; charset=utf-8"
    },
    body: JSON.stringify({ accountId, appId })
  });

  const token = await response.json();
  hamoni = new Hamoni(token);

  hamoni
    .connect()
    .then(() => {
    hamoni
        .get("board")
        .then(listPrimitive => {
          this.listPrimitive = listPrimitive;
          this.blocks = listPrimitive.getAll();
          listPrimitive.onItemUpdated(item => {
          this.blocks.splice(item.index, 1, item.value);
          });
        })
        .catch(error => {
          if (error == "Error getting state from server") {
          hamoni
            .createList("board", blocks)
            .then(listPrimitive => {
              this.listPrimitive = listPrimitive;
              this.blocks = listPrimitive.getAll();
              listPrimitive.onItemUpdated(item => {
              this.blocks.splice(item.index, 1, item.value);
              });
            })
            .catch(console.log);
          }
        });
  });
  } 
```

在结束标签`</script>`前添加以下内容:

```
const blocks = [
  {
    id: 0,
    status: "approved",
    title: "Buy coffee machine"
  },
  {
    id: 1,
    status: "in-progress",
    title: "Find better AirBnB options"
  },
  {
    id: 2,
    status: "on-hold",
    title: "Approve Q3 budget"
  },
  {
    id: 3,
    status: "approved",
    title: "Travel to Colombia"
  },
  {
    id: 4,
    status: "needs-review",
    title: "Add Redux to the app"
  },
  {
    id: 5,
    status: "approved",
    title: "Well, Sleep all day 👩‍🎤"
  },
  {
    id: 6,
    status: "in-progress",
    title: "Find language exchange partner"
  }
]; 
```

我们通过调用从 Sync 的令牌 API 获取的令牌来初始化它。建议从后端调用同步令牌服务器，并将响应令牌发送到客户端应用程序。对于这个例子，我将把它们都放在一个地方。

将字符串占位符替换为您将从 Hamoni dashboard 获得的帐户和应用程序 ID。然后我们用`hamoni.connect()`连接了 app。我们通过调用存储在 Hamoni Sync 上的应用程序状态名称来检索数据。如果它还不存在，我们将创建它，然后添加一个函数，在项目更新时更新 Vue 的状态。这通过用代码调用`onItemUpdated()`来更新`item.index`上的**块**来实现。Hamoni Sync 提供了所谓的[列表原语](https://docs.hamoni.tech/list-primitive.html)，我们在这里通过调用`hamoni.createList()`并传递给它一个数组来使用它。

现在，当一个块从一个阶段移动到另一个阶段时，我们需要更新 Hamoni 上的状态。我们将更新之前添加的函数`updateBlock(id, status)`

```
methods: {
    updateBlock(id, status) {
        let block = this.blocks[id];
        this.listPrimitive.update(id, { id, title: block.title, status });
    }
}, 
```

这段代码告诉 sync 更新状态，一旦完成，它将同步到所有连接的客户端/设备。

这就是所有需要的代码。现在，通过从命令行运行`npm run dev`来运行应用程序，这将在您的默认浏览器中启动 web 应用程序。

[![realtime-kanban-vue.gif](img/4a1c9b9e857657d9a5d5ed187ee47b2a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p2EDzG14--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/HT9934KSAuY1lbqC0Zng)

就成了🚀实时看板。

# 就这么定了！

所以我们用 vue-kanban 和 Hamoni Sync 搭建了一个实时看板板。Hamoni Sync 可以轻松地将实时功能集成到您的应用程序中。你可以在 [GitHub](https://github.com/pmbanugo/realtime-kanban-vue) 上获得完整的代码。编码快乐！👍

# 资源

*   vista CLI
*   [vue . js 要领介绍](https://dev.to/pmbanugo/from-vanillajs-to-vuejs-a-guide-to-vue-essentials-5gii)
*   [vue 看板](https://github.com/BrockReece/vue-kanban)
*   [Hamoni Sync](https://www.hamoni.tech/)

> 感谢 [Andrew Hedges](https://dev.to/segdeha) 和 [Gift](https://twitter.com/lauragift21) 审阅这篇文章。