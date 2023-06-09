# Vue.js 组件

> 原文：<https://dev.to/flaviocopes/vuejs-components-2l24>

组件是接口的单个独立单元。它们可以有自己的状态、标记和样式。

## 如何使用组件

Vue 组件可以用 4 种主要方式定义。

我们用暗语说吧。

第一个是:

```
new Vue({ /* options */ }) 
```

Enter fullscreen mode Exit fullscreen mode

第二个是:

```
Vue.component('component-name', { /* options */ }) 
```

Enter fullscreen mode Exit fullscreen mode

第三种方法是使用本地组件:只能由特定组件访问的组件，在其他地方不可用(非常适合封装)。

第四个是在`.vue`文件中，也叫[单文件组件](https://dev.to/flaviocopes/vuejs-single-file-components-33jp-temp-slug-8476893)。

让我们深入了解前三种方式的细节。

使用`new Vue()`或`Vue.component()`是构建非单页面应用程序(SPA)的应用程序时使用 Vue 的标准方式，而是仅在某些页面中使用 Vue.js，比如在联系人表单或购物车中。或者，所有页面都使用 Vue，但是服务器呈现布局，您将 HTML 提供给客户端，然后客户端加载您构建的 Vue 应用程序。

在 SPA 中，Vue 构建 HTML，使用单个文件组件更常见，因为它们更方便。

通过在 DOM 元素上挂载 Vue 来实例化它。如果你有一个`<div id="app"></div>`标签，你将使用:

```
new Vue({ el: '#app' }) 
```

Enter fullscreen mode Exit fullscreen mode

用`new Vue`初始化的组件没有对应的标签名，所以它通常是主容器组件。

应用程序中使用的其他组件使用`Vue.component()`进行初始化。这样的组件允许你定义一个标签，用它你可以在应用程序中多次嵌入组件，并在`template`属性:
中指定组件的输出

```
<div id="app">
  <user-name name="Flavio"></user-name>
</div>

Vue.component('user-name', {
  props: ['name'],
  template: '<p>Hi {{ name }}</p>'
})

new Vue({
  el: "#app"
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

* * *

我们在做什么？我们在`#app`上初始化一个 Vue 根组件，在里面我们使用 Vue 组件`user-name`，它抽象出我们对用户的问候。

该组件接受一个属性，它是我们用来将数据向下传递给子组件的属性。

在`Vue.component()`调用中，我们将`user-name`作为第一个参数传递。这为组件提供了一个名称。你可以用两种方式写这个名字。第一个是我们用过的，叫做**烤肉串**。第二个名为 **PascalCase** ，类似于 camelCase，但是第一个字母大写:

```
Vue.component('UserName', { /* ... */ }) 
```

Enter fullscreen mode Exit fullscreen mode

Vue 内部会自动创建一个从`user-name`到`UserName`的别名，反之亦然，你可以随便用。通常最好在 JavaScript 中使用`UserName`，在模板中使用`user-name`。

## 本地组件

任何使用`Vue.component()`创建的组件都是**全球注册的**。您不需要将它赋给一个变量，或者传递它以便在模板中重用它。

您可以通过将一个定义组件对象的对象赋给一个变量来本地封装组件:

```
const Sidebar = {
  template: '<aside>Sidebar</aside>'
} 
```

Enter fullscreen mode Exit fullscreen mode

然后通过使用`components`属性:
使其在另一个组件中可用

```
new Vue({
  el: '#app',
  components: {
    Sidebar
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

您可以在同一个文件中编写组件，但是最好的方法是使用 JavaScript 模块:

```
import Sidebar from './Sidebar'

export default {
  el: '#app',
  components: {
    Sidebar
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重用一个组件

子组件可以多次添加。每个单独的实例都独立于其他实例:

```
<div id="app">
  <user-name name="Flavio"></user-name>
  <user-name name="Roger"></user-name>
  <user-name name="Syd"></user-name>
</div>

Vue.component('user-name', {
  props: ['name'],
  template: '<p>Hi {{ name }}</p>'
})

new Vue({
  el: "#app"
}) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

* * *

## 构件的积木

到目前为止，我们已经看到了组件如何接受`el`、`props`和`template`属性。

*   `el`仅在使用`new Vue({})`初始化的根组件中使用，并标识组件将挂载的 DOM 元素。
*   列出了我们可以传递给子组件的所有属性
*   `template`是我们可以设置组件模板的地方，它将负责定义组件生成的输出。

组件接受其他属性:

*   `data`组件本地状态
*   `methods`:组件[方法](https://dev.to/flaviocopes/vuejs-methods-4kn1-temp-slug-989840)
*   `computed`:与组件相关的[计算属性](https://dev.to/flaviocopes/vuejs-computed-properties-384m-temp-slug-5709303)
*   `watch`:组件[观察者](https://dev.to/flaviocopes/vuejs-watchers-3kd8-temp-slug-5427953)

> 我正在学习 Vue.js 课程。如果你对学习 Vue 感兴趣，[上名单](https://flaviocopes.com/course/vue/)下周就能获得一本 100 多页的关于 Vue 基础知识的免费电子书！