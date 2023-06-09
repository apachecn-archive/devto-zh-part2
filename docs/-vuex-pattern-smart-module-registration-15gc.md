# 🚩Vuex 模式:智能模块注册

> 原文：<https://dev.to/nkoik/-vuex-pattern-smart-module-registration-15gc>

Vue 经历:⚫️⚫️⚫️⚫️⚪️
Vuex 经历:⚫️⚫️⚫️⚫️⚫️

您是否尝试过管理的应用程序状态？由于分散在许多组件中的多种状态以及它们之间的交互，大型应用程序的复杂性通常会增加。所以，Vue 提供了 **Vuex** 但是正如官方[文件](https://vuex.vuejs.org/guide/modules.html)所说:

> Vuex 使用单一的状态树，我们应用程序的所有状态都包含在一个大对象中。然而，随着我们的应用程序规模的增长，商店会变得非常臃肿。

为此，Vuex 允许我们将商场分成多个模块。每个模块可以包含自己的状态、突变、动作、getters，甚至嵌套模块。

* * *

我想你已经被弄糊涂了，所以让我们进入代码。

```
# This is a classic store structure with modules
├── index.html
├── main.js
├── components
└── store
    ├── index.js    # where we assemble modules and export the store
    └── modules
        ├── auth.js
        ├── posts.js
        └── comments.js 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们有一个带有`index.js`的存储文件夹和一个名为`modules`的子文件夹，其中包含所有模块。但是模块注册会变得很乏味。
`index.js`中的`store/`

```
import Vue from 'vue'
import Vuex from 'vuex'
import auth from './modules/auth'
import posts from './modules/posts'
import comments from './modules/comments'
// ...

Vue.use(Vuex)
export default new Vuex.Store({
    modules: {
        auth,
        posts,
        comments,
        // ...
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

Vuex 模块脚手架示例。

```
export default {
  namespaced: true,
  state: {},
  getters: {},
  mutations: {},
  actions: {}
} 
```

Enter fullscreen mode Exit fullscreen mode

这是注册模块的标准方式。如果你知道什么是命名空间，继续。

> 默认情况下，模块内的动作、变异和 getters 仍然注册在全局名称空间下——这允许多个模块对相同的变异/动作类型做出反应。
> 如果你希望你的模块更加独立或者可重用，你可以用`namespaced: true`把它标记为命名空间

让我们看看 Chris Fritz(Vue 核心成员)在一篇 VueConf 中提到的模块注册。

🚩首先，让我们在`store/modules/`
中添加一个`index.js`文件

```
# This is our store structure with modules
├── index.html
├── main.js
├── components
└── store
    ├── index.js      # where we assemble modules and export the store
    └── modules
        ├── index.js  # this is the js file that solves the problem
        ├── auth.js
        ├── posts.js
        └── comments.js 
```

Enter fullscreen mode Exit fullscreen mode

🚩那我们来修改一下`store/modules/index.js`
中的这个`index.js`

```
import camelCase from 'lodash/camelCase'
// Storing in variable a context with all files in this folder
// ending with `.js`.
const requireModule = require.context('.', false, /\.js$/)
const modules = {}

requireModule.keys().forEach(fileName => {
    if (fileName === './index.js') return
    // filter fullstops and extension 
  // and return a camel-case name for the file
    const moduleName = camelCase(
        fileName.replace(/(\.\/|\.js)/g, '')
    )
  // create a dynamic object with all modules
    modules[moduleName] = {
    // add namespace here
        namespaced: true,
        ...requireModule(fileName).default
    // if you have exported the object with name in the module `js` file
    // e.g., export const name = {};
    // uncomment this line and comment the above
        // ...requireModule(fileName)[moduleName]
    }
})
export default modules 
```

Enter fullscreen mode Exit fullscreen mode

🚩让我们从每个模块 js 文件中删除命名空间。

```
// export const name = { if you want to export an object with name
export default {
  // namespaced: true,   delete this line 
    state: {},
    getters: {},
    mutations: {},
    actions: {}
} 
```

Enter fullscreen mode Exit fullscreen mode

🚩最后是上面的代码，在这里我们把所有要导入的模块都可以改成:`store/`中的
`index.js`中的

```
import Vue from 'vue'
import Vuex from 'vuex'
import modules from './modules'

Vue.use(Vuex)
export default new Vuex.Store({
    modules
}) 
```

Enter fullscreen mode Exit fullscreen mode

我认为我们已经做了一个“自动化”系统，包括模块文件夹中的每个文件。更智能、更干净的代码。

*直到下次...编码快乐！*