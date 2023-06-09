# 为什么 Vue 组件上的数据属性必须是函数？

> 原文：<https://dev.to/winnercrespo/why-does-the-data-property-on-a-vue-component-must-be-a-function-566c>

如果你在开始使用一个框架时不熟悉它的基本规则(编程语言、工具等)，事情就不会像预期的那样工作，因为它不是以那种方式设想的。

第一次使用 Vue 时，我犯了一个错误:

```
data: {
  message: 'Some Message'
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我得到了下面的警告信息:

[Vue warn]:“数据”选项应该是返回组件定义中每个实例值的函数。

你应该做的是:

```
data: function() {
  return {
    message: 'Some Message'
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，Vue 强制数据属性为函数的原因是组件的每个实例都应该有自己的数据对象。如果我们不这样做，所有的实例将共享同一个对象，每次我们改变一些东西，它将反映在所有的实例中。

看看 [Vue 的文档](https://vuejs.org/v2/guide/components.html#data-Must-Be-a-Function)是怎么说的，以及一个快速的实例。

我希望这有所帮助。