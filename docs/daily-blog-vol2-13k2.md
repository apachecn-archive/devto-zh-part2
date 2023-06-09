# 每日博客第 2 卷

> 原文：<https://dev.to/hirodeath/daily-blog-vol2-13k2>

# 我对 web 组件的看法

我一直对使用 web 组件感兴趣，将其作为我的业余爱好项目。此外，我已经在我自己出版的书上写了两次了。虽然这让我获得了更多的知识，但我仍然有一个疑问或问题。

## 热模块更换问题。

您是否尝试过为使用 web 组件的项目设置 HMR？我试过了，但对我来说效果不好。老实说，我没有尝试过水疗环境。只是针对静态页面或者简单页面，所以对我来说问题不大。但是如果你(当然包括我)想在 SPA 环境的项目中使用 web 组件呢？不能用 HMR 做 SPA 的环境大概应该是至关重要的。至少我肯定是麻烦的。

## 为什么我们不能在 web 组件项目上使用 HMR？

简单地说，如果自定义元素注册了，你不能撤销它。自定义元素没有从 customElementsRegister 中撤销已定义的自定义元素的规范。这是关键，我们不能用 HMR。如果可能的话，我们可以撤销已定义的定制元素，然后重新定义新的定制元素。让我描述如下。

```
 import { html, render } from 'lit-html';

class SampleBtn extends HTMLElement {
  constructor() {
    super();
    this.shadow = this.attachShadow({ mode: 'open' });
  }

  template() {
    // I assume to change "test!!!" to "this is it!" later for checking whether HMR works or not.
    return html`<div>test!!!</div>`;
  }

  render() {
    render(this.template(), this.shadow);
  }

  connectedCallback() {
    this.render();
  }
}
if (!window.customElements.get('SampleBtn')) {
  window.SampleBtn = SampleBtn;
  window.customElements.define('sample-btn', SampleBtn);
} 
```

这是一个切入点。

```
import './web-components/SampleBtn';

if (module.hot) {
  module.hot.accept('./web-components/SampleBtn.js', () => {
    // Somehow I wanted to enable HMR for web components,
    // fortunately, it did work for javascript, but custom elements didn't update itself.
    // here is empty now, but I tried many things.
    // However, Everything was failed.
  });
} 
```

在`SampleBtn`的代码内部，比如`console.log`被很好地调用，但是如果我改变了`template()`方法的内部，HMR 就不好用了。
我有一种感觉，如果 HMR 不能很好地完成 SPA 环境的 web 组件项目，就像我首先提到的那样不方便。

我应该了解 HMR 是如何工作的，或者是风俗习惯之类的东西，但是我真的很累。这是我今天在 HMR 环境下对 web 组件(自定义元素)的研究。

如果你对我有任何问题或建议，请随时通过这里或 [twitter](https://twitter.com/hirodeath) 联系我！

就是这样！明天见！