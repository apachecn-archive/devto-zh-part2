# 通用灵活的项目结构，适用于任何生态系统中的所有项目。

> 原文：<https://dev.to/nullvoxpopuli/a-general-and-flexible-file-structure-that-works-for-all-projects-in-any-ecosystem-1lp9>

引用另一篇类似主题的文章:

> 理想的结构是允许你以最少的努力在你的代码中移动的结构。
> 
> - [*构建 React 应用的 100%正确方法...*T3】](https://hackernoon.com/the-100-correct-way-to-structure-a-react-app-or-why-theres-no-such-thing-3ede534ef1ed)

为什么要担心文件夹/文件结构呢？这似乎是一个很难解决的问题。当没有限制时，几乎每个人对“事物”应该如何命名以及它们应该生活在哪里都有不同的想法。为了让每个人都在同一页上实现最大的项目一致性，应该事先商定一个结构。

[有](https://reactjs.org/docs/faq-structure.html) [有](https://daveceddia.com/react-project-structure/) [多](https://levelup.gitconnected.com/structure-your-react-redux-project-for-scalability-and-maintainability-618ad82e32b7) [题目](https://survivejs.com/react/advanced-techniques/structuring-react-projects/) [上](https://hackernoon.com/the-100-correct-way-to-structure-a-react-app-or-why-theres-no-such-thing-3ede534ef1ed) [文件](https://blog.bitsrc.io/structuring-a-react-project-a-definitive-guide-ac9a754df5eb) [结构](https://medium.com/@alexmngn/how-to-better-organize-your-react-applications-2fd3ea1920f1)。[无](https://medium.com/ottofellercom/how-to-structure-large-react-apps-440b0e012d80)[同意](https://labs.mlssoccer.com/a-javascript-project-structure-i-can-finally-live-with-52b778041b72)。[有些](https://gist.github.com/tracker1/59f2c13044315f88bee9) [可能](https://www.oreilly.com/library/view/maintainable-javascript/9781449328092/ch13.html) [有](https://wecodetheweb.com/2015/05/28/how-to-structure-your-front-end-application/) [有些](https://itnext.io/how-to-structure-a-vue-js-project-29e4ddc1aeeb) [类似](https://kamranahmed.info/blog/2014/08/07/how-to-structure-your-javascript/) [的概念](https://css-tricks.com/how-do-you-structure-javascript-the-module-pattern-edition/)。[有些](https://lostechies.com/derickbailey/2012/02/02/javascript-file-folder-structures-just-pick-one/) [可能](http://cassandrawilcox.me/setting-up-a-node-js-project-with-npm/) [被](https://neutrinojs.org/project-layout.html)[过于宽松](https://www.sitepoint.com/anatomy-of-a-modern-javascript-application/) [到](https://vuejs-templates.github.io/webpack/structure.html) [被](https://www.sohamkamani.com/blog/2015/08/21/frontend/) [抵得上](https://scotch.io/tutorials/angularjs-best-practices-directory-structure) [而](http://read.humanjavascript.com/ch04-organizing-your-code.html)。[最终还是](https://engineering.opsgenie.com/how-to-organize-react-files-before-its-messed-up-c85387f691be)，[当](https://www.bignerdranch.com/blog/javascript-project-configuration/)，[面临](https://en.bem.info/methodology/filestructure/)，，[，](https://www.reddit.com/r/reactjs/comments/8ogngn/what_is_the_most_efficient_folder_structure_for_a/)，[，](https://tech.offgrid-electric.com/domain-directory-structure-for-react-apps-why-its-worth-trying-b3855ee77a1e)，[选择](https://www.nylas.com/blog/structuring-a-complex-react-redux-project)，，[，](https://blog.usejournal.com/folder-structure-in-react-apps-c2ae8974d21f)，[摆](https://expertise.jetruby.com/how-to-properly-structure-your-react-applications-5609ad3f2ee6)，，[档](https://geeks.uniplaces.com/how-to-keep-your-ember-js-project-clean-and-well-structured-fbff040274de)，[大家的](https://deaddesk.top/choosing-the-proper-redux-project-structure/)

那么，*这篇*文章会有什么不同呢？我的目标是定义一套标准来评估一个文件夹/文件结构，然后描述一个合理的结构起点，它可以作为任何生态系统中任何单页应用程序的基础——React、Vue、Angular 或 Ember。

首先，让我们定义评估结构的标准。

1.  用户应该能够维护他们的应用程序，而不用担心他们的导入结构会阻止他们做出改变。
2.  相关文件应该是可发现的，这样，如果用户没有使用 TypeScript(您可以使用“Go to definition ”),他们就不需要寻找文件
3.  相关文件应该是可访问的，这样用户就可以很容易地找到相关文件，而不需要任何 IDE 功能(例如:在 github 上浏览)。
4.  用户在他们的项目层次结构中的任何级别都应该有合理的上下文。展平太多*会*压倒一切，降低维护、发现和访问的能力。
5.  重构项目的各个部分应该很容易。将目录移动到新位置时，内部行为应该保持正常。
6.  添加新东西的正确方式和位置应该是显而易见的，结构不应该考虑不必要的决定。
7.  测试和样式应该与侧边组件放在一起。
8.  避免臭名昭著的“标题栏问题”，在编辑器中无法区分一堆名称相同的文件(尽管，这种情况很多都是基于编辑器的)
9.  这个结构不应该强加会阻止技术进步的限制——比如在还没有代码分割的项目中添加代码分割。

## 通用的足以满足所有应用的布局:

请注意,`{folder-name}/component.js,template.hbs`的任何组合都应该是同义词:

*   反应:`{folder-name}/index.jsx,display.jsx`
*   视图:t0]
*   棱角分明:`{folder-name}/component.js,template.html`
*   烬:`{folder-name}/component.js,template.hbs`
*   等等

另外，注意这些例子都是简写的，一些项目(尤其是角度项目)喜欢用*非常*明确地命名，比如`ComponentName/ComponentName.Component.js`。

```
src
├── data
├── redux-store
├── ui
│   ├── components
│   │   └── list-paginator
│   │       ├── paginator-control
│   │       │   ├── component.js
│   │       │   └── template.hbs
│   │       ├── component.js
│   │       ├── integration-test.js
│   │       └── template.hbs
│   ├── routes
│   │   ├── login
│   │   │   ├── acceptance-test.js
│   │   │   ├── route.js
│   │   │   └── template.hbs
│   │   └── post
│   │       ├── -components
│   │       │   └── post-viewer
│   │       │       ├── component.js
│   │       │       └── template.hbs
│   │       ├── edit
│   │       │   ├── -components
│   │       │   │   ├── post-editor
│   │       │   │   │   ├── calculate-post-title.js
│   │       │   │   │   ├── component.js
│   │       │   │   │   └── template.hbs
│   │       │   │   ├── route.js
│   │       │   │   └── template.hbs
│   │       │   ├── route.js
│   │       │   └── template.hbs
│   │       ├── route.js
│   │       └── template.hbs
│   ├── styles
│   │   └── app.scss
│   └── index.html
└── utils
    └── md5.js 
```

Enter fullscreen mode Exit fullscreen mode

从上到下浏览文件夹，因为 dev.to 不允许没有代码栅栏的内联链接...(其中一个 [prism.js'](https://prismjs.com/) 插件的一大特色。

### `src`

其中大部分将集中在`src`目录，因为任何其他顶级文件夹或文件都更倾向于特定于项目或生态系统，通常可能不会转化为跨生态系统的项目。由于特定于项目或特定于构建配置的原因而不能翻译的那些文件夹的一些例子有:`app/`、`tests/`、`vendor/`、`public/`、`config/`、`translations/`等。

### `src/data`

这个目录用于所有 api 相关的数据交互和表示。在一个有模型-适配器-序列化器模式的应用程序中，你可能希望在`src/data`中有额外的文件夹，比如`models`或者`transforms`，这取决于你希望在你的应用程序中有多少规范化。这就是为什么命名更具体或更模糊的东西不一定有意义。

### `src/redux-store`

如果使用 redux，大多数指南和教程只是使用相同的`store`，这可能是不明确的，因为`store`是任何维护数据缓存的库使用的构造。所以不仅在 [Redux](https://redux.js.org/api/store) 里，在 [Orbit.js](http://orbitjs.com/v0.15/guide/#Orbit-primitives) 里，还有 [ember-data](https://guides.emberjs.com/release/models/#toc_the-store-and-a-single-source-of-truth) 里。

关于应用级状态管理的更多信息，请看这篇文章比较 React 和 Ember 的状态管理

### `src/ui`

直接影响显示的所有内容都应该放在`ui`文件夹中。这包括样式、零部件和管线。用户界面可以独立于数据、应用程序状态和实用程序而存在。

### `src/ui/routes`

大多数单页应用程序使用某种路由器，因此用户界面完全基于路由。显示哪些元件取决于哪些管线处于活动状态。由于显示的这种耦合，以及随之而来的浏览器 URL 的行为，按照自然的路径边界来划分你的应用应该是很自然的。按路由分割 UI 也有助于在路由边界上直接进行代码分割。

### `src/ui/routes/{route-name}/-components`

在最近的 React 项目中，我试图省略路由级私有组件目录，但是这导致了路由的目的和支持路由上呈现的内容之间的混淆。我最初省略了`-components`目录，认为如果我/我的团队使用正确的文件夹，[事情就不会如此糟糕](https://github.com/sillsdev/appbuilder-portal/tree/ee84202aa0717191c03a12f51c5542cfb02222c5/source/SIL.AppBuilder.Portal.Frontend/src/ui/routes/project)。

一个页面的例子是标签导航:

```
posts/post
├── view/
├── comment-moderation/
├── publishing-options/
│   ├── -components/
│   │    ├── confirm-publish-modal.jsx
│   │    └── social-media-blast-options.jsx
│   └── index.jsx
└── edit/
    ├── -components/
    └── index.jsx 
```

Enter fullscreen mode Exit fullscreen mode

这种结构不像上面的链接( *[事情不会那么糟糕](https://github.com/sillsdev/appbuilder-portal/tree/ee84202aa0717191c03a12f51c5542cfb02222c5/source/SIL.AppBuilder.Portal.Frontend/src/ui/routes/project)* )，这种结构有着清晰、明确的组件和路由特定组件的分离。在[链接的 react 应用](https://github.com/sillsdev/appbuilder-portal/tree/ee84202aa0717191c03a12f51c5542cfb02222c5/source/SIL.AppBuilder.Portal.Frontend/src/ui/routes/project)中，由于其一次性使用的性质，我也一直在尝试将仅本地高阶组件(hoc)保留在顶级路由级别——尽管在这个特定的应用中，[常用的](https://github.com/sillsdev/appbuilder-portal/blob/ee84202aa0717191c03a12f51c5542cfb02222c5/source/SIL.AppBuilder.Portal.Frontend/src/ui/routes/project-directory/index.tsx#L96)hoc 被移动到数据目录中。我仍然在尝试这个想法，但是特设的位置更适合功能性的单页面应用，比如那些基于 react 的应用。

判断你的结构是否朝着正确的方向发展的一个标准是你在导入路径中使用`../`或`../../`的频率。使用向上反向相对路径违反了我们的`Goal #5`,即任何子树都可以改变位置，并且内容的功能应该保持在工作状态。上面的例子不应该固有地具有任何反向相对路径。

违反`Goal #5`的例子:

```
posts/post
├── view/
├── comment-moderation/
├── publishing-options/
│   └── index.jsx
├── confirm-publish-modal.jsx
├── social-media-blast-options.jsx
└── edit/
    └── index.jsx 
```

Enter fullscreen mode Exit fullscreen mode

这里，`publishing-options`文件必须使用`../`来访问父级定义的组件。

### `src/utils`

任何函数、类或实用程序都应该存在于`src/utils`中。这些文件应该是纯单元可测试的，因为它们应该没有应用程序依赖性。这包括字符串格式转换、auth0 包装器、`fetch`抽象等等。

### 整体

让我们回顾一下我们的目标，看看这个建议的布局如何满足每个目标:

用户应该能够维护他们的应用程序，而不用担心他们的应用程序的结构会妨碍他们做出改变。

实现这一目标主要是通过简单地让*任何*被记录的约定可以在以后被引用。目前还没有通用的静态分析工具来帮助*实施*一个结构——尽管，有一个工具可以用于一个主要的框架来规定结构。(参见下面的*实施*)

**2)**

通过让相关文件在这种布局中彼此相邻，一切本质上都是上下文相关的。如果有人是一个沉重的文件树/项目树浏览器，他们将有一个轻松的时间来导航和发现他们正在做什么和涉及什么。

**3)** *相关文件应该是可访问的，这样用户不需要任何 IDE 特性(例如:在 github 上浏览)就可以轻松定位相关文件。*

这与(2)有关，但更多的是实施协同定位。当在没有编辑器或 typescript 功能的情况下快速在线浏览文件时，可以方便地点击尽可能少的网页来查看相关组件。

**4)** *用户应该看到在他们的项目层次结构中的任何级别都有合理的上下文。过度扁平化会让人不知所措，降低维护、发现和访问的能力。_*

通过路由的嵌套结构，任何只在一个地方使用的组件都将根据上下文协同定位。这使得大型平面文件夹的数量保持在最低限度，并允许理解应用程序的更好的设计，而不必到处遵循参考。兄弟文件夹将被视为完全不相关(收养？).

**5)** *重构项目的各个部分应该很容易。将目录移动到新位置时，内部行为应该保持正常。*

我希望这是不言自明的，但是这种文件夹/文件结构允许拖放重构，任何移动的文件夹都应该通过所有的内部测试。

添加新东西的正确方式和位置应该是显而易见的，一个结构不应该考虑不必要的决定。

这在一定程度上依赖于文档和程序执行。该结构遵循一套简单易学的严格规则。例如，当使用这个文件夹/文件结构时，默认情况下，当你建立一条路线时，事情应该在`-components`文件夹中进行。要获得更多关于可能存在何种规则的灵感，请阅读[辛烷布局(正式的模块统一)](https://github.com/emberjs/rfcs/blob/master/text/0143-module-unification.md)

**7)** *测试和样式应与组件并排放置。*

测试可以与他们正在测试的东西放在一起，而不是放在顶级的`tests/`目录中。这适用于单元、集成和验收测试。当然，也有例外，你可能在测试应用范围内的东西，它没有特定的上下文——对于那些情况，我倾向于把测试放在`tests/acceptance/`(如果它们是验收测试)。

**8)**

tab 问题*不该*的事现代编辑
(neo)Vim:[![tabs vim](img/572acb87b2fa7b403fc2597e48db05f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IK3RmPLk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/owfnzb4fkonys4uoex7t.png)
vs code:[![tabs vscode](img/d5c0e5d7c9a0c91b01138424592eeb74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L1aFhOSp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bi4upaoiypp4vsxuaano.png)
Atom:[![tabs atom](img/e4229e829bb274b918603e91e414ca1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xgZ0zNmI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2cb0ut4lwxfqn4pnc689.png)

这个结构不应该强加会阻止技术进步的限制——比如在一个还没有代码分割的项目中加入代码分割()。

因为文件的位置可以符合一个规则，(即:`src/${collection}/${namespace}/${name}/${type}`)，我们可以通过编程的方式在项目中爬行并试验“惯例”，或者编译 scss 而不导入 javascript，或者在项目的特定子树上调用一些转换。

一个更具体的/真实世界的例子(在用户空间中)，通过将文件按路径分开，我们允许文件系统知道我们的自然路径/代码分割边界——这使得代码分割的实现更加容易。

## 实现

1.  当任何事情都有可能发生时，你如何让每个人都保持一致？
2.  你是如何实现开发者之间的一致性的？
3.  你怎么记得某样东西应该放在哪里？
4.  你如何管理所有这些文件树的导入？

对于 1 到 3，大多数项目的唯一答案是深入的代码评审。在最初的几条既定路线之后，维护会变得更容易。但这不可避免地是一个手动过程，因为大多数生态系统没有办法通过编程来执行约定。

对于管理导入，最好的办法是设置公共入口点的绝对别名。

例如:

```
 "paths": {
      "project-name/*: ["."],
      "@data/*": ["src/data/*"],
      "@models/*": ["src/data/models/*"],
      "@ui/*": ["src/ui/*"],
      "@components/*": ["src/ui/components/*],
      "@env": ["src/env.ts"],
      "tests/*": [ "tests/*" ],
      "*": ["types/*"], 
```

Enter fullscreen mode Exit fullscreen mode

这确实意味着，如果你有深度嵌套的组件，你的导入路径可能会很长，但是它们很容易`grep`形成，而且你将有一个更容易的时间来移动子树，因为没有相对路径需要担心中断。

React 应用程序的一个例子实现了本文中概述的大部分标准:[React 应用程序的例子](https://github.com/sillsdev/appbuilder-portal/tree/33f2ada4c2601d6586ca89b934f83dae0d44e5b0/source/SIL.AppBuilder.Portal.Frontend/src/data)

但是，在 Ember 中，有一个[解析器](https://github.com/ember-cli/ember-resolver)。解析器定义了一组规则，用于查找事物和上下文发现组件、路由、数据模型等。有一组约定允许解析器在 app-space 中查找内容，因此您不需要担心导入它们。有一个引用，解析器查找这个引用，然后把这个东西插入。

ember 的独特之处在于，它有一系列其他生态系统没有的构建时优化。这是由花椰菜驱动的，你可以在构建过程中转换你的应用程序文件树的一部分。Ember 使用它来交换对组件(例如，可能是其他东西)的实际引用的查找。在构建过程中，西兰花还被用来替换简单的助手，比如带有渲染 html 的`{{fa-icon}}`,这样捆绑包就可以更小。

要阅读更多关于余烬的分解器，请随意查看 [DockYard 的文章，“了解余烬的分解器”](https://dockyard.com/blog/2016/09/14/understanding-ember-s-resolver)
要阅读更多关于西兰花，奥利·格里菲斯有一个*惊人的* [指南/教程](http://www.oligriffiths.com/broccolijs/)

这种结构的一个例子可以在这里找到:gitlab 的
[emberclear(这是我的一个副业项目](https://gitlab.com/NullVoxPopuli/emberclear/tree/master/packages/frontend) [emberclear.io](https://emberclear.io) 的代码)。

[Octane 布局的](https://github.com/emberjs/rfcs/blob/master/text/0143-module-unification.md)文件夹结构几乎满足所有用例。这篇文章的大部分代表了 Octane 布局的 RFC 中的一些想法。

注意，辛烷布局尚未发布。它将于 2019 年初到来，同时发布的还有 [Ember Octane](https://github.com/tomdale/rfcs/blob/2018-roadmap/text/0000-roadmap-2018.md)

我会说这是人们应该使用的*和*布局吗？也许吧。在我概述的所有 js 生态系统都可以使用的东西和 Octane 布局为 ember 特定应用规定的东西之间，有一些喘息的空间。最后，如果你处在一个必须决定如何布局的生态系统中，那么当你四处放置文件或者把所有东西都复制到这里时，只要记住这些指导方针就行了——但是要做一些调整。最终，你需要做对你的团队最有利的事情。个人感觉与 React 比较，*接近*。也许有一个工具可以为非成员项目编写，帮助指导结构。类似于过梁，但用于文件位置。