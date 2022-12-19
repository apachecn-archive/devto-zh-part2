# Vue CLI 3.0 插件，用于使用原子设计和故事书创建应用程序

> 原文：<https://dev.to/miladalizadeh/vue-cli-30-plugin-for-creating-apps-using-atomic-design--storybook-42dk>

Vue 原子设计插件是一个遵循原子设计方法的自以为是的 Vue CLI 3 插件。请参考这篇由 Brad Frost 写的的[精彩文章了解更多信息。](http://bradfrost.com/blog/post/atomic-web-design/)

相关项目:

*   vue-CLI-plugin-Atomic-Design-component-基于原子设计的 Vue 组件库
*   Vue-CLI-plugin-scss-base-Vue 项目的入门 scss 基础

## 如何安装

您首先需要安装 Vue Cli 3

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli 
```

然后，您可以通过键入以下命令来添加插件

```
vue add atomic-design 
```

## 故事书

Vue 原子设计使用[故事书](https://storybook.js.org/)作为其设计系统工具。Storybook 最初是为 React 创建的，它是一个独立创建 UI 组件的工具。使用 Storybook 的好处是我们可以同时创建我们的风格指南和我们的项目，而不需要维护两者，这对小型和大型应用程序都很好。
一旦你安装了插件，故事书就会被配置好，你可以通过运行以下命令来使用它:

T2`yarn run serve:storybook`

或者生成静态样式指南:

T2`yarn run build:storybook`

## 结构

原子设计在通过组合对组件进行分组方面有一个非常清晰的方法，这是与 Vue.js 的一个很好的组合

原子设计结构总结如下。

### 原子

原子是一种原生的 html 标签。一个只呈现一个标签的 Vue 组件，如`div`、`p`或任何其他标签。

```
// atoms/Button.vue

<template>
  <button class="a-button" @click="$emit('click')">
    <slot></slot>
  </button>
</template> 
```

```
// atoms/Input.vue

<template>
  <input class="a-input" type="text" v-model="value" @input="$emit('input') />
</template> 
```

### 分子

一个分子是两个或几个原子的组合。

```
// molecules/SearchForm.vue

<template>
  <form class="m-search-form">
    <Input @input="handleInput"/>
    <Button @click="handleSearch">Search</Button>
  </form>
</template> 
```

### 生物体

有机体是原子、分子和其他有机体的组合

```
// organsims/Header.vue

<template>
  <header class="o-header">
    <Logo />
    <Navigation />
    <SearchForm />
  </header>
</template> 
```

### 模板

有机体、分子和原子的组合形成一个完整的页面。模板只有虚拟占位符内容。

```
// templates/PageTemplate.vue

<template>
  <div class="o-page-template">
    <Hero />
    <Posts />
    <Comments />
  </div>
</template> 
```

### 页面

页面本质上是具有真实代表性内容的模板实例。这通常是 Vuex 连接到我们模板的地方。这种方法的好处是数据和 UI 的分离，它使您能够创建您的 UI，而不管您的数据实际上来自哪里。这也使得测试更加容易。

```
// pages/PostPage.vue

<template>
  <PageTemplate
    :posts="posts"
    :hero="hero"
    :comments="comments"
  />
</template>

<script>
import { mapState } from 'vuex'

export default {
  computed: {
    mapState({
      posts: state => state.posts.postList,
      hero: state => state.home.hero,
      comments: state => state.comments.commentList,
    })
  }
}
</script> 
```

### 文件夹结构

为了使组织更简单，每个组件都有一个以其名字命名的文件夹，其中有 3 个文件。`index.vue`、`index.stories.js`和`index.test.js`。有了这个结构，所有的测试、故事和组件都将在同一个地方，而不会杂乱无章。例如:

```
-- components
   -- atoms
      -- Button
         -- index.vue
         -- index.stories.js
         -- index.test.js
      -- Input
         -- index.vue
         -- index.stories.js
         -- index.test.js
   -- molecules
      -- SearchInput
         -- index.vue
         -- index.stories.js
         -- index.test.js 
```

按照这种结构，所有的故事都将在运行时创建。

#### 故事书

您可以通过将故事模块命名为“{Category} - {Component Name}”来对故事书故事进行分类。例如:

```
storiesOf('Atom - Button', module)
  .add('default', () => ({
    components: { Button },
    template: `
      <Button />
    `
  })); 
```

#### Vuex

这个插件采用[模块化](https://vuex.vuejs.org/guide/modules.html)的方法来组织 Vuex 商店。你的应用程序的每个特性都被分离到一个模块中，这个模块包括它自己的状态、突变、动作和 getters。例如:

```
-- storeModules
   -- Posts
     -- index.js
   -- Users
     -- index.js
   -- Users
     -- index.js 
```

例如，storeModules/users/index.js 将包含以下内容:

```
const state = {
  userList: [],
  currentUser: null
}

const mutations = {
  setUsers (state, payload) {
    state.userList = payload
  },

  setCurrentUsers (state, payload) {
    state.currentUser = payload
  }
}

const actions = {
  async getUsers ({ commit }, username) {
    let response = await fetch(`//api.github.com/users`)
    let users = await response.json()
    commit('setUsers', users)
  }
}

export default {
  state,
  mutations,
  actions
} 
```

然后，您可以在应用程序中引用该模块，如下所示:

```
<template>
  <div>
    {{ $store.state.users.userList }}
  </div>
</template>

<script>
  created() {
     $store.dispatch('users/getUsers');
  }
<script> 
```

您只需要在`storeModule`文件夹下创建文件夹，文件夹名称将被用作命名空间。您不需要手动将这些模块导入您的商店，因为 Webpack 会自动完成。