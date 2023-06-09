# 在 Vue.js 中使用动态组件

> 原文：<https://dev.to/raymondcamden/working-with-dynamic-components-in-vuejs-3bmh>

我目前正在深入一个使用 [NativeScript Vue](https://nativescript-vue.org/) 的项目，遇到了一个有趣的问题——谢天谢地——在 Vue 中有一个非常简单的解决方案。虽然我肯定不相信 Vue 是完美的，但我越深入，我越欣赏它的强大。我遇到的问题是处理动态表单。

应用程序加载一组表示字段数组的数据。每个字段都有特定的类型、可能的选项、可能的默认值等等。我知道我可以构建 Vue 组件来表示这些字段，但是我不确定如何在我的布局中实际使用它们。事实证明，有一种非常简单的方法可以做到这一点- [动态组件](https://vuejs.org/v2/guide/components.html#Dynamic-Components)。

基本上，不是将`<my-component>`添加到布局中，而是添加`<component :is="my-component">`。是的，差不多就是这样。不过还有一个重要的方面。既然您已经知道如何创建动态组件，那么您将如何动态地向它传递数据呢？

原来，[绑定](https://vuejs.org/v2/api/#v-bind)特性支持传递一个对象，并将键/值对扩展为属性和值。简单到:`v-bind="object"`。

所以用一个例子可能更有意义。让我们从一个 Vue 应用程序开始，它有一些表示字段数据的硬编码数据。

```
const app = new Vue({
  el:'#app',
  data() {
    return {
      fields:[
        {
          'id':1,
          'label':'Name',
          'type':'textField'
        },
        {
          'id':2,
          'label':'Email',
          'type':'textField'
        },
        {
          'id':3,
          'label':'Movies',
          'type':'selectField',
          'answers':[
            { value:1,label:"Aa" },
            { value:2,label:"Bb" },
            { value:3,label:"Cc" }
          ]
        },
        {
          'id':4,
          'label':'Food',
          'type':'radioField',
          'answers':[
            { value:1,label:"Aa" },
            { value:2,label:"Bb" },
            { value:3,label:"Cc" }
          ]
        },
      ]
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

在`data`块中，我定义了 4 个字段。每个都有一个标签，一个类型，有些有代表选项的答案。为了简单起见，我排除了违约之类的东西。在真正的应用程序中，我不仅有默认值，还有类似“默认为当前时间”的概念现在让我们看看布局。

```
<div id="app" v-cloak>
  <component 
             v-for="field in fields" 
             :key="field.id"
             :is="field.type"
             v-bind="field">
  </component>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

简单明了。我遍历每个字段，根据`field.type`值定义它的组件。然后，我使用上述的`v-bind`技巧传入该字段的所有数据。至于我是如何实现组件的，我只是做了一些基本的 HTML。这里有三个:

```
Vue.component('textField', {
  template:'<div><input type="text" :id="id"></div>',
  data() {
    return {
    }
  },
  computed:{
  },
  props:["label","id"]
});

Vue.component('selectField', {
  template:
`<div>

<select :id="id">
  <option v-for="answer in answers" :key="answer.value" :value="answer.value">

  </option>
</select>
</div>`,
  data() {
    return {
    }
  },
  computed:{
  },
  props:["label","id","answers"]
});

Vue.component('radioField', {
  template:
`<div>
  <br/>
  <div v-for="answer in answers" :key="answer.value">
   <input type="radio" :name="id" :value="answer.value" />
  </div>
</div>`,
  data() {
    return {
    }
  },
  computed:{
  },
  props:["label","id","answers"]
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个相当难看的字段实现，但它完成了工作。当然，在我的“真正的”应用程序中，我将同时使用单个文件组件和 NativeScript 组件，但你会明白的。

如果你想看一个这样的例子，看看下面的代码笔。请记住，这是一个超级基本的实现，对我来说是为了测试这个想法，仅此而已。

[https://codepen.io/cfjedimaster/embed/pxXowY?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/pxXowY?height=600&default-tab=result&embed-version=2)