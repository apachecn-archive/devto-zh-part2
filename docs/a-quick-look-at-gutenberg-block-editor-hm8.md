# 快速浏览古腾堡图块编辑器

> 原文：<https://dev.to/usefulsomebody/a-quick-look-at-gutenberg-block-editor-hm8>

WordPress 正在为其下一个主要发布版本 5.0 建立全新的编辑体验。世界各地的人们对 WP 的这一巨大变化反应不一。但是随着 WordPress 版本 5 的发布越来越近，更新你的开发技能是必要的。在本教程中，我将分享我对古腾堡开发的一点点知识。在这里，我将向您展示如何在 Gutenberg 中创建静态的&富文本块。

在继续之前，我想澄清一件事，开发 Gutenberg 自定义块不需要太多 PHP 知识。需要 JavaScript 知识，尤其是 ES6 标准，需要 React JS 和节点环境。如果你没有这些技术的基础知识，我建议你先了解这些知识。

让教程开始🥁

### **设置古腾堡区块开发环境**

* * *

设置 Gutenberg 块开发环境
要开始 Gutenberg 自定义块开发，您需要在计算机上安装 node js。你可以从[这里](https://nodejs.org/en/download/)下载 nodejs，npm 也会随之安装在你的机器上。然后进入创建古腾堡块 github [库](https://github.com/ahmadawais/create-guten-block)来获得安装古腾堡定制块插件的基本设置说明。

使用您的终端或命令提示符，转到您的本地 WP 安装，并在`wp-content/plugins/`目录下运行下面的命令。

`npx create-guten-block my-block`

运行此代码后，create-guten-block 包将开始在目录中下载。当它完成下载过程时，你会在我的块文件夹中得到这种文件结构。

```
INSIDE: /local_dev_site/wp-content/plugins/my-block

├── .gitignore
├── plugin.php
├── package.json
├── README.md
|
├── dist
|  ├── blocks.build.js
|  ├── blocks.editor.build.css
|  └── blocks.style.build.css
|
└── src
   ├── block
   |  ├── block.js
   |  ├── editor.scss
   |  └── style.scss
   |
   ├── blocks.js
   ├── common.scss
   └── init.php 
```

Enter fullscreen mode Exit fullscreen mode

现在运行下面提到的这两个命令。
`cd my-block`
`npm start`
现在，我们可以开始动手做一些 Gutenberg 开发编码了。

在此文件结构中，我们必须在此目录内的`src/block/`文件夹中为新块开发工作，我们可以创建自己的块目录并在其中保存文件。
从一个简单的例子开始。

在`src/block/`中开始开发一个基本的静态 Gutenberg 块
为我们的块创建另一个名为`/basic-block/`的目录。然后在其中创建两个名为`index.js`和`editor.css`的文件，并通过在
`import './block/basic-block';`
下面添加代码将这个块添加到`src/blocks.js`中。现在，我们可以将代码添加到我们自己的块`basic-block/`文件夹中的新`index.js`文件中，它将在我们的 WP 安装中正确呈现。

###### 这是古腾堡的一个静态块的完整代码。添加这段代码后，我们可以在添加块区域看到我们的块。

```
import './editor.css';

const { registerBlockType } = wp.blocks;
const blockStyle = {backgroundColor: '#900', color: '#fff', padding: '20px'};

registerBlockType('myblock/basic-block', {
    title: 'Basic Block',
    icon: 'images-alt',
    category: 'common',
    edit: function( {className} ) {
        return (
            <div className={className}>
                <p className='my-p'>Hello Editor World!</p>
            </div>
        );
    },
    save: function() {
        return (
            <p style={blockStyle}>Hello Outer World!</p>
        );
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

[![output](../Images/a55f563c105e69b68ae13f2ca2b42e71.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CClCiWzR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.pradipdebnath.com/wp-content/uploads/2018/11/gutenberg-custom-static-block-development.png)

让我给你解释一下这段代码。
`registerBlockType('myblock/basic-block', {...});`
我们需要使用上面的代码来注册古腾堡的任何块，这里我们必须先提供我们的块名。一个完整的块名可以分为两部分，第一部分 myblock 是你的插件名，后面是/提供你的块名，比如这里我提供了 basic-block。要按预期工作 registerBlockType，我们需要首先通过代码从 wp.blocks 获取它。
T1】

现在，在 registerBlockType 函数中，我们需要为我们的块提供一些基本的设置数据。

*   标题:它将在添加块部分提供我们的块的标题/名称。
*   icon:它将定义我们希望在 add block 部分中用于我们的块的图标。在这里，我使用了一个大栅栏图标。
*   category:它定义了在 add block 部分的什么地方可以使用您的块。目前，我们可以在 5 个类别中放置积木。
    *   普通的
    *   格式化
    *   布局
    *   小工具
    *   使...嵌入
*   编辑:在这里，我们可以提供如何在编辑器中呈现我们的块。
*   保存:在这里我们可以定义它应该如何在我们的主题中呈现块。

在这个例子中，我为后端和前端都提供了静态数据。在编辑部分，在函数中，我返回了一些带有 hello world 消息的 HTML 数据，但这不是 HTML，它叫 JSX。这是一种在 JavaScript 中包含 HTML(看起来像，但不完全是 HTML)的简单方法，由脸书的 React js 开发团队创建。
这里我给了包装器 div 元素一个`className`类，我们可以在编辑和保存时在我们的道具中得到它。这个`className`由我们以`wp-block-`开头的完整块名的值组成。所以它将在我们的 HTML 中输出为`wp-block-myblock-basic-block`。这将有助于在`editor.css`文件中定义我们的设计 CSS。

现在在`editor.css`文件中添加如下 CSS 代码。

```
.wp-block-myblock-basic-block {
    color: green;
    background-color: #cfc;
    border: 2px solid #9c9;
    padding: 20px;
}
.wp-block-myblock-basic-block .my-p {
    font-size: 12px;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个 CSS 文件将只在我们的编辑器中呈现块的设计。对于前端的设计，我们可以在一个单独的文件中定义，或者在这种情况下，我在 index.js 文件中使用了一个 js 常量，我在保存部分代码时使用了该常量。
`const blockStyle = {backgroundColor: '#900', color: '#fff', padding: '20px'};`

这个 *blockstyle* 常量有一些样式代码，也不是 CSS 代码，你可以在这里看到`backgroundColor`。这个 *blockstyle* 用于返回保存我们的一部分作为 p 元素样式。我已经为后端和前端提供了不同的样式，以向您展示在这两个区域中呈现的不同样式。
[![output](../Images/8eaf96c2bc3f2695b4050ccb4c795119.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h7vQqfHT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.pradipdebnath.com/wp-content/uploads/2018/11/static-block-backend-area.png)
[![output](../Images/175d0a363b809e1e5e730dfa75c07e91.png)T12】](https://res.cloudinary.com/practicaldev/image/fetch/s--VF6VxKtE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.pradipdebnath.com/wp-content/uploads/2018/11/static-block-frontend-area.png)

这就是了。在这里，您学习了如何在 Gutenberg 中创建第一个简单的静态块。稍后我将向您展示如何在 Gutenberg 中创建一个处理动态数据的富文本块。