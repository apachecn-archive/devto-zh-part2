# Vue.js 组件道具

> 原文：<https://dev.to/flaviocopes/vuejs-component-props-1612>

当一个组件需要一个或多个属性时，它必须在它的`props`属性中定义它们:

```
Vue.component('user-name', {
  props: ['name'],
  template: '<p>Hi {{ name }}</p>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

或者，在 Vue 单个文件组件中:

```
<template>
  <p>{{ name }}</p>
</template>

<script>
export default {
  props: ['name']
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

你可以有多个道具，只要把它们附加到数组:

```
Vue.component('user-name', {
  props: ['firstName', 'lastName'],
  template: '<p>Hi {{ firstName }} {{ lastName }}</p>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

通过使用对象而不是数组，使用属性的名称作为每个属性的键，使用类型作为值，可以非常简单地指定属性的类型:

```
Vue.component('user-name', {
  props: {
    firstName: String,
    lastName: String
  },
  template: '<p>Hi {{ firstName }} {{ lastName }}</p>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用的有效类型有:

*   线
*   数字
*   布尔代数学体系的
*   排列
*   目标
*   日期
*   功能
*   标志

当类型不匹配时，Vue 会在控制台中发出警告(在开发模式下)。

道具类型可以更清晰。

您可以允许多个不同的值类型:

```
props: {
  firstName: [String, Number]
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以要求道具是强制性的:

```
props: {
  firstName: {
    type: String,
    required: true
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以指定默认值:

```
props: {
  firstName: {
    type: String,
    default: 'Unknown person'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于对象:

```
props: {
  name: {
    type: Object,
    default: {
      firstName: 'Unknown',
      lastName: ''
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`default`也可以是返回适当值的函数，而不是实际值。

您甚至可以构建一个定制的验证器，这对于复杂的数据来说很酷:

```
props: {
  name: {
    validator: (name) => {
      return name === 'Flavio' //only allow "Flavios"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 我正在学习 Vue.js 课程。如果你对学习 Vue 感兴趣，[上名单](https://flaviocopes.com/course/vue/)下周就能获得一本 100 多页的关于 Vue 基础知识的免费电子书！