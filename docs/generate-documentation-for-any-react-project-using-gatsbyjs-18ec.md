# 使用 GatsbyJS 为任何 React 项目生成文档

> 原文：<https://dev.to/whoisryosuke/generate-documentation-for-any-react-project-using-gatsbyjs-18ec>

我最近在探索设计系统的不同文档解决方案，通过我的实验，我创建了一个模板来为任何 React 项目创建 Gatsby 文档。

以 [docblocks](http://usejsdoc.org/) 的形式编写与组件内联的文档，并使用 [MDX](https://github.com/mdx-js/mdx) 添加更深入的描述+实例。查看这里的[演示。](https://gatsby-documentation-starter.netlify.com)

[![Screenshot of the demo website](img/aa24b667f4b84fc4d5dd3ad2c70880c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--slXmZmnV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1537566954609/HJQeFkmtX.png)

如果你的代码已经被*记录在案，并且你的组件位于`src/components`——那么**你就可以开始了！**将这个项目克隆到你的代码库中，然后*让它裂开吧！*🚀*

 *没有那个设置？不要担心！我涵盖了以下所有内容👇

## 入门

**用 Netlify 安装**

1.  [![Deploy to Netlify](img/cf684e295a7d6d8caa5d339b5a4a6fa6.png)T2】](https://app.netlify.com/start/deploy?repository=https://github.com/whoisryosuke/gatsby-documentation-starter/tree/example)

**用 Gatsby CLI 安装**

1.  `gatsby new docs https://github.com/whoisryosuke/gatsby-documentation-starter/`

**从 Github 安装**

1.  `git clone https://github.com/whoisryosuke/gatsby-documentation-starter.git`
2.  用你的组件+ MDX *的位置更新`gatsby-config.js`(见:“改变源文件夹”)*
3.  `npm install`项目内部
4.  `npm run develop`
5.  查看您的文档: [http://localhost:8000](http://localhost:8000)

### 创建文档

文档有两个来源:组件源代码和 MDX 文件。

```
src
└── components
    └── Button
        ├── Button.js
        └── Button.mdx 
```

Enter fullscreen mode Exit fullscreen mode

[**React-docgen**](https://github.com/reactjs/react-docgen) 抓取你为 React 类/函数(`Button.js`)编写的任何 JS 文档块，以及道具类型。这些都显示在您的文档页面上，并以表格的形式组织起来。

在你的 **MDX** 文件中，你可以用 JSX 的例子编写额外的文档(比如 React 组件！).您也可以在这里指定页面 slug(一个**页面名称**和**类别**)。您的页面将被生成为`http://yoursite.com/<category>/<pageName>`。

为了显示组件数据，您需要一个组件的 MDX 文件——并且文档块中的页面名和组件名需要匹配。

> 如果不想直接从 components/JS 文件创建 MDX 文件和生成页面——参见 Github docs 部分:“从 react-docgen 创建页面”。我选择 MDX 的首要原因是 frontmatter 的灵活性，它允许你为组件创建不同的“部分”(例如，如果你有元素 vs 排版)。

```
/**
 * ComponentTitle
**/
class ComponentName extends React.Component {} 
```

Enter fullscreen mode Exit fullscreen mode

```
---
name: ComponentTitle
menu: CategoryName
--- 
```

Enter fullscreen mode Exit fullscreen mode

> 为位于[http://localhost:8000/category name/component name](http://localhost:8000/categoryname/componentname)的组件创建一个页面

## 它是如何工作的？

如果你从来没有坐下来真正创建过一个“Hello World ”, Gatsby 可能会变得非常复杂，而在创建博客时，情况会变得更加复杂。

下面是它在高层次上的工作方式:

1.  Gatsby 从您的项目中提取数据(JS 和 MDX 文件)
2.  数据被转换成 GraphQL 数据层
3.  在构建过程中，Gatsby 使用 MDX 文件为每个组件生成页面。这些页面是 React 组件，它们向 GraphQL 查询我们组件的文档+解析的 MDX

> 如果你不熟悉盖茨比的工作方式，[去他们的网站](http://gatsby.org)了解更多信息。它基本上是一个静态站点生成器，在开发过程中使用 GraphQL 从动态数据源(API、本地文件等)生成静态页面。

## 请慢一点

Gatsby 将数据拉入 GraphQL，转换数据(比如解析 Markdown)，然后基于 React 组件构建页面。

让我们来分解一下这些部分。

### ♻️数据部分

Gatsby 使用“源”插件将数据聚合到 GraphQL 中。这个项目是用`gatsby-source-filesystem`设置的，它允许你使用项目的本地文件系统(抓取任何文件，从 TXT 到 JS 到 MDX)。这将创建一个包含所有导入文件的 GraphQL 端点。每个被查询的文件，或 GraphQL“节点”，包含自动生成的 ID 和一个字符串化版本的文档体。

### ✨改造部分

然后 Gatsby 使用**“transformer”**插件为特定数据集创建不同的 GraphQL 端点。如果您在 GraphQL 中查询“source”插件导入的数据，您会注意到它非常简单。transformer 插件就是这么做的，*将数据转换成可用的格式*。例如，`gatsby-transformer-json`遍历每个文件，检查它是否是 JSON，然后将主体字符串解析回一个对象/数组。

这个模板使用了 [@ChristopherBiscardi](https://github.com/ChristopherBiscardi/) 的 [gatsby-mdx](https://github.com/ChristopherBiscardi/gatsby-mdx/) 和盖茨比团队的[Gatsby-transformer-react-doc gen](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-transformer-react-docgen) **gatsby-mdx** 解析任何 mdx 文件，并创建缓存的 HTML+JS 文件导入到页面中。**Gatsby-transformer-react-doc gen**使用 [react-docgen](https://github.com/reactjs/react-docgen) ，这是一个由脸书团队创建的 CLI 工具，用于从 React 组件中提取文档。它对您导入的任何 JS 文件运行 CLI，并为其创建 GraphQL 端点。

### ⚙️《营造零件》

当 Gatsby 运行它的构建过程时，它从我们包含在`src/pages/`目录中的任何 JS 文件创建页面。

在构建过程中，它还会执行我们添加到`gatsby-node.js`中的其他模块。这允许我们做一些事情，比如向 GraphQL 端点添加新节点，或者从 GraphQL 查询创建页面。

对于这个模板，我在 GraphQL 中查询所有 MDX 文件，并根据这些文件创建页面。页面是从“模板”生成的，模板是一个能够运行 GraphQL 查询的 React 组件。由于 Gatsby 是一个框架，它为所有这些操作(查询 GraphQL、从 React 组件创建页面、向 React 组件传递数据等)提供了 API/方法。

## 🎨设计过程

我想保持设计和实际代码的轻量级，以便更容易重新利用。文档的布局是 2 列，带有一个标题，移动设备上的侧边栏列消失了(标题中出现了一个“切换侧边栏”按钮)。时髦的动画移动按钮是由 [@ ainalem](https://codepen.io/ainalem/) 从 Codepen 中取出的。

## 不喜欢盖茨比/JS/React/等怎么办？

如果您正在寻找不同的解决方案，有大量的文档可供选择:

*   [Docz](https://github.com/pedronauck/docz/)
*   [react-styleguidist](https://github.com/styleguidist/react-styleguidist)
*   [Docusaurus](https://docusaurus.io/)
*   [武打〔t1〕](https://vuepress.vuejs.org/)
*   [Gitbook](https://www.gitbook.com/)
*   [docsify](https://github.com/docsifyjs/docsify)

还有更多！不要觉得局限于某个特定的堆栈或设置。找一个和你的流程一致的。

## 记录一切！

我很欣赏那些伟大的工具，它们让你的辛勤工作超越了 *docblocks* 和 *proptypes* ，只需点击一下按钮，就能将你的代码库转变成一个功能齐全、设计良好的文档网站。

我是为了设计系统而设计的，但是它真的可以和任何使用 React 组件的项目一起工作🙌

我一直在寻找工具来提高我的开发人员和设计人员的能力，以改善他们的工作流程。如果这对你的文档有帮助，请在评论中告诉我，或者发推文给我👍

[在 Github 上克隆项目](https://github.com/whoisryosuke/gatsby-documentation-starter) | [查看演示站点](https://gatsby-documentation-starter.netlify.com)

Cheers 🍻
Ryo

* * *

**参考文献:**

*   [盖茨比-文档-首发](https://github.com/whoisryosuke/gatsby-documentation-starter)
*   [演示现场](https://gatsby-documentation-starter.netlify.com)
*   [盖茨比](http://gatsby.org)
*   [盖茨比-mdx](https://github.com/ChristopherBiscardi/gatsby-mdx/)
*   [盖茨比-变压器-反应-文件生成](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-transformer-react-docgen)
*   [盖茨比-变形金刚-备注](https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-transformer-remark)
*   [风格指南指南](http://bradfrost.github.io/style-guide-guide/)
*   [移动按钮 CSS](https://codepen.io/ainalem/pen/LJYRxz)*