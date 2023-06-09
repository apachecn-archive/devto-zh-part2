# 使用 Airbnb 的 React to Sketch 应用程序生成草图风格指南

> 原文：<https://dev.to/whoisryosuke/using-airbnbs-react-to-sketch-app-to-generate-sketch-style-guide-2od9>

当我发现 Airbnb 的 react-sketchapp 时，我欣喜若狂地开始抛弃笨重的设计版本控制系统，寻求单一的真相来源: **React** 。

反正一切都以 React 结束。尽管我喜欢 Sketch，但它在 Windows 或 Linux 上不可用，限制了我的贡献。我们想让我们的设计系统尽可能地易于使用，尽管 React 听起来很独特，但它最终是更好的选择。

## 真理的单一来源:反应

### 正常发育过程:

*   素描设计
*   转移设计以作出反应
*   在草图中重新设计
*   翻译更改以作出反应
*   将草图文件提交给 git repo
*   Git 获取新的更改，并丢失另一个艺术家在另一次提交中所做的小更改
*   或者理想的版本控制与[厂](http://plantapp.io/)或 [Kactus](http://kactus.io) 仍然斗争。

### 妙道:

*   草图设计(或 Photoshop，或任何程序)
*   转移设计以作出反应
*   永远不要再碰素描，除非你需要，你知道吗...素描
*   将 React 设计转换为草图
*   更新 React 代码，生成新的草图文件

## 出现了问题

### 按要求做出反应

对于一些人来说，这是一个问题，尤其是那些喜欢保持平台不可知的公司。如果是这种情况，那么你的设计系统一定要以你喜欢的格式为基础。

对于 Kushy 来说，我们所有的应用程序都是在 React 中开发的。他们中的每一个人。可能在预反应中有一两个，在电子中有一些(用反应)。显然非 app 项目，比如静态网站，都是建立在基本的 HTML/CSS 之上的。但在大多数情况下，我们生态系统中 90%的时间都在使用 React。这使得它成为真理之源的显而易见的选择。

### 通过草图更新设计

这个系统使得开发者很容易对设计系统做出贡献，但是这是一条双行道吗？它允许设计师在 React 中更新草图和反映吗？**剧透警告:** *没有，只对草图有反应。*

这意味着您需要使用 Kactus、Plant 等进行设计版本控制。设计系统会有一个从 React 组件构建草图文件的脚本。设计师`git pull`那些草图文件，并使用 Kactus 处理设计师+提交 PR 到 repo 之间的变更。开发人员必须查看草图文件(理想情况下使用 Kactus 来比较可视化提交差异)，然后应用更改来做出反应。

### 这不是 React 转换器

其中一个主要问题是只需要使用 3 种类型的通用组件(视图、文本、样式表)来创建所有的组件。这是一个由*原语*组成的系统，类似于 React Native，其中有扩展客户端操作系统或浏览软件的基础组件。

这些原语允许您轻松创建转换为草图样式和形状的组合，而不必解释 DOM 节点+ CSS。

然而，如果你在 React 中有一个现有的设计系统，比如 Bootstrap 或者 Material UI，**你将不得不在一个新的 API 中手工**重新编码所有的东西。你将*仍然*不得不手动更新你的旧 React 组件，除非你选择用 [react-primitives](https://github.com/lelandrichardson/react-primitives) 切换到通用渲染。

## 解

### 每个人都不使用草图，每个人都可以使用 React

Sketch 是 Mac 独有的应用程序，因此如果它的基础是从 Sketch 开始的，Windows 和 Linux 用户将无法为设计系统做出贡献。

通过使单一的真实来源反应组件，它允许任何人在任何平台上做出贡献。通过使用 Airbnb 库，我们可以在 React 和 Sketch 之间建立一座桥梁，因此我们仍然可以方便地使用 Sketch 访问创意。

### 更易接近的组件

即使有人不喜欢 React，我们仍然可以轻松地从呈现的组件中提取 HTML 和 CSS。这不完全是复制粘贴，但这比将草图文件转换成 HTML/CSS 要容易得多(因为这些服务已经存在，但[往往很昂贵](https://launchpad.animaapp.com/pricing))。

通过强迫你在*原语*中工作，你的组件变成了*在大多数设备(移动- Android + iOS，桌面等)中普遍可用的*。

## 流程

*   列出风格指南的需求清单(字体、颜色等)
*   用草图制作一个最终产品的模板
*   制作应用程序的界面草图
*   制作 app！

## 使用 react-sketchapp

我们将在 React-Sketch.app repo 中使用[样式向导示例](https://github.com/airbnb/react-sketchapp/tree/master/examples/styleguide)。

### 新增项目

如果你开始一个新项目:

1.  `git clone`基地项目
2.  `cd`放入 styleguide 文件夹
3.  `npm install`
4.  `npm run render`
5.  打开草图，创建新文档。
6.  点击顶部菜单中的插件，导航至`react-sketchapp: Styleguide`

### 现有项目

在现有项目中，安装必要的依赖项:

```
npm i react react-sketchapp react-test-renderer chroma-js
npm i @skpm/builder --dev 
```

Enter fullscreen mode Exit fullscreen mode

将以下脚本添加到您的`package.json` :

```
 "scripts":  {  "build":  "skpm-build",  "watch":  "skpm-build --watch",  "render":  "skpm-build --watch --run",  "render:once":  "skpm-build --run",  "postinstall":  "npm run build && skpm-link"  }, 
```

Enter fullscreen mode Exit fullscreen mode

## 结果

[![Airbnb's react-sketchapp styleguide example](img/9bb956114280cd8076495453b0f95733.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iRXPIfkp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/airbnb-react-sketchapp-styleguide-bc5d7d234a9aa73fb0f824469b398374-f3d01.jpg)

这是你从盒子里得到的东西。你得到了一个基本的风格指南，包括排版和颜色定义。

### 热重装

编辑并保存 React 组件触发器草图以移除旧的层组，并用新的层组替换。如果你移动任何东西，你会看到它反弹回画布上的原始位置。这也意味着，即使您在画板中放置了任何东西，它也会被删除并替换为 React 中定义的内容。

### 适用于自定义字体

它立即将 Helvetica 和佐治亚州的文本更改为我从第三方安装的自定义字体。

### SVGs

使用 SVG 相当简单。我抓取了 SVG 代码，将其导入到项目中，并用 React 组件等价物(`<path> becomes `)替换了所有 SVG 的 DOM 元素。这是一个简单的查找和替换过程，我有功能性的(和通用的)SVG 组件。

### 画板、页面、组织

您可以使用`<Artboard>`和`<Page>`组件使用 React 创建画板和页面。就像把所有东西都包在这些里面一样简单。它还支持基于`/`的名称符号，这对于草图导出到有组织的文件夹中是至关重要的。

我无法获得多个页面来处理 Styleguide 示例，文档也没有多大帮助。在我把所有东西都包装在`<Document>`组件中之后，我一直得到一个关于`document.pages`的错误消息(必须重命名另一个被渲染的文档组件)。

#### 为每个生成画板

我正在寻找开发人员在野外使用 react-sketchapp 的例子，在 [antd-sketchapp](https://github.com/ant-design/antd-sketchapp/blob/master/src/demo.js) repo:
中偶然发现了这段代码

```
import React from 'react';
import { render, Artboard } from 'react-sketchapp';

import ButtonDemo from './components/Button/demo';
import CheckboxDemo from './components/Checkbox/demo';
import PaginationDemo from './components/Pagination/demo';
import TableDemo from './components/Table/demo';
import GridDemo from './components/Grid/demo';
import IconDemo from './components/Icon/demo';

const renderDemo = (name, Component) => {
  return () => {
    render(
      <Artboard
        name={`antd-sketchapp ${name}`}
        style={{
          padding: 40,
        }}
      >
        <Component />
      </Artboard>,
      context.document.currentPage()
    );
  };
};

export const Checkbox = renderDemo('Checkbox', CheckboxDemo);
export const Button = renderDemo('Button', ButtonDemo);
export const Pagination = renderDemo('Pagination', PaginationDemo);
export const Table = renderDemo('Table', TableDemo);
export const Grid = renderDemo('Grid', GridDemo);
export const Icon = renderDemo('Icon', IconDemo); 
```

Enter fullscreen mode Exit fullscreen mode

您创建了一个名为`renderDemo`的简单功能组件，它接受组件的名称和实际的组件本身。它返回内部嵌套了动态的画板。然后我们在每个组件上运行`renderDemo`函数。哇啦👉我们有 6 个单独的画板用于我们的各个组件。

## 结论

[![Airbnb's react-sketchapp styleguide example with Kushy edits](img/e0549ed7d445c5318d61f73ac331c2cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_8e27UlC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://whoisryosuke.com/static/kushy-react-sketchapp-style-guide-0546fd74dbdacc0f56d65b4f130b72a0-89322.jpg)

**如果你是从零开始，**这似乎是一个有趣的实验，可以创建一组真正通用的 React 组件，用于 web 和移动设备。

**如果你有一个现有的设计系统**，我建议你不要用它。开发和维护一组镜像的原始组件是不值得的。除非您计划切换到原语，或者已经使用了某种通用组件设置，否则这可能是一条有前途的道路。

发现这并不像我希望的那样健壮，有点令人失望。这个过程被原语的限制所抑制。我需要一个世界，在那里我可以从根本上获取在 HTML/CSS 中定义的 React 组件，并将它们转换为 Sketch。

我很期待尝试 brainly 的 html-sketchapp，它将 HTML + CSS 转换成一个草图文件。我们不使用 React native-like 原语，而是简单地将 React 组件呈现为 HTML，并将它们放入转换器。但是我下次会谈到这一点！

⚡️在 Github 上下载我的源代码👈T3】

希望这有所帮助👍
良

* * *

**参考文献**:

*   [我在 Github 上的示例项目](https://github.com/whoisryosuke/kushy-react-sketchapp)
*   [airbnb 的 react-sketchapp](https://github.com/airbnb/react-sketchapp/)