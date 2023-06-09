# view . js events-检视. js 事件

> 原文：<https://dev.to/flaviocopes/vuejs-events-4hch>

## 什么是 Vue.js 事件

Vue.js 允许我们通过在元素上使用`v-on`指令来拦截任何 DOM 事件。

如果我们想在这个元素上发生点击事件时做些什么:

```
<template>
  <a>Click me!</a>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我们添加一个`v-on`指令:

```
<template>
  <a v-on:click="handleClick">Click me!</a>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

Vue 还为此提供了一个非常方便的替代语法:

```
<template>
  <a @click="handleClick">Click me!</a>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

你可以选择使用或不使用括号。`@click="handleClick"`相当于`@click="handleClick()"`。

`handleClick`是附加到组件的方法:

```
<script>
export default {
  methods: {
    handleClick: function(event) {
      console.log(event)
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

方法在我的 [Vue 方法教程](https://flaviocopes.com/vue-methods/)中有更详细的解释。

这里你需要知道的是，你可以从 events: `@click="handleClick(param)"`传递参数，它们会在方法内部被接收。

## 访问原始事件对象

在许多情况下，您会想要对事件对象执行操作，或者在其中查找一些属性。你如何访问它？

使用特殊的`$event`指令:

```
<template>
  <a @click="handleClick($event)">Click me!</a>
</template>

<script>
export default {
  methods: {
    handleClick: function(event) {
      console.log(event)
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经传递了一个变量:

```
<template>
  <a @click="handleClick('something', $event)">Click me!</a>
</template>

<script>
export default {
  methods: {
    handleClick: function(text, event) {
      console.log(text)
      console.log(event)
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

从那里你可以调用`event.preventDefault()`，但是有一个更好的方法:事件修饰符

## 事件修饰符

不要在你的方法中弄乱 DOM“东西”,告诉 Vue 为你处理事情:

*   `@click.prevent`呼叫`event.preventDefault()`
*   `@click.stop`呼叫`event.stopPropagation()`
*   `@click.passive`使用 addEventListener 的[被动选项](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters)
*   `@click.capture`使用事件捕获而不是事件冒泡
*   `@click.self`确保点击事件不是从子事件冒泡而来，而是直接发生在该元素上
*   事件只会被触发一次

所有这些选项都可以通过在另一个选项后附加 on 修饰符来组合。

有关传播、冒泡/捕获的更多信息，请参见我的 [JavaScript 事件指南](https://flaviocopes.com/javascript-events/)。

> 我正在学习 Vue.js 课程。如果你对学习 Vue 感兴趣，[上名单](https://flaviocopes.com/course/vue/)下周就能获得一本 100 多页的关于 Vue 基础知识的免费电子书！