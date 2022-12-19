# 在 Vue.js 应用程序中使用字体牛逼图标的完整指南

> 原文：<https://dev.to/bnevilleoneill/full-guide-to-using-font-awesome-icons-in-vue-js-apps-ee3>

[![](../Images/1d47749c231869784df14d5de871d1ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UCINWzgQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-Ul_sjmRFqz-WZiuZVFCZw.jpeg)

字体真棒图标收集，嗯，真棒。近 4，000 个难以置信的易用图标，其中约 1300 个是开源的，可以在任何应用程序中免费使用。作为一个初露头角的 Vue.js 程序员，这个库看起来是一个改进我正在开发的应用程序的好方法。

虽然 Font Awesome 团队提供了与 Vue.js 的良好集成，但缺乏具体如何使用该库的说明。这导致我花了几个小时旋转轮子让它工作，并发现了几种使用字体真棒图标的方法。

在本文中，我们将回顾在 Vue.js 应用程序中使用字体超棒图标的方法，对比它们，然后回顾一些 Vue.js 特定的使用图标的方法。

字体牛逼网站:[https://fontawesome.com/](https://fontawesome.com/)

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 入门

在开发 Vue.js 应用程序时，我们最有可能使用 NPM(node . js 事实上的标准包管理器)来组装组件，并使用 Webpack 来组装应用程序。

包含本文讨论的代码的 Github 资源库位于:[https://github.com/robogeek/vuejs-fontawesome-examples](https://github.com/robogeek/vuejs-fontawesome-examples)

作为一名 Vue.js 程序员，你可能已经安装了 Node.js 和 npm。如果没有，请前往[https://nodejs.org/en/](https://nodejs.org/en/)获取可安装包，或者前往[https://nodejs.org/en/download/package-manager/](https://nodejs.org/en/download/package-manager/)使用包管理系统获取安装指导。

接下来，我们安装 Vue.js CLI 工具，因为它可以提供脚手架应用程序供我们使用。网站上有安装说明:[https://cli.vuejs.org/guide/installation.html](https://cli.vuejs.org/guide/installation.html)T2】

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli 
```

一旦安装完成，你可以输入 vue - version 来验证它的存在。

接下来，我们初始化一个使用 Webpack 构建的演示应用程序:

```
$ vue init webpack-simple 001-start
? Project name vuejs-fontawesome-get-started
? Project description Vue.js Fontawesome Getting Started
? Author David Herron <david@davidherron.com>
? License MIT
? Use sass? No

 vue-cli · Generated “001-start”.

 To get started:

 cd 001-start
 npm install
 npm run dev 
```

如果您愿意，可以按照工具打印的说明进行操作。在此之前，让我们把注意力转向 Vue.js 集成的字体 Awesome 指令。[https://font awesome . com/how-to-use/on-web/using-with/vuejs](https://fontawesome.com/how-to-use/on-the-web/using-with/vuejs)

```
npm install --save @fortawesome/fontawesome-svg-core  npm install --save @fortawesome/free-solid-svg-icons  npm install --save @fortawesome/vue-fontawesome 
```

我们被建议从 npm 安装这些软件包，所以让我们这样做，然后运行推荐的步骤。我们将看到空白的 Vue.js 演示应用程序。但是我们想进行字体牛逼文档里推荐的手术。

你可以自己试试这个，或者浏览一个互动演示:
[https://codesandbox.io/embed/rwzyq379jn](https://codesandbox.io/embed/rwzyq379jn)
在生成的源码中，先把 main.js 改成这个:

```
import Vue from 'vue'
import App from './App.vue'

import { library } from '@fortawesome/fontawesome-svg-core'
import { faCoffee } from '@fortawesome/free-solid-svg-icons'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

library.add(faCoffee)

Vue.component('font-awesome-icon', FontAwesomeIcon)

Vue.config.productionTip = false

new Vue({
  el: '#app',
  render: h => h(App)
}) 
```

这将获得*库*对象，并从“free-solid-svg-icons”包中加载一个图标。那个图标后来被添加到*库中*。接下来，我们加载 *FontAwesomeIcon* ，这是 Vue.js 组件，并将其作为一个全局组件注册到 Vue.js。最后，我们设置应用程序进行渲染。

现在我们需要转向 App.vue，我们用以下内容替换它:

```
<template>
  <div id="app">
    <h1>Getting Started with Vue.js and Font Awesome</h1>
    <p>Have a cup of coffee: <font-awesome-icon icon="coffee" /></p>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
</style> 
```

这就是 Vue.js 所说的单个文件模板。它将模板、JavaScript 和 CSS 都包含在一个方便的文件中。这是一个很酷的想法，它的实现隐藏在您将在目录中看到的 Webpack 配置中。我们不需要研究 Webpack 的配置，简单地使用它就足够了。

现在，当我们运行 *npm run dev* 时，网络浏览器将自动加载此页面:

[![](../Images/b0ad62594f34dbcb80463a437d2ab88e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w9IaM7qv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/797/0%2AV3T9EIn32xZNnLLz)

这很酷，我们有这个漂亮的自定义 HTML 标签，可以很容易地用来加载图标。太好了。如果你浏览一下字体 Awesome 文档，你会发现它可以将图标分层，并添加各种效果，如旋转。它保证会很有趣。

### 显示 JavaScript 或 Vue.js 品牌图标

Font Awesome 项目包括许多“品牌”图标，如 JavaScript 和 Vue.js。

当我们创建演示应用程序时，它存储在一个名为 001-start 的目录中。复制该目录，将新目录命名为 002-brands。或者可以在 App.vue 中查看互动演示:
[https://codesandbox.io/embed/rwzyq379jn](https://codesandbox.io/embed/rwzyq379jn)
在新目录下，将模板改为:

```
<template>
  <div id="app">
    <h1>Using Font Awesome "Brand" icons in Vue.js</h1>
    <p>Have a cup of coffee: <font-awesome-icon icon="coffee" /></p>
    <p>Have a module of JavaScript: <font-awesome-icon icon="js" /></p>
    <p>Have a module of Vue.js: <font-awesome-icon icon="vuejs" /></p>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
</style> 
```

第二个是试图加载 JavaScript 图标。字体牛逼网站提供搜索功能。输入“ *javascript* ”，你会发现“js”是 javascript 图标的代码，同样，Vue.js 图标也有代码“vuejs”。

现在运行这个应用程序，npm run dev，你会看到图标不在那里。在浏览器 JavaScript 控制台中，您会看到一条错误消息，说明没有找到图标。换句话说，这并不都是很棒的，因为我们现在必须学习一些东西。

第一步是查看发行目录:

```
$ ls node_modules/@fortawesome/free-solid-svg-icons/ 
```

这显示了一长串文件名为 faCoffee.js 的文件。查看该文件内部，您会发现一堆数据，包括一个名为 svgPathData 的字符串常量，这显然是为了驱动 SVG 的生成。我们不需要担心细节，只需要知道它在这里。

重要的是，这些文件都不包含 JavaScript 或 Vue.js 图标。

字体真棒图标库不是一个库，而是四个库，我们只加载了其中一个库。总设置为:

*   *固体*:[https://www . npmjs . com/package/@ fortawesome/free-Solid-SVG-icons](https://www.npmjs.com/package/@fortawesome/free-solid-svg-icons)
*   *常规*:[https://www . npmjs . com/package/@ fortawesome/free-Regular-SVG-icons](https://www.npmjs.com/package/@fortawesome/free-regular-svg-icons)
*   *灯*:没有对应的 npm 包
*   *Brands*:[https://www . npmjs . com/package/@ fortawesome/free-Brands-SVG-icons](https://www.npmjs.com/package/@fortawesome/free-brands-svg-icons)

接下来，如果我们研究图标浏览器([https://fontawesome.com/icons?d=gallery](https://fontawesome.com/icons?d=gallery))，我们会在侧边栏中看到一些与这四个组相对应的复选框。试着先点击*免费*选项，然后点击四组，你会看到图标浏览器显示了与选项对应的不同子集。

因为 JavaScript 和 Vue.js 图标位于 Brands 部分，可以使用图标浏览器进行验证，所以我们需要加载这个包:

```
$ npm install — save @fortawesome/free-brands-svg-icons 
```

这表明 main.js 应该这样修改:

```
import { library } from ‘@fortawesome/fontawesome-svg-core’; import { faCoffee } from ‘@fortawesome/free-solid-svg-icons’; import { faJs, faVuejs } from ‘@fortawesome/free-brands-svg-icons’; import { FontAwesomeIcon } from ‘@fortawesome/vue-fontawesome’; library.add(faCoffee, faJs, faVuejs); 
```

但这无助于解决浏览器控制台中的错误信息。

在我们描述这个问题的解决方案之前，让我们回顾一下使用字体牛逼图标的其他方法。

### FontAwesome CSS

在前一节中，我建议为代码创建一个名为 002-brands 的目录。复制该目录以创建 003-css 并做一些修改。或者您可以使用在线演示:
[https://codesandbox.io/embed/rwzyq379jn](https://codesandbox.io/embed/rwzyq379jn)
否则，键入此命令:

```
npm remove -S @fortawesome/fontawesome-svg-core     @fortawesome/free-brands-svg-icons     @fortawesome/free-solid-svg-icons     @fortawesome/vue-fontawesome npm install 
```

这将删除我们刚刚使用的 Vue.js 支持。

编辑 main.js 为 so:

```
import Vue from 'vue';
import App from './App.vue';

Vue.config.productionTip = false;

new Vue({
  el: '#app',
  render: h => h(App)
}); 
```

然后在 App.vue 中修改模板部分如下:

```
<template>
  <div id="app">
    <link rel="stylesheet" 
        href="https://use.fontawesome.com/releases/v5.2.0/css/all.css" 
        integrity="sha384-hWVjflwFxL6sNzntih27bfxkr27PmbbK/iSvJ+a4+0owXq79v+lsFkW54bOGbiDQ" 
        crossorigin="anonymous">
    <h1>Using Font Awesome "Brand" icons in Vue.js</h1>
    <p>Have a cup of coffee: <i class="fas fa-coffee"></i> </p>
    <p>Have a module of JavaScript: <i class="fab fa-js"></i> </p>
    <p>Have a module of Vue.js: <i class="fab fa-vuejs"></i> </p>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
</style> 
```

这来自两页:

1.  [https://font awesome . com/how-to-use/on-the-web/referencing-icons/basic-use](https://fontawesome.com/how-to-use/on-the-web/referencing-icons/basic-use)—涵盖了`<i>`元素的使用，如下所示。
2.  [https://font awesome . com/how-to-use/on-the-web/setup/getting-started](https://fontawesome.com/how-to-use/on-the-web/setup/getting-started)—给我们这里显示的`<link>`元素

`<link>`当然属于 HTML 的`<head>`部分。这个特殊的 CSS 对前面展示的四个字体库都使用图标字体。

注意，对于 *coffee* 图标，使用“fas”类，而对于 js 和 vuejs 图标，使用 fab 类。这是这两个品牌在*品牌*库中，而不是*实体*库中的副作用。

当以这种方式使用时，显示字体牛逼图标使用`<i>`标签，如下所示。Font Awesome 网站文档重点介绍了这个标签的使用。因此，作为一名 Vue.js 程序员，你必须熟练地在他们的文档之间切换，并使用提供的 Vue.js 组件(`<font-awesome-icon>`)。

对于这个例子，我们完全放弃了 Vue.js 支持。这意味着我们有一个包含所有图标的大型 CSS 文件。代价是只需一个`<link>`元素就可以获得整个字体库，这非常方便。缺点是加载每个图标会占用内存。我们的应用程序不会使用整个库，很可能只使用一小部分，那么为什么要给浏览器整个库呢？

相比之下，前一个示例中使用的打包脚本确保只打包所需的代码，仅此而已。使用前面的方法对浏览器的影响很小，代价是多写一点代码。

[![](../Images/8d847927e8f6757454f656b51db16484.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0mEHI_b4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/771/0%2AWv3vGwcwXXx7S173)

但是，嘿，这很有效。有时工作代码胜过架构的纯粹性。

### font awesome-free 包

fond Awesome-free 软件包在他们的 CDN 上包含相同的文件 Font Awesome hosts，参见[https://www.npmjs.com/package/@fortawesome/fontawesome-free](https://www.npmjs.com/package/@fortawesome/fontawesome-free)

首先，复制 003-css 工作目录来创建一个新的目录，004-fontawesome-free。如果您希望尝试在线演示，请单击以下按钮，但会警告您它无法正常工作。您需要在笔记本电脑上执行此步骤。
[https://codesandbox.io/embed/rwzyq379jn](https://codesandbox.io/embed/rwzyq379jn)
在那个目录下安装包如下:

```
$ npm install @fortawesome/fontawesome-free — save 
```

检查已安装的软件包:

```
$ ls node_modules/@fortawesome/fontawesome-free 
```

你会发现几个目录

*   `/js` —与字体 Awesome 5 SVG with JS 关联的所有 JavaScript 文件
*   `/css` —所有使用经典 Web 字体的 CSS 和 CSS 实现
*   `/sprites` —打包在一个方便的 sprite 中的 SVG 图标
*   `/scss`、`/less` —使用 CSS 的 Web 字体的 CSS 预处理文件
*   `/webfonts` —带有 CSS 的 Web 字体的附带文件
*   `/svg`—SVG 格式的单个图标文件

然后在`App.vue`中将`<link>`元素改为:

```
<template>
  <div id="app">
    <link rel="stylesheet" 
        href="node_modules/@fortawesome/fontawesome-free/css/all.css">
    <h1>Using Font Awesome "Brand" icons in Vue.js</h1>
    <p>Have a cup of coffee: <i class="fas fa-coffee"></i> </p>
    <p>Have a module of JavaScript: <i class="fab fa-js"></i> </p>
    <p>Have a module of Vue.js: <i class="fab fa-vuejs"></i> </p>
    <h1>Using Font Awesome by referencing SVG files</h1>
    <p>Have a cup of coffee: 
          
            <use xlink:href="node_modules/@fortawesome/fontawesome-free/sprites/solid.svg#coffee"></use>
          
    </p>
    <p>Have a module of JavaScript:
          
            <use xlink:href="node_modules/@fortawesome/fontawesome-free/sprites/brands.svg#js"></use>
          </p>
    <p>Have a module of Vue.js: 
          
            <use xlink:href="node_modules/@fortawesome/fontawesome-free/sprites/brands.svg#vuejs"></use>
          </p>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
</style> 
```

没有更多的变化，图标都像以前一样出现。这是意料之中的，因为我们所做的只是从本地资产加载相同的文件。

另一个选择是使用包中的 SVG 精灵。在这个模板中添加了另一个部分来引用原始 SVG 文件。

[![](../Images/28c05551c6fbacaf6233b7b37a8e8c81.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J27Fqfyq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/772/0%2ATAC8Ya6TodKaYSKe)

这是可行的，并且显然需要更多的工作来获得正确的大小。

### 回到 Vue.js 和字体牛逼

前几节我们没有回答一个问题:如何在 Vue.js 应用程序中使用 JavaScript 或 Vue.js 或字体 Awesome brands 库中的其他图标？

如果我们将 002-brands 目录复制到 005-brands-2，我们可以从上次停止的地方开始。还有一个在线演示，位于:
[https://codesandbox.io/embed/rwzyq379jn](https://codesandbox.io/embed/rwzyq379jn)
【通过字体牛逼的其他方面的迂回告诉我们一些信息细节，我们现在可以对这个问题施加影响。

我们了解到 JavaScript 和 Vue.js 图标在*品牌*库中。当使用*元素方法时([https://font awesome . com/how-to-use/with-the-API/setup/importing-icons](https://fontawesome.com/how-to-use/with-the-api/setup/importing-icons))，我们应该使用这些模式:*

*   实心图标带有前缀`fas`，并使用`<i class=”fas fa-flag”>`
*   常规图标有前缀`far`并使用`<i class=”far fa-flag”>`
*   灯光图标带有前缀“`fal`”，并使用`<i class=”fal fa-flag”>`
*   品牌图标有前缀“`fab`”，并使用`<i class=”fab fa-font-awesome”>`

长期存在的问题是品牌图标没有显示出来，我们在这里看到品牌图标应该使用前缀“`fab`”。

npm 包([https://www.npmjs.com/package/@fortawesome/vue-fontawesome](https://www.npmjs.com/package/@fortawesome/vue-fontawesome))有额外的有用文档，这些文档非常有用，人们不禁要问为什么这些文档不在 Font Awesome 网站上。在适当的时候，我们会探索很多东西。当务之急是学习如何指定前缀。

也就是说，使用数组语法指定前缀:

```
<font-awesome-icon :icon=”[‘fas’, ‘spinner’]” /> 
```

前缀是数组中的第一个元素，图标名是第二个元素。在这种情况下，FontAwesomeIcon 组件会自动将“fa-”添加到图标名称的前面，如果没有指定库前缀，它将使用“fas”库(solid)。这解释了为什么 JavaScript 和 Vue.js 图标没有加载，因为我们没有明确使用“fab”前缀。

我们可以通过在 App.vue 中修改以下代码来解决这个问题:

```
<p>Have a module of JavaScript: 
    <font-awesome-icon :icon=”[ ‘fab’, ‘js’ ]” /></p>
<p>Have a module of Vue.js: 
    <font-awesome-icon :icon=”[ ‘fab’, ‘vuejs’ ]” /></p> 
```

图标会立即弹出:

[![](../Images/a702bdbc9c979ad05e8e3fbe1c853aec.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vkw8ST8x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/784/0%2Aep1uQcbyrfv1IST5)

除了解决这个特定的问题，我们还有一些特殊的效果要探索。这些特效不仅可以用组件实现，也可以用`<i>`方法实现。

在我们离开之前..呃..探索特效时，我们必须停下来对比一下这两种方法。

Vue.js 应用程序的构建过程确保只有所需的代码被打包并发送到浏览器。定义组件并单独导入每个所需图标的方法需要更多的代码，但好处是当应用程序到达浏览器时，它不会收到完整的字体图标库。相反，它只接收你的应用程序声明的代码和图标。

字体 Awesome 的 Vue.js 集成确实允许这个快捷键:

```
import { fab } from ‘@fortawesome/free-brands-svg-icons’; 
library.add(fab); 
```

它可以一次性导入品牌库中的每个图标，而无需单独导入每个图标。虽然这很方便，但我们被警告"*要小心使用这种方法，因为它在开始时可能很方便，但您的包大小会很大。*"请参考关于最小化应用程序大小的讨论。

我们需要警惕的另一个问题是这种方法可能行不通:

```
import { faJs, faVuejs } from ‘@fortawesome/free-brands-svg-icons’; 
```

这是一个 ES6 特性，它确保只导入给定模块的请求部分。目前，像 Babel 这样的工具被用来将它转换成等价的 ES5 代码，但并不是所有的工具都支持这个特性。因为每个图标都作为一个单独的文件存储在包中，所以另一种方法是使用这个:

```
import faJs from ‘@fortawesome/free-brands-svg-icons/faJs’;
import faVuejs from ‘@fortawesome/free-brands-svg-icons/faVuejs’; 
```

所发生的是这引用了包中的单个文件，而不是从一个集合包中提取对象。

### 字体特效牛逼

看着令人敬畏的字体文档，我们看到一些诱人的特效正在等待播放..呃..探索过。

制作名为 006-effects 的 005-brands-2 目录的副本。此步骤的在线演示位于:

[CodeSandbox](https://codesandbox.io/embed/2wk112xk50)

我们有一个相当大的手术来实现一长串由字体真棒库支持的特效。

在 main.js 中，将导入更改为:

```
import Vue from 'vue';
import App from './App.vue';

import { library } from '@fortawesome/fontawesome-svg-core';
import { 
    faCoffee, faSpinner, faWrench, faAmbulance, faEdit, faCircle, faCheck, faChessQueen,
    faPlus, faEquals, faArrowRight, faPencilAlt, faComment, faHeadphones, faSquare,
    faCalendar, faCertificate, faEnvelope, faTimes, faBookmark, faHeart, faPlay,
    faSun, faMoon, faStar
} from '@fortawesome/free-solid-svg-icons';
import { faJs, faVuejs, faFacebookF } from '@fortawesome/free-brands-svg-icons';
import { FontAwesomeIcon, FontAwesomeLayers, FontAwesomeLayersText } from '@fortawesome/vue-fontawesome'

library.add(
    faCoffee, faSpinner, faWrench, faAmbulance, faSquare,
    faEdit, faCircle, faCheck, faChessQueen, faHeadphones,
    faPlus, faEquals, faArrowRight, faPencilAlt, faComment,
    faCalendar, faCertificate, faEnvelope, faTimes, faBookmark,
    faHeart, faPlay, faSun, faMoon, faStar,
    faJs, faVuejs, faFacebookF);

Vue.component('font-awesome-icon', FontAwesomeIcon);
Vue.component('font-awesome-layers', FontAwesomeLayers);
Vue.component('font-awesome-layers-text', FontAwesomeLayersText);

Vue.config.productionTip = false;

new Vue({
  el: '#app',
  render: h => h(App)
}); 
```

这带来了我们将在演示中使用的图标，以及两个新组件。新组件实现了一种分层组合的形式。

下一步是在 App.vue 中将`<template>`部分改为:

```
<template>
  <div id="app">

<h1>Icon sizing</h1>
<font-awesome-icon icon="wrench"  size="xs" />
<font-awesome-icon icon="coffee"  size="lg" />
<font-awesome-icon icon="spinner" size="4x" />
<font-awesome-icon :icon="[ 'fab', 'vuejs' ]" size="2x" />

<h1>Rotation</h1>

<font-awesome-icon icon="spinner" rotation="90"  />
<font-awesome-icon icon="spinner" rotation="180" size="lg" />
<font-awesome-icon icon="spinner" rotation="270" size="2x"  />
<font-awesome-icon :icon="[ 'fab', 'vuejs' ]" rotation="270" size="2x"  />

<h1>Flips</h1>

<font-awesome-icon icon="ambulance" flip="horizontal" size="lg"/>
<font-awesome-icon icon="ambulance" flip="vertical" size="lg"/>
<font-awesome-icon icon="ambulance" flip="both" size="lg"/>
<font-awesome-icon :icon="[ 'fab', 'vuejs' ]" flip="vertical" size="lg"/>

<h1>Animation</h1>

<font-awesome-icon icon="spinner" spin />
<font-awesome-icon icon="spinner" pulse />
<font-awesome-icon :icon="[ 'fab', 'vuejs' ]" size="lg" spin />
<font-awesome-icon :icon="[ 'fab', 'vuejs' ]" size="lg" pulse />

<h1>Border</h1>

<font-awesome-icon icon="spinner" border size="lg"/>
<font-awesome-icon icon="ambulance" flip="vertical" border size="lg"/>

<h1>Pull left/right</h1>

<font-awesome-icon icon="spinner" pull="left" />
<font-awesome-icon icon="spinner" pull="right" />
<font-awesome-icon icon="ambulance" flip="vertical" pull="right" border size="lg"/>

<h1 style="clear: both">Power transforms</h1>

<font-awesome-icon icon="spinner" transform="shrink-6 left-4" />
<font-awesome-icon icon="ambulance" size="lg" :transform="{ rotate: 42 }" />

<h1>Masking</h1>

<font-awesome-icon icon="pencil-alt" 
    transform="shrink-10 up-.5" 
    style="background:MistyRose" 
    size="4x" />

<font-awesome-icon icon="pencil-alt" 
    transform="shrink-10 up-.5" 
    mask="comment" 
    style="background:MistyRose" 
    size="4x" />

<font-awesome-icon :icon="[ 'fab', 'facebook-f' ]" 
    transform="shrink-3.5 down-1.6 right-1.25" 
    style="background:MistyRose" 
    size="4x" />

<font-awesome-icon :icon="[ 'fab', 'facebook-f' ]" 
    transform="shrink-3.5 down-1.6 right-1.25" 
    mask="circle" 
    style="background:MistyRose" 
    size="4x" />

<font-awesome-icon icon="headphones" 
    transform="shrink-6" 
    style="background:MistyRose" 
    size="4x" />

<font-awesome-icon icon="headphones" 
    transform="shrink-6" 
    mask="square" 
    style="background:MistyRose" 
    size="4x" />

<h1>Layers</h1>

<font-awesome-layers class="fa-4x">
  <font-awesome-icon icon="circle" />
  <font-awesome-icon icon="check" transform="shrink-6" style="color: white;" />
</font-awesome-layers>

<font-awesome-layers class="fa-4x" style="background:MistyRose">
  <font-awesome-icon icon="circle" style="color:Tomato" />
  <font-awesome-icon icon="times" class="fa-inverse" transform="shrink-6" />
</font-awesome-layers>

<font-awesome-layers class="fa-4x" style="background:MistyRose">
  <font-awesome-icon icon="bookmark" />
  <font-awesome-icon icon="heart" class="fa-inverse" 
        transform="shrink-10 up-2" 
        style="color:Tomato"/>
</font-awesome-layers>

<font-awesome-layers class="fa-4x" style="background:MistyRose">
  <font-awesome-icon icon="play" transform="rotate--90 grow-2" />
  <font-awesome-icon icon="sun" class="fa-inverse" transform="shrink-10 up-2"/>
  <font-awesome-icon icon="moon" class="fa-inverse" transform="shrink-11 down-4.2 left-4"/>
  <font-awesome-icon icon="star" class="fa-inverse" transform="shrink-11 down-4.2 right-4"/>
</font-awesome-layers>

<h1>Layers text</h1>

<font-awesome-layers full-width class="fa-4x" style="background:MistyRose">
  <font-awesome-icon icon="calendar"/>
  <font-awesome-layers-text 
        class="fa-inverse" 
        transform="shrink-8 down-3" 
        value="27" 
        style="font-weight:900"/>
</font-awesome-layers>

<font-awesome-layers full-width class="fa-4x" style="background:MistyRose">
  <font-awesome-icon icon="certificate"/>
  <font-awesome-layers-text 
        class="fa-inverse" 
        transform="shrink-11.5 rotate--30" 
        value="NEW" 
        style="font-weight:900"/>
</font-awesome-layers>

<font-awesome-layers full-width class="fa-4x" style="background:MistyRose">
  <font-awesome-icon icon="envelope"/>
  <span class="fa-layers-counter" style="background:Tomato">1,419</span>
</font-awesome-layers>

  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
h1 {
  border-bottom: solid 1px black;
}
</style> 
```

这里有很多。这些示例根据所使用的特效类型分为几个部分。

使用元素的属性来指定特定的特效。可以用 size= " .. "来改变图标的大小属性。如你所料，旋转= " .. "属性旋转图标，flip= " .. "翻转图标，等等。这些属性对于它们的功能来说是非常明显的。

一个不太明显的属性是 pull= " .. "其效果类似于*浮动*样式，图标浮动在左侧或右侧。

该组件封装了一组其他图标，将它们层叠在一起。它用于从现有图标合成新图标，以及可用的特效转换。

[![](../Images/409af78536a8fd2e124345c3df88de24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZjH51LQW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/484/0%2Ax6qg7X3BTmZhxVpE)

这是它看起来的样子。

、和组件与字体 Awesome 库提供的功能相关。

从这里的文档开始:[https://font awesome . com/how-to-use/on-the-web/styling/sizing-icons](https://fontawesome.com/how-to-use/on-the-web/styling/sizing-icons)

### 图标按钮和条件渲染

最后一个例子，让我们考虑图标的一个常见用例:工具栏按钮。同时，让我们来看看 Vue.js 条件对选择要呈现的字体很棒的图标的影响。

将目录 002-品牌复制为 007-按钮-条件。或者尝试在线演示，网址为:

[CodeSandbox](https://codesandbox.io/embed/7w8jpjlvlx)

在 main.js 中将导入改为:

```
import Vue from 'vue';
import App from './App.vue';

import { library } from '@fortawesome/fontawesome-svg-core';
import { 
  faCoffee, faCocktail, faGlassMartini, faBeer
} from '@fortawesome/free-solid-svg-icons';
import { 
  faJs, faVuejs, faJava, faPhp, faPython, faCss3, faHtml5 
} from '@fortawesome/free-brands-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';

library.add(faCoffee, faCocktail, faGlassMartini, faBeer,
      faJs, faVuejs, faJava, faPhp, faPython, faCss3, faHtml5);

Vue.component('font-awesome-icon', FontAwesomeIcon);

Vue.config.productionTip = false;

new Vue({
  el: '#app',
  render: h => h(App)
}); 
```

同样，我们正在导入一些新的字体图标。

要使用它们，在 App.vue 中将`<template>`更改为如下:

```
<template>
  <div id="app">
    <h1>Icon Buttons and Conditional Icons</h1>
    <p>Drink: {{ drink }}
        <font-awesome-icon icon="coffee" 
                           size="4x" v-if="drink == 'Coffee'" />
        <font-awesome-icon icon="cocktail" 
                           size="4x" v-if="drink == 'Cocktail'" />
        <font-awesome-icon icon="glass-martini" 
                           size="4x" v-if="drink == 'Martini'" />
        <font-awesome-icon icon="beer" 
                           size="4x" v-if="drink == 'Beer'" />
    </p>
    <p>Language: {{ language }}
        <font-awesome-icon :icon="[ 'fab', 'js' ]" 
                           size="4x" v-if="language == 'JavaScript'"  />
        <font-awesome-icon :icon="[ 'fab', 'vuejs' ]" 
                           size="4x" v-if="language == 'Vue.js'" />
        <font-awesome-icon :icon="[ 'fab', 'java' ]" 
                           size="4x" v-if="language == 'Java'" />
        <font-awesome-icon :icon="[ 'fab', 'php' ]"
                           size="4x" v-if="language == 'PHP'" />
        <font-awesome-icon :icon="[ 'fab', 'python' ]"
                           size="4x" v-if="language == 'Python'" />
        <font-awesome-icon :icon="[ 'fab', 'css3' ]"
                           size="4x" v-if="language == 'CSS 3'" />
        <font-awesome-icon :icon="[ 'fab', 'html5' ]"
                           size="4x" v-if="language == 'HTML 5'" />
    </p>
    <p>
      <button @click="drink = 'Coffee'"> 
        <font-awesome-icon icon="coffee" size="4x" />
      </button>
      <button @click="drink = 'Cocktail'"> 
        <font-awesome-icon icon="cocktail" size="4x" />
      </button>
      <button @click="drink = 'Martini'"> 
        <font-awesome-icon icon="glass-martini" size="4x" />
      </button>
      <button @click="drink = 'Beer'"> 
        <font-awesome-icon icon="beer" size="4x" />
      </button>
    </p>
    <p>
      <button @click="language='JavaScript'">
        <font-awesome-icon :icon="[ 'fab', 'js' ]" size="4x" />
      </button>
      <button @click="language='Vue.js'">
        <font-awesome-icon :icon="[ 'fab', 'vuejs' ]" size="4x" />
      </button>
      <button @click="language='Java'">
        <font-awesome-icon :icon="[ 'fab', 'java' ]" size="4x" />
      </button>
      <button @click="language='PHP'">
        <font-awesome-icon :icon="[ 'fab', 'php' ]" size="4x" />
      </button>
      <button @click="language='Python'">
        <font-awesome-icon :icon="[ 'fab', 'python' ]" size="4x" />
      </button>
      <button @click="language='CSS 3'">
        <font-awesome-icon :icon="[ 'fab', 'css3' ]" size="4x" />
      </button>
      <button @click="language='HTML 5'">
        <font-awesome-icon :icon="[ 'fab', 'html5' ]" size="4x" />
      </button>
    </p>

  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      drink: "", language: ""
    }
  }
}
</script>

<style>
</style> 
```

就活动的 Vue.js 组件而言，这相当简单。我们有一些按钮，显示各种字体真棒图标，当点击这些按钮分配一个相应的值给一个变量。

在 UI 的另一部分，我们显示使用的文本代码，以及相应的图标。使用 Vue.js 条件来选择图标。

运行应用程序后，我们可以点击按钮，相应的选择就会显示出来。用户界面可能如下所示:

[![](../Images/19c7d7808cc760de18b0feabbd450723.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SgUciM-x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/617/0%2A85ke2m6xX6UXcgNw)

### 结论

图标当然为任何图形应用程序增加了很多。与文字相比，它们将意义传达给不同层次的人类体验，因此可以成为一种更容易学习的应用。此外，作为符号，很少需要翻译不同语言的用户界面(本地化),因为精心选择的符号是通用的。

通过本教程，我们已经看到了将字体 Awesome 集合中的图标添加到 Vue.js 应用程序中是多么容易，并且我们已经尝试了使用底层字体 Awesome 库。

字体 Awesome 的 Vue.js 集成就是建立在这个基础库之上的。它的功能以 Vue.js 组件的形式呈现，公开了大部分功能。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

关于在 Vue.js 应用程序中使用字体牛逼图标的帖子[首先出现在](https://blog.logrocket.com/full-guide-to-using-font-awesome-icons-in-vue-js-apps-5574c74d9b2d/)[博客](https://blog.logrocket.com)上。