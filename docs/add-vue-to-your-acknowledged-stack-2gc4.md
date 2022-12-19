# 将 Vue 添加到您确认的堆栈中

> 原文：<https://dev.to/lexswed/add-vue-to-your-acknowledged-stack-2gc4>

### TL；速度三角形定位法(dead reckoning)

Vue.js 不能称为“和 React 一样好”(甚至更好)。React 作为一个代码库，它的技巧和架构决策(如纤程或时间片、悬念和钩子)将 JS 开发推得比我们预期的更远，它还教会我思考函数性，这对使用任何技术编写任何应用程序都有很大帮助。但是 Vue.js 的做法，对我来说，略有不同。它让你专注于你开发的产品，而不是你写的代码。同时，我相信 99%的项目可以用 Vue 开发，而不是在功能和性能上没有区别的 React。但是 Vue 让你开心。它有如此大量的小助手、技巧和窍门，以至于当你试图再次用 React 构建东西时，你会想“我为什么要一遍又一遍地写这些样板文件？”。Vuex 是一个核心库(看它是什么意思),它给了你一个简单的使用方法，减少了你的代码库，从而减少了 bug。vue-router 是另一个核心库，它以最少的设置为您提供所需的一切，但如果您需要复杂的东西，它会非常灵活。我甚至不会提到 Vue out of the box 中的`transition`和`transition-groups`所提供的强大的 UI 和 UX 改进，这使得任何应用程序都变得更好。我认为 Vue 比 React 好吗？不，React 仍然更受欢迎，一年一次震撼我的心灵(再次纤维，悬念)。但是我会在下一个项目中使用 React 吗？不不不。使用 Vue.js 的开发者体验要好得多，我宁愿选择它。

## 我们开始吧

好吧，我知道 React 开发人员非常忙，没有时间做更多的介绍。让我们创建一个新的 Vue 项目:

```
npx @vue/cli create simple-sample 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在设置中选择我们想要的功能:
[![Vue features setup](../Images/23a81c92b83f1f4ebfe69270644715d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LBGQ5wFp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ihbumegzmoofdhzeg5e.png)

我选择 TypeScript 是因为我们喜欢安全的类型，我不需要任何预处理程序，因为默认情况下会包含 PostCSS，而 [vuex](https://vuex.vuejs.org/) 和 [vue-router](https://router.vuejs.org/) 是因为它们是 vue 生态系统的重要组成部分。我们希望使用类语法(是的，这不是默认的)，因为类是熟悉的，看起来很好。所以我们有我们的设置像:
[![vue project setup](../Images/ac10a945e5f50e64f1dd2f2bb5baa26e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cf4CImxn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k7flabpu8v48bmekni37.png)

快速依赖项安装，现在我们可以看到项目结构:

[![Initial project structure](../Images/3181c9d767e3cfddb46172fd5ae2dac7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GRBsjmiT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/psv8bvjlztlbi7km32zk.png)

`shims-`只是 TS 的一个设置，在`.vue`单个文件组件中使用这个令人敬畏的类型化 JavaScript。您可能已经听说过 SFC:我们不需要这样做，但是我们可以将我们的组件编写在一个文件中，并且对它感到满意！
为什么？嗯，因为你的组件通常是骨架(模板)、行为(脚本)和外观(风格)。因此，让我们在**组件**文件夹中创建我们的`vue`文件，并编写我们的组件。我把它叫做`DevToHeader.vue`。

###### (快速提示: [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur) 是 VS 代码的 Vue 语法助手)

## 模板快速介绍

*   模板是有效的 html
*   如果你需要绑定一些数据到模板，你用`v-bind`(没人这么做**，用`:`)，比如`:prop="{ react: 'cool' }"`(和 React 一样，`:prop="true"`只等于`prop`)
*   如果你需要监听某个事件，你可以使用`v-on`或简称`@`。例如`@click="functionName"`或感受到`@customEvent="handlerOfThisEventName"`或`@click="$event => handlerFuncName($event, 'my custom data')"`或`@mousedown="mouseDownDataInYourComponent = true"`的力量
*   您只需要记住几条指令:
    *   `v-for`指令用于循环，遍历你的集合，比如:`v-for="(value, key) in youObjectOrArray"`，所以现在你可以很容易地使用你的`value`或`key`(我听到“嗯，为什么先用`value`？”，嗯，你平时做`value in yourArray`
    *   `v-if`、`v-else-if`和`v-else`进行条件渲染，你在 JSX 很好的替代了三元运算符。使用像`v-if="userLoggedIn"`(或者简单的`v-show`到`display: none;`)的(！)挂载的组件，你会很快发现这个助手有多牛逼，现在不需要 css 或者内联样式了！)
    *   `v-model` -你的英雄，让你不用为每个动态输入编写`setState`方法。你现在可以拥有与`<input :value="searchText" @input="updateSearchTextValue)" />`相同的`<input v-model="searchText" />`(你能猜出文档中的这个例子[:`<input v-model.number="age" type="number">`是什么吗？](https://vuejs.org/v2/guide/forms.html#number)
    *   你可以看到或创建一个自定义的，他们通常以`v-*`开始，并添加一些很酷的功能。
*   为了渲染一些数据，你可以使用胡子:`<h2>{{ variableName }}</h2>`，不需要那些仅仅用于文本的胡子:`<h2>search</h2>`。

基本就是这样！有了这些知识，让我们来定义我们的模板:

```
<template>
  <header class="main-header">
    <img src="../assets/logo.png" alt="logo" />
    <input placeholder="search" v-model="searchText" />
    <button @click="openModal">Write a post</button>
    <img v-if="user" :src="user.photo" alt="User avatar" />
    <button v-else>Login</button>
  </header>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

这里没有问题，对吗？可能只是这个动态数据是从哪里来的，像`user`或者像`goToNewPostPage`这样的函数？

## 我们来定义数据和逻辑

现在我们可以去脚本标签。我们选择了基于类的 sytax，以便更容易地从 React 过渡，我们还提供了 TypeScript 支持，只是为了好玩。让我们开始:

```
<script lang="ts">
</script> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们来看身体:

```
// think about this as import React from "react"
import { Component, Vue } from "vue-property-decorator";

// with this decorator we're saying to compile regular Vue component from our class
@Component
export default class DevToHeader extends Vue {
    user:User = null;
    searchText:string = ""; // two-way binding in v-model works with this guy

    openModal(event: Event) {
      this.$emit('openCreatePostModal', event);
    }
}

type User = IUser | null;

interface IUser {
  photo: string;
  name: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们在组件和方法中定义了数据，即`$emits`数据。还记得那个`@customEvent="handlerForIt"`吗？好了，现在我们的`header`的父节点可以监听事件`@openCreatePostModal="handlerForIt"`，处理程序将接收`event`作为参数。我们可以把任何我们想要的数据传给我们的父母。

###### 一些 vue 特有的方法或数据总是从`$`符号开始。

问:我们的`componentDidMount`在哪里？
嗯，就定义一个`mounted`方法:

```
 // ...
  async mounted() {
    this.user = await fetchUserData()
  }
  // ... 
```

Enter fullscreen mode Exit fullscreen mode

用户更新->组件更新->查看更新。简单。

问:`static getDerivedStateFromProps(props, state)`呢？
好的，让我们假设我们从父节点获得`username`，我们想根据`username`改变虚拟角色的路径。为此，我们来改变一下:

```
import { Component, Vue, Prop } from "vue-property-decorator";

@Component
export default class DevToHeader extends Vue {
    @Prop({
      type: String, // your `prop-types` checks out of the box
      default: null // we don't really need that
    })
    username:string | null = null; // now for TypeScript
    // our photo src path that we will use as img :src
    photoSrcPath: string | null = null;
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

所有的`props`都可以作为实例属性，就像我们的自定义数据一样。现在添加添加路径:

```
// import Watch decorator
import { Component, Vue, Prop, Watch } from "vue-property-decorator";

// ... or component class
    // watch for 'username' property
    @Watch('username', {
      immediate: true // call this function also on component mount
    })
    changePhotoPath(username:string | null) { // takes (newValue, oldValue)
      this.photoSrcPath = username ? `/user/${username}/data/avatar.png` : null;
    }
// ... 
```

Enter fullscreen mode Exit fullscreen mode

所以我们基于属性改变来改变我们的状态，这是最常见的情况吗？是的，您也可以观察您的“状态”数据属性。观察者非常强大💪。

但是我们如何以一种 Vue 的方式来处理它呢？[计算属性](https://vuejs.org/v2/guide/computed.html)！由于我们不需要更改组件中的任何其他数据，不需要复杂的逻辑，也不需要进行任何异步请求，所以拥有一个简单的属性是有意义的，它将基于`username`进行更改。计算属性是必由之路，它们是高性能的，它们有缓存，易于编写和使用:

```
 // remove photoSrcPath data property
  // define computed property:
  get photoSrcPath():string {
    return `/user/${this.username}/data/avatar.png`
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的`img`标签:

```
 <img v-if="username" :src="photoSrcPath" alt="User avatar" /> 
```

Enter fullscreen mode Exit fullscreen mode

当然，在 computed 中你可以有任何类型的东西，就像我曾经有一堆过滤器用于相同的输入集合:

```
// ...
    get filteredByA() {
      return this.collection.filter(filterByA).map(setFlags);
    }

    get filteredByB() {
      return this.collection.filter(filterByB)
    }

    get filteredByC() {
      return this.collection.filter(filterByC).map(setFlags);
    }
// ... 
```

Enter fullscreen mode Exit fullscreen mode

不需要在 state 中存储它，实现`shouldComponentUpdate`之类的东西。再说一次，他们很有表现力。

## 添加我们的组件

让我们转到`views/Home.vue`并在那里添加我们的组件:

```
import { Component, Vue } from "vue-property-decorator";
import HelloWorld from "@/components/HelloWorld.vue"; // @ is an alias to /src
import DevToHeader from "@/components/DevToHeader.vue";

@Component({
  components: {
    HelloWorld,
    DevToHeader // becomes 'DevToHeader': DevToHeader
  }
})
export default class Home extends Vue {} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们传递给 decorator 一些选项，特别是`components`。通过这种方式，我们告诉 Vue 编译器我们将在模板中使用哪些组件。Vue 自动将 PascalCase 更改为 kebab-case 以在模板中使用(或者你可以自己命名，比如`'hello-w': HelloWorld`)。所以在我们的`Home.vue`模板中，我们可以使用我们的组件:

```
 <div class="home">
    <dev-to-header
      username="Alex"
      @openCreatePostModal="$router.push('/newPost')"
    />
    <img alt="Vue logo" src="../assets/logo.png">
    <hello-w msg="Welcome to Your Vue.js + TypeScript App"/>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

我们将“Alex”作为一个`username`道具传递，并为我们的组件附加一个监听器。我们的头不知道，但没有模态，我们应该去另一个页面(是的，我们现在应该重命名这个事件)，所以我在这里写了一个内联函数。记住内联🔝？从 DX 的角度来看，它们不是很好，但是对于一些简单的东西，为了避免样板文件，为什么不呢？我们毕竟是人...

所以这个内联器实际上调用的是`this.$router.push('/newPost')`，那么什么是`$router`？

## **vista 路由器**

你有过因为 React 路由器升级而导致路由器设置被重写几次的经历吗？看看这个几乎不随时间变化的设置: