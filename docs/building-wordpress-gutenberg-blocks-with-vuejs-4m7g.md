# 用 Vue.js 构建 Wordpress Gutenberg 块

> 原文：<https://dev.to/kball/building-wordpress-gutenberg-blocks-with-vuejs-4m7g>

Wordpress 的新 Gutenberg 编辑器是 web 开发界一个非常热门的新话题。有了它，Wordpress 完全接受了 React 和现代 JavaScript，将数百万开发者带入了现代前端世界，并为现有前端开发者创造了大量受众。

虽然 React 是 Gutenberg 支持的主要框架，但有迹象表明，使用 Vue.js [等其他 JavaScript 框架实现 Gutenberg 块应该可以使用 vuera](https://github.com/WordPress/gutenberg/issues/4344) 等库，因此我决定探索如何实现这一点以及它的工作情况。

**TL/DR:我们可以毫不费力地在 Vue 中实现基本块，但是如果我们试图使用像`BlockControls`或`InnerContent`这样的 Wordpress 内置程序，我们很快就会遇到限制。**

## 设置

首先，我们将使用[create-guten-block](https://github.com/ahmadawais/create-guten-block)scaffold 设置插件。

进入`wp-content/plugins`目录，建立一个新的插件:

```
npm install -g create-guten-block
npx create-guten-block vuetenberg 
```

Enter fullscreen mode Exit fullscreen mode

这创建了一个具有非常基本的初始块的支架，它位于`src/block/block.js`中。一旦你把它放到你的 wordpress 管理器中，你就可以看到它了。

出于这篇博文的目的，我不打算对这个模块的功能做太多改变，只是简单地将其转换为使用 Vue 和 Vue 单文件组件(sfc)

要做到这一点，理解古腾堡地块的核心结构是有帮助的。它由一个包含许多字段的纯 JavaScript 对象组成，包括两个字段——编辑和保存——它们是 React 组件。

```
registerBlockType( 'cgb/block-vuetenberg', {
  // Block name. Block names must be string that contains a namespace prefix. Example: my-plugin/my-custom-block.
  title: __( 'vuetenberg - CGB Block' ), // Block title.
  icon: 'shield', // Block icon from Dashicons → https://developer.wordpress.org/resource/dashicons/.
  category: 'common', // Block category — Group blocks together based on common traits E.g. common, formatting, layout widgets, embed.
  keywords: [
    __( 'vuetenberg — CGB Block' ),
    __( 'CGB Example' ),
    __( 'create-guten-block' ),
  ],

  edit: function( props ) {
    // Creates a <div class='wp-block-cgb-block-vuetenberg'></div>.
    return (
      <div className={ props.className }>
        <p>— Hello from the backend.</p>
      </div>
    );
  },

  save: function( props ) {
    return (
      <div>
        <p>— Hello from the frontend.</p>
      </div>
    );
  },
} ); 
```

Enter fullscreen mode Exit fullscreen mode

为了在我们的核心模块中使用 Vue.js 组件，我们将使用一个名为 [vuera](https://github.com/akxcv/vuera) 的库，它允许我们在 React 组件中调用 Vue 组件。

然后我们将简单地用包装器替换`edit`和`save`，包装器将道具传递给我们的 Vue 组件。

## 准备定制配置

为了将 Vue 添加到我们的组件中，我们需要对我们的构建环境进行一些定制。为了用`create-guten-app`做到这一点，我们需要`eject`构建脚本——否则它们是在插件内部管理的。我们在插件目录中运行下面的代码:

```
npm run eject 
```

Enter fullscreen mode Exit fullscreen mode

这将使用一组位于`scripts`目录中的构建脚本和一些位于`config`目录中的 webpack 配置文件来填充我们的目录。

## 设置 Vue 和 Vuera

我们的下一步是使用 npm 安装 Vuera，并设置我们的构建配置以允许我们使用它。我们还需要安装 Vue，因为我们想使用 Vue SFCs，所以我们需要`vue-loader`。

使用`vue-loader`也需要使用`css-loader`和`vue-template-compiler`，所以我们最终的 NPM 安装看起来像:

```
npm install --save vuera vue vue-loader css-loader vue-template-compiler 
```

Enter fullscreen mode Exit fullscreen mode

要在 React 中使用 Vue，Vuera 建议通过`.babelrc`配置一个 babel 插件，但我无法在 Gutenberg 环境中使用它。相反，我们将使用另一种方法用一个`VueInReact`高阶组件包装 Vue 组件。

首先，为了编译我们的`.vue`文件，我们需要配置 webpack 来添加`vue-loader`。在`create-guten-block`、`config/webpack.config.dev.js`和`config/webpack.config.prod.js`有两种网络包配置。

我们需要做出的改变是:

1.  新增检视载入器外挂程式
2.  将 vue-loader 引用添加到规则中

这意味着我们需要将每个配置文件添加到插件列表:

```
const VueLoaderPlugin = require('vue-loader/lib/plugin');

module.exports = {
  // ...
  plugins: [blocksCSSPlugin, editBlocksCSSPlugin, new VueLoaderPlugin()],
} 
```

Enter fullscreen mode Exit fullscreen mode

并将此块添加到`rules`和

```
module.exports = {
  // ...
  rules: [
    // ...
    {
        test: /.vue$/,
        use: [
          {
            loader: 'vue-loader'
          }
        ]
      }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 古腾堡光秃秃的最小 Vue

我们现在已经准备好了将 Vue 放在古腾堡的最低限度的“概念证明”。为此，我创建了一个非常简单的 Vue 编辑组件，它除了从 Vue 打招呼之外什么也不做:

```
<template>
  <p>{{message}}</p>
</template>
<script>
export default {
  data() {
    return {
      message: 'Hello from Vue',
    };
  },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

然后，为了将它包含在我的块中，我需要导入它，用来自`vuera`的`VueInReact`高阶组件包装它，并将其放入我的模板中。

```
import { VueInReact } from 'vuera'
import EditComponent from './edit.vue';
const Edit = VueInReact(EditComponent);

registerBlockType( 'cgb/block-vuetenberg', {
  // ...
  edit: Edit
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:一旦我们在`VueInReact`中包装了我们的组件，它*将*作为一个反应组件，允许我们在 JSX 内部使用它或者将它返回到任何需要组件的地方。

Props 正如您所期望的那样被传递，所以我们的`Edit` Vue 组件可以引用任何 Gutenberg 特定的属性。

## 使用古腾堡内置组件

好的，很好，所以我们在古腾堡内部的 Vue 组件渲染很好。但是如果我们想使用 Gutenberg 的一些内置组件，比如他们漂亮的块控件呢？

使用一个类似于我们在 React 中嵌入 Vue 的`ReactInVue`包装器，实现起来应该同样简单。

让我们尝试添加一些块控件来定制对齐。首先，我们在块中设置了一个对齐属性:

```
registerBlockType( 'cgb/block-vuetenberg', {
  //...
  attributes: {
    align: {
      type: 'string',
      default: 'full',
    },
  },
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在我们的组件中，我们将利用来自`wp.editor`的`BlockControls`和`BlockAlignmentToolbar`内置组件。

在我们的脚本部分:

```
import { ReactInVue } from 'vuera';
const {
  BlockControls,
  BlockAlignmentToolbar,
} = wp.editor;

export default {
  props: ['attributes', 'setAttributes'],
  components: {
    'block-controls': ReactInVue(BlockControls),
    'block-alignment-toolbar': ReactInVue(BlockAlignmentToolbar),
  },
  data() {
    return {
      message: 'Hello from Vue',
    };
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们的模板:

```
<template>
  <div>
    <block-controls>
      <block-alignment-toolbar :value="attributes.align"
          :onChange="align => setAttributes( { align } )"
          :controls="['wide', 'full']"
       />
    </block-controls>
    <p>{{message}}</p>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单，但是在古腾堡和 Vuera 的当前状态下，我们遇到了一点挑战和缺点。

只有当块被选中时,`BlockControls`组件才可见——但是在我们基于 Vue 的实现中，它永远不会出现！

经过一番挖掘，我发现这是新的 [React 上下文 API](https://reactjs.org/docs/context.html) 带来的挑战。

虽然我们可以使用 Vuera 在 Vue 组件中很好地呈现 React 组件，但 Gutenberg 的许多内置组件利用 React 的上下文 API 来根据元素是否被选中来改变行为，并且上下文似乎不会跨越 React/Vue 边界。

在`BlockControls`的情况下，这意味着元素永远不会出现。

## 变通办法

这对于用 Vue 构建 Gutenberg 块来说是一个严重的限制——内置编辑器组件是使所有块的界面保持一致的重要部分。

对于像控件- `BlockControls`或`InspectorControls`这样的东西，它们是绝对定位的，不需要存在于我们的核心块中。

我们可以通过使用 pure React 将它们**放在 Vue 组件的**之外来解决这个限制，并继续在 Vue 中保留组件的主要部分:

```
import { VueInReact } from 'vuera'
import EditComponent from './edit.vue';
const Edit = VueInReact(EditComponent);
const {
  BlockControls,
  BlockAlignmentToolbar,
} = wp.editor;

registerBlockType( 'cgb/block-vuetenberg', {
  // ...
  edit: function(props) {
    return (
      <div>
        <BlockControls>
          <BlockAlignmentToolbar />
        </BlockControls>
        <Edit {...props} />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，对于像`InnerBlocks`这样的东西，这种解决方法是不够的，因为由于其多样性，它被嵌入在块中。

此时，我必须得出结论，只有不依赖于内置且不嵌套内容的古腾堡块才能使用 Vue.js 构建

## 前进的道路

React 上下文 API 仍然相对较新，Vuera 很可能能够实现一种传递上下文的方法。我已经为这件事开了[一个 github 问题](https://github.com/akxcv/vuera/issues/50)，并且花了相当多的时间试图理解如何实现它，但是到目前为止还没能弄明白。

如果任何阅读这篇文章的人了解 Context API 的内部工作原理，并能帮我指出正确的方向，我将不胜感激！

另一种可能性是，如果通过 Vuera 传递上下文是不可能的，那么 Gutenberg 可以实现一种替代方法，将组件的选定状态传递给子组件。

主 Vue 组件接收到一个`isSelected`属性，表明*正在正确地*更新，它可以将这个属性传递给子组件。然而，那些组件现在还没有被设置来接收这个道具，只查看上下文。

无论我们如何实现，我乐观地认为有一天我们将能够使用 Vue.js 实现复杂的 Gutenberg 块，就像我们在 React 中一样简单。我们只是还没到那一步。

* * *

附:如果你对这类话题感兴趣，你或许应该[在 Twitter 上关注我](https://twitter.com/kbal11)或者加入我的邮件列表。我发出一份名为“[周五前端](https://zendev.com/friday-frontend.html)”的每周简讯。每周五我会发出 15 个链接，链接到 CSS/SCSS、JavaScript 和其他各种精彩的前端新闻中的最佳文章、教程和公告。在这里报名:【https://zendev.com/friday-frontend.html T4】