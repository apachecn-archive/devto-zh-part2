# ASP.NET MVC 应用中的 Vue 组件

> 原文：<https://dev.to/keco39/vue-components-in-an-aspnet-mvc-app-11jd>

我已经在我的几个专业项目中使用 Vue JS([https://vuejs.org](https://vuejs.org))将近一年了，但从来没有以首选的方式，这意味着它仍然由静态 HTML 组成，Vue 实例只包含脚本和专用的 SASS 文件。由于组件是当今 web 开发的首选方法，我真的想将这种工作方式集成到我的 MVC 应用程序中。

有 SFC(单个文件组件),但是因为你需要 Vue Loader 和 Webpack，所以对于仍然使用 MSBuild 和标准捆绑方式的项目来说，这不是一个真正的选择。所以我继续寻找，但我无法找到一个好的集中解释如何把这一切放在一起。大多数博客都谈到在 SPA 和 Webpack 中使用它们，但从来没有谈到在遗留项目中使用它们…这就是为什么，在最终找到一个好的实现后，我计划写一篇关于它的博文，希望为其他人节省一些时间，这些时间可以花在更有利可图的功能上。

[更新 2019/02/08]我没有停下来寻找 SFC 解决方案，我找到了它，并在这里写下了它……[https://dev.to/keco39/vue-sfcs-in-an-aspnet-mvc-app-3e45](https://dev.to/keco39/vue-sfcs-in-an-aspnet-mvc-app-3e45)

首先，我将从两个步骤开始，这两个步骤仅在您第一次想要将对 Vue 组件的支持添加到您的项目中时需要。

### 第一步——设置打字稿

我使用 TypeScript([https://www.typescriptlang.org](https://www.typescriptlang.org/))作为我的主要脚本语言。它不仅为您提供了强大的输入和改进的智能感知，而且还确保了如果您在 Internet Explorer 中打开您的网站，您的模板字符串仍将工作。这样做的原因是，当在多行中使用一个模板字符串(阅读 https://vuejs.org/v2/guide/components.html 了解更多细节)时，你将需要反斜杠。由于反斜线是 ES6 的一项功能，Internet Explorer 无法识别模板字符串中的字符，因此无法呈现组件。确保在 tsconfig.json 文件中以 ES5 为目标，这样所有的反引号都将被转换成单引号，从而获得对旧浏览器的支持。

### 步骤 2 —添加对 Vue 库的引用

和所有第三方库一样，你需要把 Vue 添加到你网页的底部，在创建和注册你的组件的脚本上面(例如[https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.17/vue.js](https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.17/vue.js))。

一旦这两个步骤完成，除了当你开始一个新项目的时候，你再也不用看它们了。

现在我们来看看最酷的部分，创建和注册你的 Vue 组件。为此，有 4 个步骤。

### 第一步——创建组件

我有一个名为“/js/components”的文件夹，其中创建了一个 TypeScript-file，文件名以 vue-*开头，后面是组件的名称(最好下面的单词也用连字符隔开)。如何创建你的组件可以在[https://vuejs.org/v2/guide/components-registration.html](https://vuejs.org/v2/guide/components-registration.html)的官方文档中找到

例如:

[![](img/a3fd7889a7a8fe9b1664deac488a9e2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hgOnRRsD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/834/1%2AyU2YFMe03drlOojWc-iNbg.png)T3】/js/components/vue-component . ts

我更喜欢将返回值赋给一个变量，原因将在第二步解释。

### 步骤 2 —注册您的组件

由于创建的组件在您的 web 应用程序中仍然未知，您需要通过初始化 Vue 来注册它。为此，我在我的“/js”文件夹中创建了一个不同的文件，名为 component-loader.ts，明确了这个文件是用来注册(从而加载)使用 Vue 的组件的。

例如:

[![](img/3c875e42d24c01a9dbd3d5c56141dfb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xgoy3aLO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/590/1%2Aom-JifInOvzJ8aIjVxUe9g.png)T3】/js/component-loader . ts

没有必要使用“components”选项(传递一个包含所有要注册的组件的对象)，因为没有它，您的所有组件在您的 web 项目中都是已知的，而不仅仅是传递的列表。我更喜欢显式命名，因为我有时会为不同类型的组件创建几个实例(如 myFormComponents、myGalleryComponents 等)。).至于选择器' #main '，这将是组件将被加载的语义元素(主要是一个 div)。

到目前为止，HTML 标签“vue-component”可以在您的项目中使用。

您注意到我将实例的值赋给了一个新变量，这是因为以下(方便的)特性…假设您使用如下标签:

[![](img/6c10e40ede9a1cea135b3f6f885cc90e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---kq2JaF2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0LgeFlBfFEJ-dQAFSHWQfQ.png) 

<figcaption>Vue 组件在 HTML 文档中的用法</figcaption>

HTML 标记“vue-component”(创建组件时的给定名称)是用该组件的名称属性(数据属性)和引用名称创建的。有了这个引用，现在就可以使用您为其分配实例值的变量，后面是$refs，后面是“引用名”，再后面是创建组件时配置的属性。例如。

[![](img/5354f2468a3af05c2a18932c632dfa86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NitnmCBG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/723/1%2AV7xn0_oELzjnPyyxmMK6uA.png)

### 步骤 3 —捆绑

由于捆绑仍然是强制性的，以使您的脚本占用尽可能少，您需要缩小并捆绑生成的 JavaScript 文件(从 TypeScript 转换而来)。在本例中，我将组件和加载器文件添加到包中，并将它们放在 HTML 页面的底部，就在第三方 Vue 库引用的下面。

[![](img/9897fc39f00aafd0c8faca52919ab4a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JGJHLiPg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aus7Pf82Ijkq1ec_FV9wXPw.png) 

<figcaption>Javascript 捆绑组件(。NET 功能)</figcaption>

### [步骤 4-走](#step-4%E2%80%8A%E2%80%8Agit)

为了确保转换后的 JavaScript 文件不会被添加到您的 git-repository 中，请将这些文件添加到. gitignore 文件中。Javascript 文件最好由 CI/CD-build 创建。

[![](img/c126225611c17e0319882bccedb82565.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m3V4m-0o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/310/1%2AdR05mGavMrOs9u_UaZNzzA.png) 

<figcaption>。gitignore 规则排除 JavaScript 文件</figcaption>

因此，您现在有了一个工作的 Vue 组件，它将脚本和 HTML 捆绑到一个文件中，只留下样式位，该样式位仍然放在一个专用的 CSS/SASS 文件中。希望有一天这能结合起来…

另一个具有挑战性的特性是使用 i18n，因为大多数资料只谈论插件，而不是选项，但这是我下一篇博文的内容。

感谢阅读！