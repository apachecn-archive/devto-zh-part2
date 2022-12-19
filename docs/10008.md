# vue . js 插槽

> 原文:# t0]https://dev . to/flaviocopes/vuej 插槽-2h4d

> 我正在学习 Vue.js 课程。如果你对学习 Vue 感兴趣，[上列表](https://vuecourse.com/)明天就可以获得一本 120 多页的关于 Vue 基础知识的免费电子书！

一个组件可以选择完全定义它的内容，就像这样:

```
Vue.component('user-name', {
  props: ['name'],
  template: '<p>Hi {{ name }}</p>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

或者它也可以让父组件通过使用**槽**将任何类型的内容注入其中。

什么是插槽？

通过将`<slot></slot>`放入组件模板
中来定义它

```
Vue.component('user-information', {
  template: '<div class="user-information"><slot></slot></div>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

使用该组件时，在开始和结束标记之间添加的任何内容都将被添加到槽占位符中:

```
<user-information>
  <h2>Hi!</h2>
  <user-name name="Flavio">
</user-information> 
```

Enter fullscreen mode Exit fullscreen mode

如果您将任何内容放在`<slot></slot>`标签旁边，这将作为默认内容，以防没有内容传入。

复杂的组件布局可能需要更好的方式来组织内容。

输入**命名插槽**。

使用命名槽，您可以将槽的一部分分配到组件模板布局中的特定位置，并且您可以对任何标签使用一个`slot`属性，以将内容分配到该槽。

任何模板标签之外的任何东西都被添加到主`slot`中。

为了方便起见，我在这个例子中使用了一个`page`单个文件组件:

```
<template>
  <div>
    <main>
      <slot></slot>
    </main>
    <sidebar>
      <slot name="sidebar"></slot>
    </sidebar>
  </div>
</template>

<page>
  <ul slot="sidebar">
    <li>Home</li>
    <li>Contact</li>
  </ul>

  <h2>Page title</h2>
  <p>Page content</p>
</page> 
```

Enter fullscreen mode Exit fullscreen mode

> 我正在学习 Vue.js 课程。如果你对学习 Vue 感兴趣，[上列表](https://vuecourse.com/)明天就可以获得一本 120 多页的关于 Vue 基础知识的免费电子书！