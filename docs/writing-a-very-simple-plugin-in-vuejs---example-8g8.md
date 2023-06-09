# 在 Vue.js 中编写一个非常简单的插件-(示例)

> 原文：<https://dev.to/nkoik/writing-a-very-simple-plugin-in-vuejs---example-8g8>

*   什么是 Vue 插件？
*   编写和使用插件

在这篇文章中，我将向你展示如何在 Vue 中编写一个基本插件。你应该有使用 Vue 和 JavaScript 的经验。

## 什么是 Vue 插件？

使您能够向 Vue 添加全局级别的功能。
这是保持你的 Vue 组件清晰小巧的好方法。你也可以打包并与其他开发者分享你的代码。
确切地说，插件只不过是一个公开了`install`方法的对象。

## 编写和使用插件

首先，您应该导出插件对象，这样它就可以在项目中的任何地方使用。然后公开带有两个参数的`install`方法:`Vue`构造函数和`options`对象。第二个参数——`options`——是可选的，用于定制你的插件。
**`plugin.js`**T8】

```
// plugin.js

// This exports the plugin object.
export default {
  // The install method will be called with the Vue constructor as
  // the first argument, along with possible options
  install (Vue, options) {
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是出现的一个问题是我在哪里提供这些`options`？

好的。您可以调用`use()`方法并传递`yourPlugin`和您的`options`作为参数，因为您已经将您的`plugin.js`导入到了`main.js`文件中。

**`main.js`**T3】

```
// main.js

import Vue from 'vue'
import yourPlugin from './plugin.js'

// Without options
Vue.use(yourPlugin)
// With options
Vue.use(yourPlugin, {
    someOption: true
})

new Vue({
  el: '#app',
  render: h => h(App)
}) 
```

Enter fullscreen mode Exit fullscreen mode

[安装方法-插件](https://vuejs.org/v2/guide/plugins.html)T3`install`方法包含以下一个或多个:

*   添加一些全局方法或属性。
*   添加一个或多个全局资产:指令/过滤器/转换等。
*   通过全局 [mixin](https://vuejs.org/v2/guide/mixins.html) 增加一些组件选项。

> Mixins 是为 Vue 组件分发可重用功能的一种灵活方式。mixin 对象可以包含任何组件选项。当一个组件使用 mixin 时，mixin 中的所有选项都将“混合”到组件自己的选项中。

*   通过将它们附加到 Vue.prototype 来添加一些 Vue 实例方法。

```
// plugin.js
import Component from '/src/components/Component.vue'

// This exports the plugin object.
export default {
  // The install method will be called with the Vue constructor as 
  // the first argument, along with possible options
  install (Vue, options) {
     // Add or modify global methods or properties.
     Vue.yourMethod = (value) => value
     // Add a component or directive to your plugin, so it will be installed globally to your project.
     Vue.component('component', Component)
     // Add `Vue.mixin()` to inject options to all components.
     Vue.mixin({
         // Add component lifecycle hooks or properties.
         created() {
            console.log('Hello from created hook!')
         }
     })
     // Add Vue instance methods by attaching them to Vue.prototype.
     Vue.prototype.$myProperty = 'This is a Vue instance property.'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 现在，让我们深入研究一下我们例子的代码

-示例-