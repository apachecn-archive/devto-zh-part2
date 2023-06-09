# 让我们构建 Web 组件！第 4 部分:聚合物库

> 原文：<https://dev.to/bennypowers/lets-build-web-components-part-4-polymer-library-4dk2>

基于组件的 UI 最近非常流行。您知道 web 有自己的本地组件模块，不需要使用任何库吗？真实故事！你可以编写、发布和重用单文件组件，这些组件可以在任何好的浏览器 [*](https://caniuse.com/#feat=shadowdomv1) 和任何框架[中工作(如果那是你的包的话)。](https://custom-elements-everywhere.com/)

在我们的[上一篇文章](https://dev.to/bennypowers/lets-build-web-components-part-3-vanilla-components-4on3)中，我们学习了如何只用 JavaScript 和 <abbr title="document object model">DOM</abbr> <abbr title="application programmer interface">API</abbr> 编写单文件组件。

[![bennypowers](img/57370d6f6dab8d6651a3f138a1aaaa40.png)](/bennypowers) [## 让我们构建 Web 组件！第 3 部分:普通组件

### 本尼·鲍尔斯🇮🇱🇨🇦10 月 5 日 1818 分钟阅读

#webcomponents #customelements #javascript #html](/bennypowers/lets-build-web-components-part-3-vanilla-components-4on3)

今天我们将深入到最初的 web 组件库:Polymer。我们将重构上次构建的`<lazy-image>`组件，以利用 Polymer 的有用特性。我们还将学习如何使用基于聚合物的组件的表达模板系统和双向绑定来构建整个应用程序。我们将看看聚合物团队发布的一些奇妙的现成纸元素。最后，我们将调查一些聚合物项目的有用工具，并了解它们如何对任何 web 组件项目有用，而不仅仅是聚合物应用程序。

*   [聚合物项目](#the-polymer-project)
*   [重构`<lazy-image>`](#refactoring-lazy-image)
    *   [属性](#properties)
    *   [数据绑定模板](#data-binding-templates)
*   [更多聚合物特性](#more-polymer-features)
    *   [高级数据绑定](#advanced-data-binding)
    *   [观察者和计算属性](#observers-and-computed-properties)
    *   [属性描述符](#property-descriptors)
    *   [辅助元素](#helper-elements)
*   [合成聚合物应用程序](#polymer-apps)
*   [纸张元素](#paper-elements)
*   [聚合物工具](#polymer-tools)
    *   [T2`prpl-server`](#prpl-server)
    *   聚合物客户端
    *   [WebComponents.org](#webcomponents-org)

## 高分子工程

[聚合物项目](https://www.polymer-project.org/)早在 2012/2013 年就开始了，目标是提升网络平台的能力。[传说](https://youtu.be/7CUO7PyD5zA?t=69)在谷歌总部的深处，一群 Chrome 浏览器工程师和一群 web 开发人员召开了一次秘密会议，以规划整个 web 的未来路线。

浏览器工程师要求 web 开发人员告诉他们，他们希望 web dev 在五年后会是什么样子，然后他们开始着手构建它。其结果是聚合物库的首次发布和现代 web 组件故事的开始。

从那以后，Polymer 项目又回到了起点，以至于现在根本不用 Polymer Library 就可以编写 web 组件。但是聚合物项目仍然生机勃勃。他们维护着各种各样的 web 平台提案，并倡导比当前流行的更基于标准的 web 开发类型。

另一方面,[聚合物库](https://github.com/Polymer/polymer)已经成为分解 web 组件和基于组件的应用程序的众多选择之一。

所以不要混淆这两件事。**项目**是关于整个平台的，**库**是关于帮助你构建组件的。

## 重构`<lazy-image>`

所以让我们开始吧！既然我们已经开发了我们的`<lazy-image>`香草成分，让我们也以它为基础来探索聚合物。

重构的第一步是安装和导入聚合物库。

```
npm i -S @polymer/polymer 
```

Enter fullscreen mode Exit fullscreen mode

我们还将稍微重命名我们的组件，以帮助我们保持头脑清醒:

```
import { PolymerElement, html } from '@polymer/polymer'

const tagName = 'polymer-lazy-image';

class PolymerLazyImage extends PolymerElement {
  /* ... */
}

customElements.define(tagName, PolymerLazyImage) 
```

Enter fullscreen mode Exit fullscreen mode

Polymer 3.0 和 paper-elements 要求我们对所有模块说明符进行转换，无论是在构建步骤中，还是在服务器运行时。我们将使用`polymer serve`，它为我们动态地转换裸说明符。

```
npm i -D polymer-cli
npx polymer serve 
```

Enter fullscreen mode Exit fullscreen mode

在我们做任何事情之前，我们现在应该采取的另一个重要步骤是调用所有生命周期回调的`super`版本。

```
connectedCallback() {
  super.connectedCallback();
  // ...
}

disconnectedCallback() {
  super.disconnectedCallback();
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

不这样做将会导致问题，因为当生命周期的事情发生时,`PolymerElement`基类需要工作。像处理聚合填充这样的工作，我们不再需要手动完成...

```
connectedCallback() {
  super.connectedCallback();
  this.setAttribute('role', 'presentation');
  if ('IntersectionObserver' in window) this.initIntersectionObserver();
  else this.intersecting = true;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以丢失所有与`shadowRoot`和`ShadyCSS`相关的代码，包括`updateShadyStyles`，因为 Polymer 会为我们处理这些代码。不错！和图书馆一起工作已经让我们忘记了一个压力——支持垃圾邮件。

### 属性

Polymer 允许你静态地声明你的元素的属性，我指的“静态”是指`static get`和“在编写时”。当您在该块中声明一个属性时，Polymer 会为您处理属性和特性的同步。这意味着当我们的元素上的`src`属性被设置时，Polymer 将自动更新元素实例上的`src`属性。

所以现在我们可以删除我们的`attributeChangedCallback`、`safeSetAttribute`，以及所有的 getters 和 setters，用一个静态的属性图来代替它们，这个属性图带有一些特殊的聚合物描述符。

```
static get properties() {
  return {
    /** Image alt-text. */
    alt: String,

    /**
     * Whether the element is on screen.
     * @type {Boolean}
     */
    intersecting: {
      type: Boolean,
      reflectToAttribute: true,
      notify: true,
    },

    /** Image URI. */
    src: String,
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，聚合物绑定到属性，而不是属性。这意味着，如果您在宿主元素的聚合物模板中绑定到元素的属性之一，它不一定会显示为元素的属性。在属性描述符上设置`reflectToAttribute` boolean 确保每当属性改变时，Polymer 也将在元素上设置适当的属性。不过不要担心，即使你用像`propName: String`这样的构造函数声明了一个属性，属性的改变总是会更新相关的属性，不管你是否设置了`reflectToAttribute`。

**注意** : Polymer 会将 camelCase 属性名转换成破折号属性名，反之亦然。顺便说一下，这就是为什么[聚合物库实际上没有通过一些“无处不在的定制元素”测试的原因](https://custom-elements-everywhere.com/libraries/polymer/results/results.html)。

每次属性改变时，`notify` boolean 将使你的元素分派一个定制事件。该事件将被命名为`property-name-changed`，例如`intersecting`属性的`intersecting-changed`，并且它的`detail`属性将拥有一个包含键`value`的对象，该键指向属性的新值。

```
lazyImage.addEventListener('intersecting-changed', event => {
  console.log(event.detail.value) // value of 'intersecting';
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是聚合物双向结合系统的基础。这在这里并不是绝对必要的，但是我们也可以公开这些事件，以防用户想要将图像的`intersecting`状态绑定到一个封闭组件中。

所以现在我们也可以删除`setIntersecting`方法，因为在属性图和 Polymer 模板系统的帮助下，我们不再需要它了。

我们将在稍后的中有更多关于聚合物的属性描述符[。](#property-descriptors)

### 数据绑定模板

我们用一个静态的`template` getter 定义了一个 Polymer 3 元素的模板，它返回一个带标签的模板文字。

```
static get template() {
  return html`
    I'm the Template!
  `;
} 
```

Enter fullscreen mode Exit fullscreen mode

聚合物模板具有一种特殊的语法，让人想起车把或小胡子。单向(数据向下)绑定用双[[方括号]]完成，双向(数据向上)绑定用双`{{`花括号`}}`完成。

```
<some-input input="{{myInput}}"></some-input>

<some-element
    some-property="[[myInput]]"
    some-attribute$="[[myAttribute]]"
></some-element> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，每当`<some-input>`触发一个`input-changed`事件，主机元素就更新`<some-element>`上的`someProperty`属性。用 <abbr title="JavaScript">JS</abbr> 的术语来说，这是一个简单的任务:`someElementInstance.someProperty = this.myInput`。

如果您想绑定到一个属性，而不是一个特性，那么在绑定中添加`$`字符:每当`myOtherProp`改变时，`<some-element>`上的`some-attribute`就会更新:`someElementInstance.setAttribute('some-attribute', this.myOtherProp)`。

类似地，每当`input-changed`自定义事件在`<some-input>`上触发时，主机组件上的`myInput`属性将被设置为事件的`detail.value`属性。

* * *

在我们的`<polymer-lazy-image>`模板中，我们没有使用任何双向绑定，所以我们将坚持使用方括号。

属性提出了一个小挑战。Polymer 用`setAttribute(name, '')`和`removeAttribute(name)`将布尔值绑定到属性。但是由于`aria-hidden`必须接受字符串文字`"true"`或`"false"`，我们不能仅仅将其绑定到`intersecting`的布尔值。`<img/>` `src`同样有趣。实际上，我们只希望在元素相交后设置它。为此，我们需要根据`intersecting`属性的状态来计算图像的 src 属性。

聚合物模板可以包括*计算的绑定*。这些绑定到所选方法的返回值。

```
<img id="image"
    aria-hidden$="[[computeImageAriaHidden(intersecting)]]"
    src="[[computeSrc(intersecting, src)]]"
    alt$="[[alt]]"
/> 
```

Enter fullscreen mode Exit fullscreen mode

在我们的绑定表达式中，这种类似函数的语法是怎么回事？它告诉 Polymer 何时运行哪种元素方法。每当观察到它的依赖项(即绑定表达式中的“传递的参数”)发生变化时，它就会触发，用返回值更新绑定。

还要注意，我们绑定到图像上的`src` *属性*，而不是它的*属性*。这是为了避免试图在 URL `"undefined"`加载图像。

```
computeSrc(intersecting, src) {
  // when `intersecting` or `src` change,
  return intersecting ? src : undefined;
}

computeImageAriaHidden(intersecting) {
  // when `intersecting` changes,
  return String(!intersecting);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，不要被误导，这些不是 JavaScript 表达式，所以你不能传入任何你想要的值:`[[computeImageAriaHidden(!intersecting)]]`不起作用，`[[computeImageAriaHidden(this.getAttribute('aria-hidden'))]]`也不起作用

现在，我们将稍微调整我们的属性映射和样式，以说明元素的 API:

```
static get properties() {
  return {
    // ...

    /** Whether the element is intersecting. */
    intersecting: Boolean,

    /**
     * Whether the image has loaded.
     * @type {Boolean}
     */
    loaded: {
      type: Boolean,
      reflectToAttribute: true,
      value: false,
    },

  };
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<style>
  /* ... */
  #placeholder ::slotted(*),
  :host([loaded]) #image {
    opacity: 1;
  }

  #image,
  :host([loaded]) #placeholder ::slotted(*) {
    opacity: 0;
  }
</style>

<div id="placeholder" aria-hidden$="[[computePlaceholderAriaHidden(intersecting)]]">
  <slot name="placeholder"></slot>
</div>

<img id="image"
    aria-hidden$="[[computeImageAriaHidden(intersecting)]]"
    src="[[computeSrc(intersecting, src)]]"
    alt$="[[alt]]"
    on-load="onLoad"
/> 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们能够大幅减少组件中的样板文件(boilerplate )( T1 ),并通过将它包含在模板中来削减一些多余的逻辑，尽管使用了一些有点烦人的计算绑定助手。

这是我们完成的`<polymer-lazy-image>`模块:

```
import { PolymerElement, html } from '@polymer/polymer';

const isIntersecting = ({isIntersecting}) => isIntersecting;

const tagName = 'polymer-lazy-image';

class PolymerLazyImage extends PolymerElement {
  static get template() {
    return html`
      <style>
        :host {
          position: relative;
        }

        #image,
        #placeholder ::slotted(*) {
          position: absolute;
          top: 0;
          left: 0;
          transition:
            opacity
            var(--lazy-image-fade-duration, 0.3s)
            var(--lazy-image-fade-easing, ease);
          object-fit: var(--lazy-image-fit, contain);
          width: var(--lazy-image-width, 100%);
          height: var(--lazy-image-height, 100%);
        }

        #placeholder ::slotted(*),
        :host([loaded]) #image {
          opacity: 1;
        }

        #image,
        :host([loaded]) #placeholder ::slotted(*) {
          opacity: 0;
        }
      </style>

      <div id="placeholder" aria-hidden$="[[computePlaceholderAriaHidden(intersecting)]]">
        <slot name="placeholder"></slot>
      </div>

      <img id="image"
        aria-hidden$="[[computeImageAriaHidden(intersecting)]]"
        src="[[computeSrc(intersecting, src)]]"
        alt$="[[alt]]"
        on-load="onLoad"
      />
    `;
  }

  static get properties() {
    return {
      /** Image alt-text. */
      alt: String,

      /** Whether the element is on screen. */
      intersecting: Boolean,

      /** Image URI. */
      src: String,

      /**
       * Whether the image has loaded.
       * @type {Boolean}
       */
      loaded: {
        type: Boolean,
        reflectToAttribute: true,
        value: false,
      },

    };
  }

  constructor() {
    super();
    this.observerCallback = this.observerCallback.bind(this);
  }

  connectedCallback() {
    super.connectedCallback();
    // Remove the wrapping `<lazy-image>` element from the a11y tree.
    this.setAttribute('role', 'presentation');
    // if IntersectionObserver is available, initialize it.
    if ('IntersectionObserver' in window) this.initIntersectionObserver();
    // if IntersectionObserver is unavailable, simply load the image.
    else this.intersecting = true;
  }

  disconnectedCallback() {
    super.disconnectedCallback();
    this.disconnectObserver();
  }

  /**
   * Loads the img when IntersectionObserver fires.
   * @param  {Boolean} intersecting
   * @param  {String} src
   * @return {String}
   */
  computeSrc(intersecting, src) {
    return intersecting ? src : undefined;
  }

  /**
   * "true" when intersecting, "false" otherwise.
   * @protected
   */
  computePlaceholderAriaHidden(intersecting) {    
    return String(intersecting);
  }

  /**
   * "false" when intersecting, "true" otherwise.
   * @protected
   */
  computeImageAriaHidden(intersecting) {
    return String(!intersecting);
  }

  /** @protected */
  onLoad() {
    this.loaded = true;
  }

  /**
   * Sets the `intersecting` property when the element is on screen.
   * @param  {[IntersectionObserverEntry]} entries
   * @protected
   */
  observerCallback(entries) {
    if (entries.some(isIntersecting)) this.intersecting = true;
  }

  /**
   * Initializes the IntersectionObserver when the element instantiates.
   * @protected
   */
  initIntersectionObserver() {
    if (this.observer) return;
    // Start loading the image 10px before it appears on screen
    const rootMargin = '10px';
    this.observer = new IntersectionObserver(this.observerCallback, { rootMargin });
    this.observer.observe(this);
  }

  /**
   * Disconnects and unloads the IntersectionObserver.
   * @protected
   */
  disconnectObserver() {
    this.observer.disconnect();
    this.observer = null;
    delete this.observer;
  }
}

customElements.define(tagName, PolymerLazyImage); 
```

Enter fullscreen mode Exit fullscreen mode

检查普通和聚合物版本之间的[差异](http://www.mergely.com/ZC8tDqmJ/?ws=1&cs=1&wl=1&vp=1&rm=1)，并查看工作中的组件:

[https://glitch.com/embed/#!/embed/polymer-lazy-image?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/polymer-lazy-image?previewSize=100&path=index.html)

## 更多高分子特性

聚合物提供了比我们简单的示例元素更容易证明的东西。一个小例子是聚合物将模板中所有的元素映射到一个名为`$` :
的物体上

```
<paper-button id="button">Button!</paper-button>
<paper-input id="input" label="Input!"></paper-input> 
```

Enter fullscreen mode Exit fullscreen mode

```
connectedCallback() {
  console.log(this.$.button.textContent) // "Button!"
  this.$.input.addEventListener('value-changed', breakTheInternet);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 高级数据绑定

聚合物也可以通过特殊的语法绑定到非聚合物元素事件的宿主属性:

```
<video current-time="{{videoTime::timeupdate}}"/> 
```

Enter fullscreen mode Exit fullscreen mode

这意味着“当`timeupdate`事件触发时，将本地`videoTime`属性分配给视频元素的`currentTime`”。

在`<polymer-lazy-image>`的后续版本中，我们可能会使用这些类型的绑定来同步内部`<img>`属性和我们自己的属性。

对于 Polymer 的数据绑定系统的内幕，请阅读文档。

### 观察者和计算属性

计算属性和绑定是聚合体*观察者*的特殊情况。一个简单的观察者看起来是这样的:

```
static get properties() {
  return {
    observed: {
      type: String,
      observer: 'observedChanged',
    },
  };
}

observedChanged(observed, oldVal) {
  console.log(`${ observed } was ${ oldVal }`);
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以定义复杂的观察器，这些观察器接受多个依赖项或深入观察对象或数组。

```
static get properties() {
  return {
    observed: Object,
    message: {
      type: String,
      value: 'A property of observed has changed',
    },
  };
}

static get observers() {
  return [
    // careful: deep observers are performance intensive!
    'observedChanged(message, observed.*)'
  ],
}

observedChanged(message, { path, value, base }) {
  // path: the path through the object where the change occurred
  // value: the new value at that path
  // base: the root object e.g. `observed`
  console.log(message, path + ': ' + value);
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以设置计算属性，类似于计算绑定:

```
static get properties() {
  return {
    theString: String,
    theLength: {
      type: Number,
      computed: 'computeTheLength(theString)',
    },
  };
}

computeTheLength(theString) {
  return theString.length;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，只要`theString`发生变化，`theLength`就会根据`computeTheLength`进行更新。

然后，这些计算出的属性可以像任何普通属性一样绑定到您的模板。

```
<span>[[theString]] has [[theLength]] characters</span> 
```

Enter fullscreen mode Exit fullscreen mode

阅读[文档](https://www.polymer-project.org/3.0/docs/devguide/observers)中关于聚合物观察者的所有内容。

### 属性描述符

我们已经看到了如何设置`reflectToAttribute`和`notify`在我们的值更新时影响外部世界，以及如何用`observer`描述符设置简单的观察器。

您还可以用`value`设置一个默认值，它可以是一个文字值，也可以是一个函数。

```
static get properties() {
  return {
    prop: {
      type: String,
      value: '🚣‍♂️'
    },

    things: {
      type: Array,
      value: () => [],
    },
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

**小心！**当你想用引用类型`Array`或`Object`设置默认值时，一定要传递一个函数，否则*你的元素*的每个实例将共享同一个引用。

组件初始化时，赋值被设置一次，然后不再更新。如果您需要在连接后动态设置属性，请使用[计算属性](#observers-and-computed-properties)或观察器。

### 辅助元素

Polymer 附带了一些助手元素，您可以在模板中使用它们来减少您需要编写的命令式 JavaScript 的数量。最常用的两个是用于遍历列表和输出 DOM 的`<dom-repeat>`，以及用于条件呈现的【T1:

```
<!-- Will output a new article with h2 and img for each post -->
<dom-repeat items="[[posts]]" as="post">
  <template>
    <article>
      <h2>[[post.title]]</h2>
      <img src$="[[post.picture]]">
    </article>
  </template>
</dom-repeat>

<!-- Will only render it's template if conditionDepending(someProp, another) is truthy -->
<dom-if if="[[conditionDepending(someProp, another)]]">
  <template>
    I'm a very lucky textNode to have [[someProp]] and [[another]] on my side.
  </template>
</dom-if> 
```

Enter fullscreen mode Exit fullscreen mode

要使用这些助手，请确保导入它们

```
import '@polymer/polymer/lib/elements/dom-repeat.js';
import '@polymer/polymer/lib/elements/dom-if.js'; 
```

Enter fullscreen mode Exit fullscreen mode

有关辅助元素的更多信息，请参见[聚合物文档](https://www.polymer-project.org/3.0/docs/devguide/templates)。

## 合成高分子应用程序

当涉及到分解整个应用程序时，Polymer 确实大放异彩。Polymer 项目开创了一种相当进步和明显特殊(抱歉)的声明式应用程序结构，主要基于 HTML 元素。聚合物方法利用 HTML 内置的可组合性，使得“一切都是元素”。例如，有一个`<iron-ajax>`元素，它可以获取资源并将它们暴露给 Polymer 的数据绑定。

```
<iron-ajax auto
    url="/api/posts"
    handle-as="json"
    last-response="{{posts}}"></iron-ajax>

<dom-repeat items="[[posts]]" as="post">
  <template>
    <article>
      <h2>[[post.title]]</h2>
      <img hidden$="[[!post.cover]]" src$="[[post.cover]]">
      [[post.body]]
    </article>
  </template>
</dom-repeat> 
```

Enter fullscreen mode Exit fullscreen mode

但是以我的拙见，这种方法最好的例子来自于`<app-route>`元素和[封装路由](https://www.polymer-project.org/blog/routing) :
的想法

```
<!-- <app-shell> template -->

<!-- Capture and expose address-bar changes -->
<app-location route="{{route}}"></app-location>

<app-route route="[[route]]"
    data="{{routeData}}"
    tail="{{pageTail}}"
    pattern="/:page"></app-route>

<!-- Composed routing! -->
<app-route route="[[tail]]"
    data="{{itemData}}"
    tail="{{itemTail}}"
    pattern="/:itemId"></app-route>

<iron-pages selected="{{routeData.page}}" attr-for-selected="name">
  <app-master name="master"></app-master>
  <app-detail name="detail"
      item-id="[[itemData.itemId]]"
      route="[[itemTail]]"></app-detail>
</iron-pages> 
```

Enter fullscreen mode Exit fullscreen mode

使用 app-route 和 iron-pages 元素，我们有一个完整的路由解决方案，它将根据 URL 隐藏和显示内容，甚至将与路由相关的数据传递给这些视图组件。

由于`<app-route>`将它的`route`属性作为数据，而不是直接绑定到`window.location`，你可以将部分路径传递给子视图，让它们管理自己的内部状态和自己的`<app-route>`子视图。整洁！

```
<!-- <app-detail> template -->
<app-route route="[[route]]"
    data="{{routeData}}"
    pattern="/:editing"></app-route>

<item-detail hidden$="[[routeData.editing]]"></item-detail>
<item-editor hidden$="[[!routeData.editing]]"></item-editor>

<paper-checkbox checked="{{routeData.editing}}">Editing</paper-checkbox> 
```

Enter fullscreen mode Exit fullscreen mode

多酷的概念啊！

**Note** that for the sake of brevity, we're binding directly to subproperties of `routeData` in this example, but in a real project we'd add some helper methods to compute an intermediate `page` property from `routeData`.

对于这种类型的应用架构的完全实现的例子，请参见 GitHub 上古老的[聚合物初学者工具包](https://github.com/polymer/polymer-starter-kit)。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [聚合物](https://github.com/Polymer) / [聚合物启动器套件](https://github.com/Polymer/polymer-starter-kit)

### 聚合物应用的起点

<article class="markdown-body entry-content container-lg" itemprop="text">

# 聚合物应用工具箱-入门套件

[![Build Status](img/b492b20800bd2e6d7e11d9898002675f.png)T2】](https://travis-ci.org/Polymer/polymer-starter-kit)

该模板是使用基于抽屉的布局构建应用程序的起点。布局由`app-layout`元素提供。

该模板以及`polymer-cli`工具链还展示了“PRPL 模式”的使用。该模式允许在用户请求的初始路径快速首次交付内容并与之交互，同时通过预缓存应用程序所需的剩余组件并在用户导航应用程序时逐步按需加载它们来快速进行后续导航。

简而言之，PRPL 模式:

*   **按下初始路线所需的**组件
*   **尽快渲染**初始路线
*   **预缓存剩余路线的**组件
*   **延迟加载**并按需逐步升级下一条路线

### 设置

##### 先决条件

使用 [npm](https://www.npmjs.com) 安装 [Polymer CLI](https://github.com/Polymer/polymer-cli) (我们假设你已经预装了 [node.js](https://nodejs.org) )。

```
npm install -g polymer-cli 
```

##### 从模板初始化项目

```
mkdir my-app
cd my-app
polymer init polymer-3-starter-kit 
```

### 启动开发服务器

该命令用于…

</article>

[View on GitHub](https://github.com/Polymer/polymer-starter-kit)

## 纸元素

如果我们不提到聚合物项目发布的材料设计 UI 组件集 [Paper Elements](https://www.webcomponents.org/collection/polymerelements/paper-elements) ，这就不是一篇关于聚合物的博客文章。但是，如果我们没有把一件事弄得非常清楚，我们也会犯一个巨大的错误:

```
PaperElements != Polymer; 
```

Enter fullscreen mode Exit fullscreen mode

不使用纸元素也可以很好地使用聚合物库，不使用聚合物库也可以很好地使用纸元素！

```
<head>
  <script type="module" src="https://unpkg.com/@polymer/paper-checkbox/paper-checkbox.js?module"></script>
  <script type="module" src="https://unpkg.com/@polymer/paper-card/paper-card.js?module"></script>
  <script type="module" src="https://unpkg.com/@polymer/paper-button/paper-button.js?module"></script>
</head>  
<body>
  <paper-card heading="Am I Checked?">
    <div class="card-content">
      Output: <span id="output">Not Checked</span>
    </div>
    <div class="card-actions">
      <paper-checkbox id="input">Check me!</paper-checkbox>
      <paper-button raised disabled id="button">Reset</paper-button>
    </div>
  </paper-card>
  <script>
    const onClick = () => input.checked = false;
    const onInput = ({detail: { value }}) => {
      output.textContent = value ? 'Checked' : 'Not Checked';
      button.disabled = !value;
    }

    input.addEventListener('checked-changed', onInput);
    button.addEventListener('click', onClick);
  </script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode[https://glitch.com/embed/#!/embed/possible-yoke?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/possible-yoke?previewSize=100&path=index.html)

我们在这里失去的是使用 Polymer 的数据绑定系统的能力。但是——你猜对了——有一个元素，叫做 [`<dom-bind>`](https://www.polymer-project.org/3.0/docs/api/elements/dom-bind)

如果你正在寻找一个基于材料设计的界面，不要大惊小怪——试试纸元素。

## 高分子工具

除了他们的宣传工作、JS 和组件库以及标准提案之外，Polymer Project 还发布了各种工具，帮助您构建、发布和服务您的应用程序和组件。

### `prpl-server`

Chrome 团队开发了 [PRPL 模式](https://developers.google.com/web/fundamentals/performance/prpl-pattern/)，作为编写和交付高性能网络应用的最佳实践。使向有能力的浏览器提供最小的有效捆绑包变得容易，同时仍然支持具有较大捆绑包的旧浏览器。有一个现成的二进制和一个快速中间件库。[试试看](https://github.com/Polymer/prpl-server)。

### 聚合物 CLI

Vue CLI 帮助您开发 Vue 应用程序。Angular CLI 帮助您开发 Angular 应用程序。`create-react-app`帮助您开发 React 应用。

Polymer CLI 帮助您开发 *web* 应用程序。

诚然，它提供了 Polymer 3 元素和应用程序的模板，但这还不是全部。`polymer build`和`polymer serve`命令将构建和服务任何 web 组件应用程序。传输是可选的。事实上，CLI 对您的代码做的唯一一件事就是将类似`import { PolymerElement } from '@polymer/polymer';`的裸模块说明符替换为浏览器可以直接加载的相对 URL。

> 什么！？你是说没有网络包？没有巴别塔？没有时间纠结于与我的应用程序代码无关的配置文件和 API？

是啊。这正是我要说的。下次你有一个应用程序项目时，考虑用 web 组件和 Polymer CLI 进行分解。

但是如果你*想让*移植到老版本的浏览器上(见上面的 [`prpl-server`](#prpl-server) ，你可以定义一个`polymer.json`的`builds`部分:

```
{  "root":  "~/projects/my-project",  "entrypoint":  "index.html",  "shell":  "src/my-project.js",  "sources":  [  "src/my-project.js",  "manifest/**",  "package.json"  ],  "builds":  [{  "name":  "es5prod",  "preset":  "es5-bundled",  "addServiceWorker":  true  },  {  "name":  "es6prod",  "preset":  "es6-unbundled",  "addServiceWorker":  true  },  {  "name":  "dev",  "addServiceWorker":  false,  "js":  {"minify":  false,  "compile":  false},  "css":  {"minify":  false},  "html":  {"minify":  false},  "bundle":  false,  "addPushManifest":  false  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

然后你只需配置`prpl-server`为现代浏览器提供`es6prod`,为 IE 和朋友提供`es5prod`,你就可以开始比赛了。

给他们读博士，博士！

### WebComponents.org

在你跑去实现你心中的`<super-button>`之前，为什么不在[webcomponents.org](https://webcomponents.org)搜索一下，这是最大的网络组件目录。
每个元素都显示了其文档、公共 API 和安装方法。你还可以找到 npm 和 github 的链接。
如果你是组件作者，不要犹豫！[发布您的组件](https://webcomponents.org/publish)供他人受益。

## 结论

不可否认，聚合物库走在了时代的前面。它采取的方法是对网络平台提出更高的要求，然后将其变为现实，而不是仅仅解决平台的局限性。

既然 web 组件得到了广泛的支持，那么聚合物库在我们的 web-dev 工具箱中还有一席之地吗？确实如此！一些项目自然会采用 Polymer 的声明式风格。一些团队将发现设计者和文档作者如何使用 Polymer 的表达绑定系统来完成开发人员的工作。

这不都是 <abbr title="sunshine">☀️</abbr> 和<abbr title="roses">🌹🌹</abbr>虽然。随着平台和更广泛的网络社区的发展，聚合物项目的优先权也在发展。Polymer 3 可能是该库的最后一个主要版本，同样，3.0 系列也将是 paper-elements 的最后一个版本。

因此，让我们回顾一下聚合物库的优缺点:

| 赞成的意见 | 骗局 |
| --- | --- |
| 表达模板系统 | 不能将 JS 直接传递给模板 |
| 观察器和计算属性，声明性事件侦听器 | 庞大的依赖链刺激了更大的纯聚合物应用 |
| 声明式应用程序结构的超酷和独特的方法 | 不管是好是坏，这种独特的声明式风格不像其他架构那样受欢迎 |
| 一个成熟的库和组件集。经过尝试、测试和验证 | Polymer.js 已经过时，除非被分叉，否则不会有新特性 |

那么这是否意味着 Web 组件的终结呢？*见鬼*不！聚合物远不是唯一的游戏。一个名为 [`lit-html`](https://polymer.github.io/lit-html) 的轻量级声明性 JS 模板库和一个名为 [`LitElement`](https://github.com/polymer/lit-element) 的利用它的定制元素基类是新的热点。如果顺利的话，我们将在下一期文章中讨论它们。

再见😊

您想就此处涉及的任何主题进行一对一的辅导吗？[![Contact me on Codementor](img/97f0e0737ce864c47f2412396b01e737.png)T2】](https://www.codementor.io/bennyp?utm_source=github&utm_medium=button&utm_term=bennyp&utm_campaign=github)

## 鸣谢

感谢 Pascal Schilp 和 [@ruphin](https://github.com/ruphin) 的建议和修正，排名不分先后。

查看本系列的下一篇文章

[![bennypowers](img/57370d6f6dab8d6651a3f138a1aaaa40.png)](/bennypowers) [## 让我们来构建 Web 组件！第五部分:文学元素

### 本尼·鲍尔斯🇮🇱🇨🇦10 月 22 日 1818 分钟阅读

#webcomponents #functional #lithtml #javascript](/bennypowers/lets-build-web-components-part-5-litelement-906)