# 一个简单、真实的 Vue.js 指令

> 原文：<https://dev.to/hugo__df/a-simple-real-world-vuejs-directive-1p7m>

[VueJS](https://vuejs.org/) 是“渐进式 JavaScript 框架”。它从视图库和前端框架世界的所有现有技术中获得灵感，包括 AngularJS、React、Angular、Ember、Knockout 和 Polymer。
在 Vue(和 Angular/AngularJS)中，指令是一种包装通常应用于 DOM 元素的功能的方式。Vue 文档中的例子是一个`focus`指令。

在 AngularJS 内部运行 VueJS 时，AngularJS 路由器会在点击时尝试解析
普通锚‘`href`。
的`href`不是 AngularJS 网址，所以它会回落到默认页面。
一个解决方案可以利用组件直接更新`window.location`，但是这里有一个漂亮的指令来做同样的事情:

```
<a v-href="'/my-url'">Go</a> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常酷的 API，可能比:
更地道

```
<MyAnchor href="/my-url">
  Go
</MyAnchor> 
```

Enter fullscreen mode Exit fullscreen mode

有几个问题:

*   [本地与全球指令注册🌐](https://codewithhugo.com/a-simple-real-world-vuejs-directive#local-global)
*   一个最小指令 API👌
*   [Vue 指令挂钩和`removeEventListener`🆓](https://codewithhugo.com/a-simple-real-world-vuejs-directive#hooks-and-remove)
*   [正确处理点击🖱](https://codewithhugo.com/a-simple-real-world-vuejs-directive#handling-clicks)
*   [离别的思念📚](https://codewithhugo.com/a-simple-real-world-vuejs-directive#parting-thoughts)

[订阅](https://buttondown.email/hugo)在你的收件箱里获得最新的帖子(比任何人都要早)。

# 本地与全球指令注册🌐

全局 Vue 指令可以这样定义:

```
Vue.directive("non-angular-link", {
  // directive definition
}); 
```

Enter fullscreen mode Exit fullscreen mode

也可以局部定义如下:

```
Vue.component("my-component", {
  directives: {
    "non-angular-link": nonAngularLinkDirective
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

其中`nonAngularLinkDirective`是定义指令的 JavaScript 对象，例如

```
const nonAngularLinkDirective = {
  bind(el, binding) {},
  unbind(el) {}
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果使用像 webpack 这样的捆绑器和单个文件组件，这就提供了灵活性:

```
// non-angular-link-directive.js
export const nonAngularLinkDirective = {
  // directive definition
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
// MyComponent.vue
<template>
  <a
    href="/my-url"
    v-non-angular-link
  >
    Go
  </a>
</template>

<script>
import { nonAngularDirective } from './non-angular-link.directive';
export default {
  directives: {
    'non-angular-link': nonAngularLinkDirective
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

# 一个最小指令 API👌

一个完整的`MyAnchor`单个文件组件将如下所示:

```
// MyAnchor.vue
<template>
  <a
    @click="goToUrl($event)"
    :href="href"
  >
    <slot />
  </a>
</template>
<script>
export default {
  props: {
    href: {
      type: String,
      required: true
    }
  },
  methods: {
    goToUrl(e) {
      e.preventDefault();
      window.location.assign(this.href);
    }
  }
});
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这相当冗长，并且利用了一个全局 DOM 对象...不理想。
这里有一些类似的指令:

```
// non-angular-link-directive.js
export const nonAngularLinkDirective = {
  bind(el) {
    el.addEventListener("click", event => {
      event.preventDefault();
      window.location.assign(event.target.href);
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个指令必须像 so `<a href="/my-url" v-non-angular-link>Go</a>`一样使用，这不是最好的 API。
通过利用传递给`bind`的第二个参数，我们可以编写它，以便它可以像`<a v-href="'/my-url'">Go</a>`
一样使用(有关`el`和`binding`的更多信息，请参见[https://vuejs . org/v2/guide/custom-Directive . html # Directive-Hook-Arguments):](https://vuejs.org/v2/guide/custom-directive.html#Directive-Hook-Arguments):)

```
// non-angular-link-directive.js
export const nonAngularLinkDirective = {
  bind(el, binding) {
    el.href = binding.value;
    el.addEventListener("click", event => {
      event.preventDefault();
      window.location.assign(event.target.href);
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以像使用本地指令定义一样使用它:

```
// MyComponent.vue
<template>
  <a v-href="'/my-url'">Go</a>
</template>
<script>
import { nonAngularLinkDirective } from './non-angular-link.directive';
export default {
  directives: {
    href: nonAngularLinkDirective
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

# Vue 指令挂钩和`removeEventListener`🆓

有关指令挂钩的完整列表，请参见[https://vue js . org/v2/guide/custom-directive . html # Hook-Functions](https://vuejs.org/v2/guide/custom-directive.html#Hook-Functions)。

一个好的做法是，当不再需要事件侦听器时，应该将其删除。
这可以在`unbind`中以与添加时相同的方式完成，但是有一个问题，
传递给`removeEventListener`的参数必须与传递给`addEventListener` :
的参数相同

```
// non-angular-link-directive.js
const handleClick = event => {
  event.preventDefault();
  window.location.assign(event.target.href);
};
export const nonAngularLinkDirective = {
  bind(el, binding) {
    el.href = binding.value;
    el.addEventListener("click", handleClick);
  },
  unbind(el) {
    el.removeEventListener("click", handleClick);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，当使用该指令的组件被销毁/卸载
时，将会删除监听器，这样我们就没有挂起的监听器了。

# 妥善处理点击🖱

当锚点包含图像时，会发生边缘情况:事件的`target`不是`anchor`、
，而是没有`href`属性的`img` …。

为了解决这个问题，稍微了解一下`addEventListener`如何调用传递的处理程序，
我们可以重构`handleClick`函数。

```
// non-angular-link-directive.js
function handleClick(event) {
  // The `this` context is the element
  // on which the event listener is defined.
  event.preventDefault();
  window.location.assign(this.href);
}

// rest stays the same 
```

Enter fullscreen mode Exit fullscreen mode

通过使用命名函数和`this`允许事件监听器将
`this`绑定到它所附着的元素，而不是箭头函数的词法`this`。

## 离别的思念📚

我们使用`window.location.assign`以便于测试。使用 Jest 和`@vue/test-utils`，组件级的测试应该是这样的:

```
import { shallowMount } from '@vue/test-utils';
import MyComponent from './MyComponent.vue';

test('It should call window.location.assign with the right urls', () => {
  // Stub window.location.assign
  window.location.assign = jest.fn();

  const myComponent = shallowMount(MyComponent);

  myComponent.findAll('a').wrappers.forEach((anchor) => {
    const mockEvent = {
      preventDefault: jest.fn()
    };
    anchor.trigger('click', mockEvent);
    expect(mockEvent.preventDefault).toHaveBeenCalled();
    expect(window.location.assign).toHaveBeenCalledWith(
      anchor.attributes().href
    );
}); 
```

Enter fullscreen mode Exit fullscreen mode

指令允许您包含与 DOM 交互的代码片段。该代码需要足够通用，以便与指令可用的有限信息一起使用。

通过针对 DOM 编码，我们利用了浏览器 API，而不是重新发明它们。

[订阅](https://buttondown.email/hugo)在你的收件箱里获得最新的帖子(比任何人都要早)。

弗兰克·麦肯纳在 [Unsplash](https://unsplash.com/search/photos/contain?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面照片