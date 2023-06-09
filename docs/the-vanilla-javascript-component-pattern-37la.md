# 普通 Javascript 组件模式

> 原文：<https://dev.to/megazear7/the-vanilla-javascript-component-pattern-37la>

大约一年前，我开始钻研 web 组件。我真的很喜欢这个想法，即获取对自定义元素的引用，然后调用方法并在自定义元素上设置值。之后，我研究了 Polymer 3.0，它基于许多便利和最佳实践。这些特别出现在模板、生命周期管理和属性/属性反射领域。我从 Polymer 3.0 开始使用 lit-element，最后只使用 lit-html。我继续这个剥离技术的过程，同时留下我学到的模式、方案和最佳实践。我得到的是一个普通的 Javascript 组件模式(我可能需要一个更具体的名字)。

这种模式甚至不使用 web 组件，因为我想要可以跨浏览器部署的东西，而不需要聚合填充或任何需要交付给浏览器的额外代码。这并不困难，也不应该成为在绿地项目中使用 web 组件的障碍，但是我想要可以在任何地方使用的东西。

下面是这种组件的一个非常简单的例子。它使用 ES6 类和普通模板文字来产生标记。它在构造函数中做了一些花哨的事情，这些代码本质上是样板文件，确保每个 DOM 元素只有一个表示它的 JavaScript 对象。这是通过用随机生成的 ID 设置 data-ref 属性来实现的。然后，当使用 ExampleComponent 类并且对于所提供的 DOM 元素已经存在该类的实例时，从构造函数返回对已经存在的对象的引用。这允许一个 DOM 元素被多次传递给这个类的构造函数，并且这个类只存在一个实例。

```
export default class ExampleComponent {
  init(container) {
    this.container = container;
    this.render();
  }

  render() {
    this.container.innerHTML = ExampleComponent.markup(this);
  }

  static markup({}) {
    return `
      <h1>Hello, World!</h1>
    `;
  }

  constructor(container) {
    // The constructor should only contain the boiler plate code for finding or creating the reference.
    if (typeof container.dataset.ref === 'undefined') {
      this.ref = Math.random();
      ExampleComponent.refs[this.ref] = this;
      container.dataset.ref = this.ref;
      this.init(container);
    } else {
      // If this element has already been instantiated, use the existing reference.
      return ExampleComponent.refs[container.dataset.ref];
    }
  }
}

ExampleComponent.refs = {};

document.addEventListener('DOMContentLoaded', () => {
  new ExampleComponent(document.getElementById('example-component'))
}); 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到这呈现了静态的“Hello，World！”`<h1>`标签中的值。然而，如果我们想要一些动态值呢？首先，我们将更新这个类，如下所示:

```
export default class ExampleComponent {
  set title(title) {
    this.titleValue = title;
    this.render();
  }

  get title() {
    return titleValue;
  }

  init(container) {
    this.container = container;
    this.titleValue = this.container.dataset.title;
    this.render();
  }

  render() {
    this.container.innerHTML = ExampleComponent.markup(this);
  }

  static markup({title}) {
    return `
      <h1>${title}</h1>
    `;
  }

  constructor(container) {
    // The constructor should only contain the boiler plate code for finding or creating the reference.
    if (typeof container.dataset.ref === 'undefined') {
      this.ref = Math.random();
      ExampleComponent.refs[this.ref] = this;
      container.dataset.ref = this.ref;
      this.init(container);
    } else {
      // If this element has already been instantiated, use the existing reference.
      return ExampleComponent.refs[container.dataset.ref];
    }
  }
}

ExampleComponent.refs = {};

document.addEventListener('DOMContentLoaded', () => {
  new ExampleComponent(document.getElementById('example-component'))
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们用提供给构造函数的容器 DOM 元素上的 data-title 属性初始化该值。此外，我们提供 setter 和 getter 方法来检索和更新值，每当值更新时，我们重新呈现组件。

但是，如果我们希望子组件作为这个组件的一部分呈现呢？

```
export default class ExampleComponent {
  set title(title) {
    this.titleValue = title;
    this.render();
  }

  get title() {
    return titleValue;
  }

  init(container) {
    this.container = container;
    this.titleValue = this.container.dataset.title;
    this.render();
  }

  render() {
    this.container.innerHTML = ExampleComponent.markup(this);
    this.pageElement = this.container.querySelector('.sub-component-example');
    new AnotherExampleComponent(this.pageElement);
  }

  static markup({title}) {
    return `
      <h1>${title}</h1>
      <div class="sub-component-example"></div>
    `;
  }

  constructor(container) {
    // The constructor should only contain the boiler plate code for finding or creating the reference.
    if (typeof container.dataset.ref === 'undefined') {
      this.ref = Math.random();
      ExampleComponent.refs[this.ref] = this;
      container.dataset.ref = this.ref;
      this.init(container);
    } else {
      // If this element has already been instantiated, use the existing reference.
      return ExampleComponent.refs[container.dataset.ref];
    }
  }
}

ExampleComponent.refs = {};

document.addEventListener('DOMContentLoaded', () => {
  new ExampleComponent(document.getElementById('example-component'))
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，这一次，我们向标记方法添加了一个具有唯一类名的 div。然后，在 render 方法中，我们获取对该元素的引用，并用该 DOM 元素初始化 AnotherExampleComponent。注意:这里我没有为 AnotherExampleComponent 提供实现。最后，如果我们希望我们的组件将事件从组件传播到父组件，或者任何初始化的代码或对组件的引用，该怎么办？

```
export default class ExampleComponent {
  set title(title) {
    this.titleValue = title;
    this.render();
  }

  get title() {
    return titleValue;
  }

  init(container) {
    this.container = container;
    this.titleValue = this.container.dataset.title;
    this.render();
  }

  render() {
    this.container.innerHTML = ExampleComponent.markup(this);
    this.pageElement = this.container.querySelector('.sub-component-example');
    this.clickMeButton = this.container.querySelector('.click-me');
    new AnotherExampleComponent(this.pageElement);

    this.addEventListeners();
  }

  static markup({title}) {
    return `
      <h1>${title}</h1>
      <button class="click-me">Click Me</div>
      <div class="sub-component-example"></div>
    `;
  }

  addEventListeners() {
    this.clickMeButton().addEventListener('click', () =>
      this.container.dispatchEvent(new CustomEvent('click-me-was-clicked')));
  }

  constructor(container) {
    // The constructor should only contain the boiler plate code for finding or creating the reference.
    if (typeof container.dataset.ref === 'undefined') {
      this.ref = Math.random();
      ExampleComponent.refs[this.ref] = this;
      container.dataset.ref = this.ref;
      this.init(container);
    } else {
      // If this element has already been instantiated, use the existing reference.
      return ExampleComponent.refs[container.dataset.ref];
    }
  }
}

ExampleComponent.refs = {};

document.addEventListener('DOMContentLoaded', () => {
  new ExampleComponent(document.getElementById('example-component'))
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们现在添加了一个 addEventListeners 方法，该方法监听组件中的事件。当单击按钮时，它会在容器上调度一个具有自定义名称的事件，这样客户端代码就可以侦听容器上一组专门的自定义命名事件，而不需要知道组件本身的实现细节。也就是说，容器是客户端代码和实现之间的边界。对于数据或事件，类本身不应该到达它自己的容器之外，客户端代码也不应该到达容器内部。所有数据和事件都应该通过 getter 方法的接口和从容器调度的事件提供给客户端。

在没有库、框架或聚合填充的普通 JS 中，所有这些关注点的分离、封装和组合开发都是可能的。正如我一直说的，方案和模式总是比框架和库好。我们也不需要 web 组件来做到这一点。然而，web 组件和库的好处在哪里呢？

首先，web 组件是一种平台增强，它将这里介绍的方案和模式转化为平台的规则。这意味着对于 web 组件，这里显示的关注点的封装和分离不能被客户端代码分解，因为平台会强制执行它。因此，如果可以使用 web 组件，那么应该为 web 组件更新这些最佳实践(关于这一点的博客文章即将发布！).

其次，图书馆会有所帮助。因此，如果您的数据预算中有足够的空间来决定向客户端交付多少代码，有几个库可以帮助我们。目前这个方案除了实际的项目代码本身之外什么都没有，因为不需要库。这种模式的主要问题是呈现标记。目前，重新渲染的成本很高，而且用普通的模板文字表示复杂的视图可能很复杂。然而，我们可以使用标记模板文字库，如 hyperHTML 或 lit-html，以简化渲染过程并加快重新渲染过程。请记住，虽然 hyperHTML 已经生产了一年多，但 lit-html 目前正在 1.0 版本中。

我在[我的博客](https://www.alexlockhart.me/)上有[同样的帖子](https://www.alexlockhart.me/2018/07/the-vanilla-javascript-component-pattern.html)，在那里我谈论了更多关于最新和最伟大的 web 开发*模式超过框架*。