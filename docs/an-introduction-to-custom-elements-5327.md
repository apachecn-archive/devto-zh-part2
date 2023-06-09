# 自定义元素

> 原文：<https://dev.to/jamesrweb/an-introduction-to-custom-elements-5327>

## 简介

在我看来，定制元素是 web 组件的一个子集，是 Web 上最酷的东西之一。实际上，它们允许我们构建 web 平台的本地组件，而不是使用中间库或框架，如 React、Angular、Vue 等。

在这篇文章中，我希望向你展示什么是自定义元素，如何构建自定义元素，以及如何将它放到页面上。

## web 组件的基础

所有的定制元素将共享一些公共的方法，这些方法的例子可以在下面的代码示例中看到:

```
class MyComponent extends HTMLElement {
  static get observedAttributes() {
    return [];
  }

  constructor(...args) {
    super(...args);
  }

  connectedCallback() {}

  disconnectedCallback() {}

  adoptedCallback() {}

  attributeChangedCallback(attrName, oldVal, newVal) {}
}

window.customElements.define('my-component', MyComponent); 
```

让我们来分析一下这里到底发生了什么。

### 构造函数()

> 构造函数必须总是被理想地声明，并且任何参数也必须传递给父构造函数。

理想情况下，构造函数总是在任何事件监听器等通常被实现的地方，例如:

```
...
constructor(...args) {
    super(...args);
    this.addEventListener('click', this.handleClick);
}

handleClick(event) {}
... 
```

### connectedCallback()

> 每次将元素插入 DOM 时调用。

每次组件被添加到页面的任何地方，它都会触发这个函数。

### disconnectedCallback()

> 每次从 DOM 中移除元素时调用。

例如，如果我们删除了 DOM 树中的节点或父节点，这个函数将会触发，因为本质上它会从前面提到的树中删除元素。

当元素在文档的其他地方或新页面中被采用时,`disconnectedCallback()`也将运行。

### 采用了回调()

> 每次将自定义元素移动到新文档时调用。

如果自定义元素被移动到新的页面或文档，这个回调将被触发。

### [t1 属性的 change callback(attrname、oldVal、newVal)](#attributechangedcallbackattrname-oldval-newval)

> 当添加、移除、更新或替换元素的属性时，会发生这种行为。

然而，每当组件的一个属性被改变时，这个函数就被触发，只有当被改变的属性当前正在被观察时，这就把我们带到了`observedAttributes()`。

### 观察到的属性()

> 我们实际上想要观察变化的定制元素的属性

正如你所看到的，这个方法被声明为`static get observedAttributes()`，这明显不同于其他方法声明，这是因为我们希望它被任何子类/组件继承，并且我们希望只声明它一次来引用，注意，它是`static`(为所有继承者及其自身设置)和`get`表(用于引用)。

该函数应该返回一个字符串数组，其中每个字符串都是您希望观察的属性的名称，例如:

```
...
static get observedAttributes() {
    return ['id', 'my-custom-attribute', 'data-something', 'disabled'];
}
... 
```

在定制元素规范中还有一些其他的功能，但是这些是我们日常主要使用的功能。

## 基本成分

让我们构建一个向用户问好的基本组件。

### html

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Custom Elements 101
</head>
<body>
  <hello-component data-name="James"></hello-component>
  <script src="./hello-component.js"></script>
</body>
</html> 
```

### JavaScript

```
class HelloComponent extends HTMLElement {
  static get observedAttributes() {
    return ['data-name'];
  }

  // custom methods
  render() {
    this.innerHTML = `Hello ${this.name}`;
  }

  get name() {
    return this.getAttribute('data-name');
  }

  // lifecycle hooks
  connectedCallback() {
    this.render();
  }

  attributeChangedCallback(attrName, oldVal, newVal) {
    this.render();
  }
}

// add into the 'real' dom as a valid tag
window.customElements.define('hello-component', HelloComponent); 
```

加载 index.html，您应该看到页面上显示“Hello James”。

现在，打开检查器(DevTools ),将`data-name`属性改为 James 以外的属性。你会看到我们有内在的反应能力！很可爱，对吧？

当然，这只是一个非常基本的、非最佳实践的、0 用例的、默认的教程实现，但是它确实给了您一个粗略的介绍，我们可以在以后的文章中以此为基础。

## 浏览器支持

以下是当前对 Web 组件的支持，以及促进这些组件的所有 API，包括影子 DOM、自定义元素(我们刚刚研究过的)、HTML 模板和插槽以及 HTML 导入:

[![Current support for Web Components APIs from the caniuse documentation at the time of writing this article](img/73fe62fdeb3b51bf4a2d2a7b131ed52c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ly5BsOXt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/iFidQK/Capture.png)

## 结论

当*需要*时，自定义元素允许我们以一种无框架的方式实现反应式 UI。它们确实给我们带来了很多挑战，其中许多我们将在未来研究，但继续尝试，研究其他 Web 组件 API，因为当它们放在一起时，它们确实允许我们制作酷、强、反应性的元素，这些元素可以用很少的资源做很多事情。

## 资源

*   [Web 组件- MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
*   [自定义元素- MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)
*   [影子 DOM - MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM)
*   [HTML 模板和插槽- MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_templates_and_slots)