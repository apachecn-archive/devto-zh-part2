# React 还是 Vue 还是什么新东西？

> 原文：<https://dev.to/proticm/react-or-vue-or-something-new-3bd>

你好，我的开发伙伴，考虑到你正在读这篇文章，这个标题引起了你的兴趣，不是吗？好吧，坚持到最后，你会发现什么是新的东西。

首先，简单介绍一下我。

我是一个充满激情的软件开发人员，喜欢学习新东西。从早年开始，我就一直在鞭策自己更努力、更聪明地工作。我总是试图改进我的代码，并质疑自己是否可以做得更好或更直观。最重要的是理解代码和一些东西是如何实现的，因为只有这样，你才能开始思考如何改进它。

如果您想知道 Angular 为什么被排除在外，其原因是缺少对开始一个简单项目的好的老方法的支持。我们不能在 HTML 页面上放置一个裸露的脚本标签，然后马上开始编写代码。

好了，是时候进入这篇文章的主题了。

# 反应堆

ReactJS 是一个声明性的、基于组件的 JavaScript 库，用于构建用户界面。这意味着我们有封装逻辑的组件，这些组件稍后用于创建复杂的交互式 ui。

这真的很方便，但我认为这里的主要问题是在 ReactJS 中一切都是 JavaScript。在我看来，这种权衡是不值得的。我的意思是，HTML、CSS 和 JavaScript 这三者到底怎么了？我们甚至有了一种通过 JSX 来表达我们的 UI 的新方法，这使我们远离了标准的 web 开发。有时这可能会派上用场，但模板作为一种简单的替代方式似乎更有吸引力。

现在，让我们用 *Hello World* 的例子来支持这个观点:

首先，我们需要在页面上放置一个元素来挂载:

```
<main id="greeting"></main> 
```

其次，我们需要创建我们的组件:

```
class Greeting extends React.Component {
  render() {
    return (
      <div>
        {this.props.message}
      </div>
    );
  }
};

ReactDOM.render(
  <Greeting message="Hello World!" />,
  document.getElementById('greeting')
); 
```

对于这个例子，我们需要包括三个脚本标签。一个用于 react 本身，一个用于 react-dom，一个用于 babel 编译我们的 ECMAScript 代码。

上面的代码将显示 *Hello World！*浏览器中的消息。如果我们看一下页面源代码，我们会发现我们最终得到了两个 HTML 元素。我们装载的元素、页面上预先呈现的主元素以及组件初始化期间动态创建的 div 元素。

# view . js-检视. js

Vue.js 被定义为构建用户界面的渐进式框架。和 ReactJS 没什么区别，对吧？在 Vue.js 网站的实际对比页面上，有一行文字说两者有很多相似之处。

Vue.js 越来越受欢迎，这并不奇怪，因为它和 ReactJS 一样，是一个很好的框架。它还支持一些组件，当这些组件组合在一起时，就构成了一个交互式 UI。在我看来，Vue.js 比 ReactJS 更直观一点。

我们来看看 Vue.js 是怎么做一个 *Hello World* 的。

我们的 HTML 标记看起来像这样:

```
<main id="greeting">
    <hello-world v-text="message"></hello-world>
</main> 
```

我们的脚本是这样的:

```
Vue.component('hello-world', {
    props: ['message'],
    template: '<div>{{ message }}</div>'
});

new Vue({
    el: '#greeting',
    data: {
        message: 'Hello World!'
    }
}); 
```

对于 Vue.js 示例，我们只需要包含一个用于 Vue 本身的脚本标签。就像我们之前的例子一样，浏览器中显示了 *Hello World* 消息，同样，如果我们看一下页面源代码，我们会看到最后有两个 HTML 元素。不同之处在于，我们通过使用自定义标签来呈现组件，框架在呈现过程中会识别该标签。自定义标记名称映射到组件名称。

# 新的东西，也叫 PlazarJS

对任何和我一起坚持到现在的人来说，太棒了，谢谢你们！是时候说说我在题目里提到的那个新东西了。

这个新事物被称为 PlazarJS，它是一个通用的框架，旨在从应用程序开发的简单性和速度方面丰富开发人员的体验。它是面向对象的，可以很容易地用来创建一个大型的单页面应用程序，也可以集成到需要动态工作流的网页的一部分。

这里的关键词是*简单*，重点是老三样，HTML，CSS，JavaScript。它是基于组件的框架，就像本文开头段落中描述的两个巨人。

现在，让我们看看 PlazarJS 的做事方式，并创建一个 *Hello World* 示例。就像我们在前两个例子中所做的那样，我们需要为我们的组件添加一个 HTML 标记:

```
<main id="greeting"></main> 
```

接下来，我们需要定义并加载我们的组件:

```
pz.define('hello-world', {
    ownerType: 'component',
    renderTo: 'main#greeting',
    template: '<div>{message}</div>',
    viewModel: {
        message: 'Hello World!'
    }
}).create().load(); 
```

对于 PlazarJS 的例子，就像我们使用 Vue.js 的例子一样，我们只需要包含一个用于框架本身的脚本标签。

注意，如果我们将组件的*自动加载*配置设置为*真*，则不需要调用*加载*函数。该组件将在创建时自动加载。此外，我们调用了一个静态方法 *create* ，因为我们想在定义组件后立即创建它。

我们只能定义组件，然后在应用程序中动态地使用定义的类型。

在上面的例子中，我们像前两个一样得到了两个 HTML 元素，但是在这里，我们可以将配置*替换*设置为*真*，这将导致用组件模板替换原始元素。

查看这些和更多 PlazarJS 特性(混合、类、继承、绑定...等)在官方[文档](http://www.plazarjs.com/)网站上。

源代码可以在[这里](https://github.com/ProticM/plazar-js)找到。

这是基于简单的 Hello World 示例的三个框架的快速比较。

感谢您的阅读，祝您好运！