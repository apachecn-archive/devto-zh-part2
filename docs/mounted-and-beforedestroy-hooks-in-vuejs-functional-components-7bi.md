# Vue.js 功能组件中的已安装和销毁前挂钩。

> 原文：<https://dev.to/denisinvader/mounted-and-beforedestroy-hooks-in-vuejs-functional-components-7bi>

当你使用 Vue.js [功能组件](https://vuejs.org/v2/guide/render-function.html#Functional-Components)时，你除了渲染函数和它的带有一些参数的上下文之外什么都没有。

在工作中，我总是喜欢在我的共享组件库中使用功能组件而不是有状态组件，当我不需要状态时，它工作得很好。但是有时我需要无状态组件中的`mounted`和`beforeDestroy`钩子。

## 问题

让我们看看例子。我们需要一个简单的模态组件来呈现一些覆盖和传递子块。大概是这样:

```
export default {
  functional: true,
  render (h, context) {
    return (
      <div class="modal">
        <div class="modal__overlay" />
        <div class="modal__content">{context.children}</div>
      </div>
    );
  },
}; 
```

我没有提供任何样式，但它应该看起来像[引导模态](https://getbootstrap.com/docs/4.0/components/modal/#live-demo)。现在，如果窗口有 y 轴滚动打开，模态将随着页面滚动而移动。为了创建更好的 UX，我们应该在模态打开时禁用滚动，在模态关闭时再次启用它。当使用通常的组件时，你可以在`mounted`和`befoDestroy`钩子:
中完成

```
export default {
  // ...
  mounted () {
    document.body.style.overflow = 'hidden';
  },
  beforeDestroy () {
    document.body.style.overflow = null;
  },
  // ...
}; 
```

但是如何实现这个没有钩子的逻辑呢？答案是:用`<transition>`组件搭配`appear`道具！

## 解

组件有自己的[钩子](https://vuejs.org/v2/guide/transitions.html#JavaScript-Hooks)用于进入和离开，所以我们可以把所有的组件包装在里面并定义钩子。`appear`道具保证当组件挂载时，我们的“挂载”钩子将被触发。

```
const mounted = () => {
  document.body.style.overflow = 'hidden';
};
const beforeDestroy = () => {
  document.body.style.overflow = null;
};

export default {
  functional: true,
  render (h, context) {
    return (
      <transition
        appear
        name="fade"
        onAfterEnter={mounted}
        onBeforeLeave={beforeDestroy}
      >
        <div class="modal">
          <div class="modal__overlay" />
          <div class="modal__content">{context.children}</div>
        </div>
      </transition>
    );
  },
}; 
```

就是这样！现在我们在一个功能组件中有了一些钩子。

您还可以通过实现过渡动画来改进您的 UI。

> Suad Kamardeen 在 Unsplash 上拍摄的照片