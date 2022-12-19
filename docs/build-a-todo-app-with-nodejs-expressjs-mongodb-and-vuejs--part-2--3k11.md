# 使用节点构建 Todo 应用程序。Js、ExpressJs、MongoDB 和 vue js——第 2 部分

> 原文：<https://dev.to/abiodunjames/build-a-todo-app-with-nodejs-expressjs-mongodb-and-vuejs--part-2--3k11>

在本教程的第 1 部分中，我们为一个简单的 todo 应用程序构建了 API，现在我们将前端和 VueJS 放在一起。如果你是 VueJs 新手，不要担心。我写了[VueJs:4 分钟基础](https://codeburst.io/vuejs-the-basics-in-4-mins-6208df76003d)和[在 VueJs 中创建你的第一个组件](https://codeburst.io/building-your-very-first-component-with-vuejs-21b4a2f6a15a)来帮助你立刻学会 vue js。

#### 项目目录

在第 1 部分中，我们创建了`backend`目录。`backend`目录包含我们后端代码的源代码。

我们会做一些类似的事情。让我们创建一个名为`frontend`的新目录。这将存放我们的前端代码。

```
$ mkdir frontend 
```

如果您运行上面的命令，您的项目目录现在应该看起来像这样:

```
.
├── backend
└── frontend 
```

我们在这篇文章中的所有代码都将放到`frontend`目录中。

#### 检视 CLI

Vue CLI 是一个命令行工具，可以帮助你快速搭建一个新项目。要安装 Vue CLI，请从您的终端运行以下命令:

```
$ npm install -g @vue/cli 
```

安装了 Vue Cli 后，转到`frontend`目录，从命令运行`vue create .`来搭建一个新项目。

```
$ vue create . 
```

确保对所有提示都回答*是*。

```
Vue CLI v3.5.1
? Generate project in current directory? Yes

? Please pick a preset: default (babel, eslint) 
```

如果一切顺利，你的前端目录应该是这样的:

```
├── README.md
├── babel.config.js
├── node_modules
├── package-lock.json
├── package.json
├── public
│   ├── favicon.ico
│   └── index.html
└── src
    ├── App.vue
    ├── assets
    │   └── logo.png
    ├── components
    │   └── HelloWorld.vue
    └── main.js 
```

### 项目依赖关系

1.  Bootstrap Vue:一个兼容 Vue 的 Bootstrap 框架
2.  [Sass 加载器](https://github.com/webpack-contrib/sass-loader):将 Sass 编译成 css
3.  [Axios](https://github.com/axios/axios) :用于对 todo API 进行 rest API 调用

使用命令安装 bootstrap-vue 和 axis:

```
$ npm install vue bootstrap-vue bootstrap axios 
```

使用命令安装 sass-loader:

```
$ npm install sass-loader node-sass --save-dev 
```

在下面的段落中，我们将创建这个项目所需的组件。

### 创建 Vue 组件

基本上，我们需要 2 个主要的 vue 组件。第一个组件将是`CreateTodo`，第二个组件将是`ListTodo`

在某些时候，这些组件需要相互通信或共享数据，这就是事件总线发挥作用的地方。

在 Vue 中处理组件间通信的方法之一。Js 使用一个全局事件总线，这样当一个组件发出一个事件时，一个事件总线将这个事件传输到其他监听组件。

#### 事件总线

我们创建一个名为`src/bus.js`的全局事件总线，代码为:

```
//src/bus.js

import Vue from 'vue';

const bus = new Vue();
export default bus; 
```

现在我们已经创建了一个事件总线，让我们编写添加新 todo 项的代码。

#### 用于添加新待办事项的 Vue 组件

在`src/components/CreateTodo.vue`创建一个新文件，并用
更新其内容

```
 <template>
  <div class="col align-self-center">
    <h3 class="pb-5 text-left underline">Create todos</h3>
    <form class="sign-in" @submit.prevent>
      <div class="form-group todo__row">
        <input
          type="text"
          class="form-control"
          @keypress="typing=true"
          placeholder="What do you want to do?"
          v-model="name"
          @keyup.enter="addTodo($event)"
        />
        <small class="form-text text-muted" v-show="typing">Hit enter to save</small>
      </div>
    </form>
  </div>
</template>
<script>
import axios from "axios";
import bus from "./../bus.js";

export default {
  data() {
    return {
      name: "",
      typing: false
    };
  },
  methods: {
    addTodo(event) {
      if (event) event.preventDefault();
      let todo = {
        name: this.name,
        done: false //false by default
      };
      console.log(todo);
      this.$http
        .post("/", todo)
        .then(response => {
          this.clearTodo();
          this.refreshTodo();
          this.typing = false;
        })
        .catch(error => {
          console.log(error);
        });
    },

    clearTodo() {
      this.name = "";
    },

    refreshTodo() {
      bus.$emit("refreshTodo");
    }
  }
};
</script>
<style lang="scss" scoped>
.underline {
  text-decoration: underline;
}
</style> 
```

*   一旦按下`enter`键，就会执行`addTodo()`。它用新的待办事项向后端发出一个`POST`请求。
*   `clearTodo()`保存待办事项后，清除输入框。
*   `refreshTodo()`发出一个事件`refreshTodo`。这在添加新的 todo 项目时很有用。重新呈现列表以便显示新项目是有意义的。

说明之后，让我们继续创建`ListTodo`组件。

#### 用于列出待办事项的组件

用代码
创建一个文件`src/components/ListTodo.vue`

```
<template>
  <div v-bind:show="todos.length>0" class="col align-self-center">
    <div class="form-row align-items-center" v-for="todo in todos">
      <div class="col-auto my-1">
        <div class="input-group mb-3 todo__row">
          <div class="input-group-prepend">
            <span class="input-group-text">
              <input
                type="checkbox"
                v-model="todo.done"
                :checked="todo.done"
                :value="todo.done"
                v-on:change="updateTodo(todo)"
                title="Mark as done?"
              />
            </span>
          </div>
          <input
            type="text"
            class="form-control"
            :class="todo.done?'todo__done':''"
            v-model="todo.name"
            @keypress="todo.editing=true"
            @keyup.enter="updateTodo(todo)"
          />
          <div class="input-group-append">
            <div class="input-group-text">
              <span
                class="input-group-addon addon-left"
                title="Delete todo?"
                v-on:click="deleteTodo(todo._id)"
              >
                X
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div
      class="alert alert-primary todo__row"
      v-show="todos.length==0 && doneLoading"
    >Hardest worker in the room. No more todos now you can rest. ;)</div>
  </div>
</template>

<script>
import axios from "axios";
import bus from "./../bus.js";

export default {
  data() {
    return {
      todos: [],
      doneLoading: false
    };
  },
  created: function() {
    this.fetchTodo();
    this.listenToEvents();
  },
  watch: {
    $route: function() {
      let self = this;
      self.doneLoading = false;
      self.fetchData().then(function() {
        self.doneLoading = true;
      });
    }
  },
  methods: {
    fetchTodo() {
      this.$http.get("/").then(response => {
        this.todos = response.data;
      });
    },

    updateTodo(todo) {
      let id = todo._id;
      this.$http
        .put(`/${id}`, todo)
        .then(response => {
          console.log(response);
        })
        .catch(error => {
          console.log(error);
        });
    },

    deleteTodo(id) {
      this.$http.delete(`/${id}`).then(response => {
        this.fetchTodo();
      });
    },

    listenToEvents() {
      bus.$on("refreshTodo", $event => {
        this.fetchTodo(); //update todo
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.todo__done {
  text-decoration: line-through !important;
}

.no_border_left_right {
  border-left: 0px;
  border-right: 0px;
}

.flat_form {
  border-radius: 0px;
}

.mrb-10 {
  margin-bottom: 10px;
}

.addon-left {
  background-color: none !important;
  border-left: 0px !important;
  cursor: pointer !important;
}

.addon-right {
  background-color: none !important;
  border-right: 0px !important;
}
</style> 
```

让我们花点时间解释一下代码中发生了什么。

我们在代码片段中创建了 4 个函数。

*   `fetchTodo()`调用`GET`后端并获取所有待办事项。
*   当您更改待办事项并按回车键时，会调用`updateTodo(todo)`。它会将您的更改转发到后端。
*   `deleteTodo(id)`点击回收站按钮时运行。它向后端发出`DELETE`请求。
*   `listenToEvents()`:在`CreateTodo`组件中，当一个新的 todo 项目被添加到列表中时，我们发出事件。`ListTodo`负责渲染待办事项。这个方法负责监听`refreshTodo`事件。

#### App 组件

下面我们将所有的组件包装在一个名为`App.vue`的父组件中。用以下内容更新文件`src/App.vue`:

```
<template>
  <div class="container">
    <div class="row vertical-centre justify-content-center mt-50">
      <div class="col-md-6 mx-auto">
        <CreateTodo></CreateTodo>
        <ListTodo></ListTodo>
      </div>
    </div>
  </div> </template> 
<script>
import CreateTodo from "./components/CreateTodo.vue";
import ListTodo from "./components/ListTodo.vue";

export default {
  name: "app",
  components: { CreateTodo, ListTodo }
};
</script> 
<style lang="scss">
@import "node_modules/bootstrap/scss/bootstrap";
@import "node_modules/bootstrap-vue/src/index.scss";
.vertical-centre {
  min-height: 100%;
  min-height: 100vh;
  display: flex;
  align-items: center;
}
.todo__row {
  width: 400px;
}
</style> 
```

#### 根实例

必须为每个 vue 应用程序定义一个根实例。您可以将 Vue 实例或根实例视为构成我们应用程序的组件树的根。

让我们用
修改`src/main.js`文件的内容

```
import Vue from 'vue';
import BootstrapVue from 'bootstrap-vue';
import axios from 'axios';
import App from './App.vue';

const http = axios.create({
  baseURL: process.env.BACKEND_URL ? process.env.BACKEND_URL : 'http://localhost/todos',
});

Vue.prototype.$http = http;

Vue.use(BootstrapVue);

Vue.config.productionTip = false;

new Vue({
  render: (h) => h(App),
}).$mount('#app'); 
```

我们导入了 BoostrapVue 和应用程序所需的其他库。
我们还导入了`App`组件，并将其定义为根实例上的一个组件。

我们导入了一个 http 客户端`axios`，并配置了后端应用程序的基本 url。您应该确保`baseUrl`与您的后端 URL 匹配。

我们已经走了这么远，用:
运行应用程序

```
$ npm run serve 
```

可能需要一些时间来构建。最后，您应该在控制台中有一个 URL printend:

```
App running at:
  - Local:   http://localhost:8080/ 
  - Network: http://192.168.178.20:8080/
  Note that the development build is not optimized.
  To create a production build, run npm run build. 
```

如果你导航到`http://localhost:8080`，你应该会看到这样一个页面。
[![Todo App](../Images/b8943d4a80133669bb502b26dc6d7486.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wt2jm-SR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/samueljames/image/upload/v1570888955/Screenshot_2019-10-12_at_16.01.32.png)

要连接前端应用程序和后端，您还需要启动后端服务器。

导航到`backend`目录并运行

```
$ npm start 
```

#### 注:

1.  您的 MongoDB 连接 URL 必须在`backend/config/Config.js`中正确配置，并且 MongoDB 必须正在运行。
2.  您的后端服务器必须正在运行。
3.  您的前端服务器必须正在运行。

如果你导航到 [http://localhost:8080](http://localhost:8080) ，你会看到这样一个页面。【T2![](../Images/cc7ca7e0a98822ac5f976e6b0297e456.png)

[在此获取源代码](https://github.com/abiodunjames/NodeJs-Todo-List)