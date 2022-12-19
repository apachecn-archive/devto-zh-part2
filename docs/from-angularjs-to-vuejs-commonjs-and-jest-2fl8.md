# 从 AngularJS 到 Vue.js，CommonJS 和 Jest

> 原文：<https://dev.to/hugo__df/from-angularjs-to-vuejs-commonjs-and-jest-2fl8>

> 启动 AngularJS -> Vue.js 迁移的考验和磨难

AngularJS 相当具有开创性。直到今天，它仍然令人印象深刻，它包含了一个路由器、一个 HTTP 客户端、一个依赖注入系统和一堆我不一定有幸处理过的其他东西。它还将大多数浏览器 API 包装成可注入的服务，这很酷。缺点是它很复杂:服务、过滤器、注入器、指令、控制器、应用程序、组件。

有了今天的工具，AngularJS 不再是一种强有力的做事方式。它处于维护模式的事实说明了一切。一个新的应用程序或新的功能，即。对于一个*没有*处于维护模式的项目来说，它没有像 Angular 2+、React 或 Vue 这样的其他框架和库那么好。应该有办法*而不是*一次性重写整个 AngularJS 应用程序，我是这样做的:

*   运送一些包裹📦
    *   [为什么是 CommonJS 而不是 ES 模块？](https://codewithhugo.com/from-angularjs-to-vue.js-commonjs-and-jest#why-commonjs-instead-of-es-modules)
*   添加视图
*   [开个玩笑🔧](https://codewithhugo.com/from-angularjs-to-vue.js-commonjs-and-jest#setting-up-jest)
    *   [模拟资产](https://codewithhugo.com/from-angularjs-to-vue.js-commonjs-and-jest#mock-assets)
    *   [CommonJS、Webpack 和`vue-jest`灾难](https://codewithhugo.com/from-angularjs-to-vue.js-commonjs-and-jest#commonjs--webpack-and-vue-jest-woes)
    *   [一些解决方案🤔](https://codewithhugo.com/from-angularjs-to-vue.js-commonjs-and-jest#some-solutions)
    *   [用 Jest 预处理器修复`vue-jest` /Webpack CommonJS 处理](https://codewithhugo.com/from-angularjs-to-vue.js-commonjs-and-jest#fixing-vue-jest-webpack-commonjs-handling-with-a-jest-preprocessor)
*   [关于弯道内跑的思考🏃‍](https://codewithhugo.com/from-angularjs-to-vue.js-commonjs-and-jest#thoughts-on-running-vue-inside-angularjs)

[订阅](https://buttondown.email/hugo)在你的收件箱里获得最新的帖子(比任何人都要早)。

# 运送一些包裹📦

捆绑 AngularJS 应用程序允许您发送几个文件，其中包含运行单页面应用程序所需的所有内容。
包括使用`script`减少到几个 JavaScript 包(取决于是否有供应商包)和可能的几个 CSS。

使用 ES6 及更高版本对代码库进行现代化改造通过 transpilation 步骤成为可能，一些 bundler 甚至允许将非 JS 文件加载到 JavaScript 包中，这意味着模板甚至资产可以在同一个有效载荷中发送。

使用 Node (+ JSDOM)在测试环境中加载不依赖于浏览器 API 的 JavaScript 功能成为可能，
让您能够利用 Jest、AVA 或 Mocha(而不是 Jasmine + Karma 甚至量角器)等工具。

这意味着控制器看起来更像下面这样:

```
const angular = require('angular');
function homeController(
  $location,
  otherService
) {
  const ctrl = this;
  // attach methods to ctrl
  return ctrl;
}
angular.module('myApp')
.controller('HomeController', [
  '$location',
  'otherService',
  homeController
]);
module.exports = {
  homeController
}; 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段利用了节点的默认模块系统 CommonJS，其特点是使用了`require()`和`module.exports =`。

为了捆绑应用程序，Webpack 允许我们使用利用 CommonJS 的 AngularJS 代码库，并输出一些应用程序包。
模板可以使用正确的 webpack 加载器(`html-loader`)进行`require` -ed。SCSS 样式表甚至车把模板也可以编译。

## 为什么是 CommonJS 而不是 ES 模块？

ES 模块是 ECMAScript 规范中定义的模块格式。它们看起来如下:

```
import angular from 'angular'
export function homeController() {} 
```

Enter fullscreen mode Exit fullscreen mode

ES 模块的问题是它们是静态导入，理想情况下它们不应该有副作用。
包含一些有`angular.module('some-name')`作用的东西似乎很有副作用，所以 CommonJS 更多地反映了这一点:`require('./this-script-that-adds-something-to-angular')`。

# [t1【添加视图】](#adding-vue)

这部分出乎意料的简单，添加 Vue 组件到 AngularJS 应用程序 ngVue 是可用的([https://github.com/ngVue/ngVue](https://github.com/ngVue/ngVue))。
`ngVue`公开功能，将 Vue 组件包装成 AngularJS 指令。

清单是这样的:

*   `npm i --save ngVue vue vue-loader` (vue-loader 加载/编译`.vue`单个文件组件)
*   将`ngVue`添加到包中:将`require('ngVue')`放在某个地方
*   用 AngularJS `angular.module('myApp', ['ngVue'])`注册`ngVue`
*   创建一个在全局 Vue 实例上注册为组件的 Vue 组件

```
const myComponent = {
  template: '<div>My Component</div>'
};
const MyVueComponent = Vue.component(
  'my-component',
  MyComponent
); 
```

Enter fullscreen mode Exit fullscreen mode

*   将组件注册为 AngularJS 指令

```
angular
.module('myApp')
.directive('myComponent', [
  'createVueComponent' ,
  createVueComponent => createVueComponent(MyVueComponent)
]); 
```

Enter fullscreen mode Exit fullscreen mode

*   在 AngularJS 模板中，您现在可以使用:`<my-component v-props-my-data="ctrl.myData"></my-component>` ( `vprops-*`允许您将数据和函数作为道具从 AngularJS 传递到 Vue)

利用 webpack 加载单个文件组件的完整代码片段:

```
const angular = require('angular');
const { default: Vue } = require('vue');
const { default: MyComponent } = require('./my-component.vue');
const MyVueComponent = Vue.component('my-component', MyComponent)
angular
.module('myApp')
.directive('myComponent', [
  'createVueComponent' ,
  createVueComponent => createVueComponent(MyVueComponent)
]); 
```

Enter fullscreen mode Exit fullscreen mode

为了像上面的例子一样加载单个文件组件，需要`vue-loader`(参见[https://github.com/vuejs/vue-loader](https://github.com/vuejs/vue-loader))，
取决于 webpack 在项目中的设置，它也可以影响你处理 CSS 的方式(因为单个文件组件包含 CSS 以及 JavaScript 和模板)。

# 设置笑话🔧

## 模拟资产

`.html`、`.scss`、`.svg`需要在你的 Jest 配置中模拟:

```
{  "testRegex":  ".*spec.js$",  "moduleFileExtensions":  [  "js",  "vue"  ],  "moduleNameMapper":  {  "\\.(html)$":  "<rootDir>/src/mocks/template-mock.js"  },  "transform":  {  ".*\\.js$":  "<rootDir>/node_modules/babel-jest",  ".*\\.(vue)$":  "<rootDir>/node_modules/vue-jest"  },  "collectCoverageFrom":  [  "src/**/*.{js,vue}"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## CommonJS、Webpack 和`vue-jest`灾难

Webpack 并不关心 CommonJS vs ESM，对于所有意图和目的，Webpack 都将它们视为同一事物。这里有个问题:推荐给 Vue ( `vue-jest`)的 Jest 插件处理`.vue`文件的`require`和`import`的方式与 Webpack 不同。
这是一个 Vue 组件中的示例代码，它导入了 CommonJS 中的另一个 Vue 单个文件组件:

```
const MyOtherComponent = require('./my-other-component.vue').default;

export.default = {
  components: {
    MyOtherComponent
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

问题如下:

*   为了让 Webpack 构建工作，您需要使用`const MyComponent = require('./my-component.vue').default`或`import MyComponent from './my-component.vue'`。
*   为了通过测试，您需要执行`const MyComponent = require('./my-component.vue')`或者使用`import`并使用 Babel 来传输模块
*   AngularJS 控制者喜欢`this` …不知何故通过巴别塔裂缝`this`传送 ES 模块

## 有的解🤔

1.  使用 ES6 导入/导出 Vue 组件和测试，添加特定扩展名(`.mjs`、`.module.js`)，禁用 CommonJS 文件上的`babel-jest`。

    **缺点**:由于以下问题导致覆盖中断(现已修复):[https://github . com/istanbuljs/babel-plugin-Istanbul/pull/141](https://github.com/istanbuljs/babel-plugin-istanbul/pull/141)

2.  在你的测试中使用 Jest:`jest.setMock('./my-component.vue', { default: MyComponent });`。

    **缺点**:这不是一个真正的修复，它让开发者不得不思考 Vue vs 捆绑 JavaScript vs 测试中的 JavaScript，这在大多数情况下应该是一样的。

3.  使用定制的预处理器重写转换后的代码，使其在 Webpack 和`vue-jest`下表现相同。

## 用 Jest 预处理器修复`vue-jest` /Webpack CommonJS 处理

下面的预处理器获取`require('./relative-path').default`并将其转换为`require('./relative-path')`(这似乎是 Webpack 在幕后所做的)。
要使用下面的预处理程序，将 Jest 配置的`"transform"`中的`.vue`匹配行替换为`".*\\.(vue)$": "<rootDir>/vue-preprocessor"`。
这是预处理器的完整代码，下面是代码/方法的演练:

```
// vue-preprocessor.js
const vueJest = require('vue-jest');

const requireNonVendorDefaultRegex = /(require)\('\..*'\).default/g;

const rewriteNonVendorRequireDefault = code =>
  code.replace(requireNonVendorDefaultRegex, match =>
    match.replace('.default', '')
  );

module.exports = {
  process (src, filename, config, transformOptions) {
    const { code: rawCode, map } = vueJest.process(
      src,
      filename,
      config,
      transformOptions
    );
    const code = rewriteNonVendorRequireDefault(rawCode);
    return {
      code,
      map
    };
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在高层次上，我们通过`vue-jest`处理代码，然后将`require('./relative-path').default`重写为`require('./relative-path')`。
这是通过以下方式完成的:

*   `/(require)\('\..*'\).default/g`匹配字符串 arg 以`.`开头的任何`require`，即它将匹配节点模块的本地`require('./something-here')`，但不匹配节点模块的`require`(例如`required('vue')`)。需要注意的是，这个正则表达式只适用于单引号要求……但是如果代码使用双引号，这就很容易解决了。
*   利用函数参数对前一个正则表达式的每次匹配运行自定义替换。用`match.replace('.default', '')`就搞定了。

# 关于跑穴内角的思考🏃‍

AngularJS 来自 bundlers 和 JavaScript 模块系统之前的时代。AngularJS 针对 JavaScript 应用的唯一当代捆绑工具是 Google Closure 编译器。
供参考 Browserify 发布于 2011 年，webpack 发布于 2012 年。AngularJS 的首次发布是在 2010 年。

这就是为什么我们最终为每个控制器和每个模板提供了类似于`script`includes(`script type="ng-template"`)的东西。

每个`script`将调用`angular.module('app').{controller, directive, service}`，每个调用将在全局`angular`实例上注册一些东西，然后可以在其他地方使用。
这很脆弱，因为应该在同一位置的代码分散在代码库中，并被类似`'HomeController'`的字符串引用。
只需一个打字错误，我们就发现了一个错误，直到我们让应用程序处于特定状态时才会被发现…

有了 Vue.js、Webpack 和 Jest，我们可以更有信心地进行捆绑、测试和构建。
安古拉吉斯过去和现在都很棒。多亏了`ngVue`团队，我们还可以逐步迁移。

这意味着我们可以让坚固的 AngularJS 与 Vue 中编写的新特性一起工作。

[订阅](https://buttondown.email/hugo)在你的收件箱里获得最新的帖子(比任何人都要早)。

Justyn Warner 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的封面照片