# Vue.js 中的功能组件

> 原文：<https://dev.to/oaltena_48/functional-components-in-vuejs-4j7b>

虽然 Vue.js 基本上性能很好，但是每个额外的组件都使用内存资源。不是每个组件都需要自己的 Vue 实例和变更检测。使用功能组件可以节省资源。

功能组件在运行时不接收 Vue 实例。相反，这些组件的行为就好像它们是更高级组件的一部分。因此，功能组件特别适合简单和通用的 UI 组件，如按钮、列表元素或下拉控件。功能组件的一个实际例子是路由视图*的占位符。*

 *您可以通过在带有模板的模板标签中添加属性 *functional* 来创建功能组件。然而，开发人员通常使用他们自己的渲染函数来实现功能组件。

功能组件没有自己的实例，因此没有上下文。为了访问属性、事件处理程序和其他定义，功能组件被赋予一个对象上下文。

示例性渲染功能被构造为如下功能组件:

```
export default {
  name: 'SimpleList',
  functional: true,
  props: {
    items: Array
  },
  render(h, context) {
    return h("ul", context.props.items.map(item => h("li", item)))
  }
} 
```

render 函数获取对象上下文作为第二个属性，现在对数组的访问不是通过 this.items 完成的，而是通过 context.props.items 完成的

导入组件后，可以如下使用:

```
<template>
  <div>
    <SimpleList :items="languages"/>
  </div>
</template>

<script>
import SimpleList from './SimpleList'

export default {
  data() {
    return {
      languages: ['German', 'French', 'Italian']
    }
  },
  components: {
    SimpleList
  }
}
</script> 
```

输出:

*   德国人
*   法语
*   意大利的*