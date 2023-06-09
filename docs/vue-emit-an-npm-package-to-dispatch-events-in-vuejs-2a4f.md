# Vue-emit:在 Vue.js 中调度事件的 npm 包

> 原文：<https://dev.to/denisinvader/vue-emit-an-npm-package-to-dispatch-events-in-vuejs-2a4f>

[源代码](https://github.com/denisinvader/vue-emit) | [NPM](https://www.npmjs.com/package/vue-emit)

Vue.js 提供了一个由`this.$emit`在组件中调度事件的 API。但是在功能组件中，你根本没有`this`实例，许多以前没有使用过功能组件的人会问“如何在功能组件中发出事件？”

第一个解决方案是为事件侦听器使用 props。它这么一个反应方式。问题是，在这种情况下，你有严格定义的事件名称，不能使用`@` / `v-on` / `v-model`语法，并且在实现[透明包装器](https://www.vuemastery.com/conferences/vueconf-2018/7-secret-patterns-vue-consultants-dont-want-you-to-know-chris-fritz/)时有一些额外的困难。示例:

```
// Child component
export default {
  name: 'base-input',
  functional: true,
  props: {
    onInput: {
      type: Function,
      default: () => (),
    },
  },
  render (h, context) {
    return (
      <input
        onInput={context.props.onInput}
        {...{
          attrs: context.data.attrs,
          on: context.listeners,
        }}
      />
    );
  },
}; 
```

```
<!-- Parent component template -->
<template>
  <base-input onInput="doSomething" />
</template> 
```

第二种解决方案是使用`context.listeners`。这是在 Vue.js 中调度事件的好方法。例如:

```
// Child component
export default {
  name: 'base-input',
  functional: true,
  render (h, context) {
    return (
      <input
        onInput={e => context.listeners.input(e.target.value)}
        {...{
          attrs: context.data.attrs,
          on: context.listeners,
        }}
      />
    );
  },
}; 
```

```
<!-- Parent component template -->
<template>
  <base-input @input="doSomething" />
</template> 
```

但是使用`context.listeners`的时候也有一些陷阱。我发现了两种你会得到`TypeError: context.listeners.yourEvent is not a function`的情况

### 1。监听器可以是`undefined`(不需要证明)

如果不从父组件传递监听器，那么在`context.listeners`对象中就没有事件(在我们的例子中是输入事件)的处理程序。

### 2。监听器值可以是一个函数

[![Components structure example](img/8f837161fc9f268f838f1d123d3e446a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rz7_NWlS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0mimq45c5trvz1ik4byt.jpg)

上图显示了组件和侦听器的示例结构。如果 C 组件中的这个结构`context.listeners.input`是两个函数的数组:`[doSomethingA, doSomethingB]`。对于`this.$listeners`来说也是如此。

所以当使用`context.listeners`(从`this.$listeners`直接调用处理程序)时，你必须总是检查监听器是否存在，以及它是什么类型:函数还是数组。

### 使用 vue-emit

为了解决上面的问题，我写了一个助手函数来做所有的检查，并提供了类似于`$emit`的 API。这就是使用方法:

```
yarn add vue-emit 
```

```
import emit from 'vue-emit';

export default {
  name: 'base-input',
  functional: true,
  render (h, context) {
    return (
      <input
        onInput={e => emit(context.listeners, 'input', e.target.value)}
      />
    );
  },
}; 
```

第一个参数是一个具有`eventName => handler`结构的 listeners 对象。它是一个标准的 Vue `this.$listeners`和`context.listeners`对象。

第二个参数是事件名称。Vue-emit 将检查 listeners 对象，如果未定义处理程序，则什么也不做；如果有多个侦听器，则调用每个处理程序。

您还可以传递任意数量的附加参数，它们都将被传递给 handler。顺便说一下，这是有状态组件的用例——由于某种原因，您希望将多个参数作为有效载荷传递给处理函数。

## 结论

我希望你会发现这个包是有用的，并喜欢阅读这篇文章。请随意提出您的问题、未决问题、拉动请求等。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [丹尼斯维德](https://github.com/denisinvader) / [ vue-emit](https://github.com/denisinvader/vue-emit)

### 用于在 Vue.js(功能性)组件中发出事件的辅助函数

<article class="markdown-body entry-content container-lg" itemprop="text">

# 已发布视图

从 Vue.js 功能组件发出事件的帮助器函数。也可以在常规组件中使用 [CodeSandbox 演示](https://codesandbox.io/s/71pnzj8qrq)

## 装置

```
yarn add vue-emit
# or
npm install vue-emit
```

## 使用

```
import emit from 'vue-emit';
export default {
  functional: true,
  render (h, context) {
    return (
      <button
        onClick={e => emit(context.listeners, 'someEvent', e, 'additional param', 'etc')}
      >
        {context.children}
      </button>
    );
  },
};
```

## 参数

```
emit(handlers_list, event_name, [optional_payload]);
```

*   `handlers_list` -功能对象。在 Vue 中，这是`this.$listeners`和`context.listeners`(用于功能组件)。每个值可以是一个函数或函数数组。如果是数组`emit`将调用所有提供的回调。

*   `event_name` -要触发的事件的名称。该事件可能不会…

</article>

[View on GitHub](https://github.com/denisinvader/vue-emit)

丹尼尔·里吉在 Unsplash 上的照片