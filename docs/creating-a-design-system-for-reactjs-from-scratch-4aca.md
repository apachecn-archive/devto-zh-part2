# 从头开始创建反应堆设计系统

> 原文：<https://dev.to/whoisryosuke/creating-a-design-system-for-reactjs-from-scratch-4aca>

设计系统就在我们面前。放眼望去，各公司都在发布新的设计系统，它们有着奇特的名字，比如北极星、T2、闪电、T4、碳和 T5。而且你已经有了像 [Material UI](https://github.com/mui-org/material-ui) 这样的开源系统，因为它们的高质量和可访问性而在[的每个其他项目](https://www.npmjs.com/package/@material-ui/core)中得到使用。

但是，如果你在这里，我不需要说服你有一个设计系统的优势。您已经知道了在所有项目中为您的 web 组件提供单一真实来源的好处，以及开发人员拥有一个专用的生态系统来控制和创建一致的风格指南是多么强大。

你可能会问自己和我一样的问题:设计系统到底需要什么？我写下了我在大多数系统中遇到的绝对必要的特征，然后继续前行。

## 对设计系统的要求

*   成分
*   查看组件的开发环境
*   文档(带道具类型和示例)
*   单元测试*(以及理想的视觉回归)*
*   自动化代码林挺和格式化

当你把它提炼出来的时候，它是非常简单的。

我们需要组件来共享。一个地方建造它们，另一个地方记录它们。林挺和测试，以确保代码没有错误和工作。

> 这是设计系统可以有多简单的最好的部分。如果我们的最终用户只是将我们的组件导入他们的应用程序(NPM 或其他)，我们不需要构建或传输我们的代码。最终用户会以他们自己的方式来处理。您需要构建代码的唯一原因是，如果您想要分发组件文件(这样用户就可以从 CDN 将它们作为`<script>`标签导入)。

## 堆栈

出于本教程的考虑，我将使用下面的堆栈:

*   **组件系统:** [ReactJS](https://reactjs.org)
*   **CSS in JS:**[react-jss](https://github.com/cssinjs/react-jss)
*   **开发代码传输** : [巴别塔](https://babeljs.io/docs/en)
*   **开发环境:** [故事书](https://storybook.js.org/)
*   **组件测试:** [jest](https://jestjs.io/) +【酵素】
*   **文档:**[react-style guide ist](https://github.com/styleguidist/react-styleguidist)([https://github.com/airbnb/enzyme](https://github.com/airbnb/enzyme))
*   **代码林挺+格式:** [诚信通](https://eslint.org/) + [漂亮点](https://github.com/prettier/prettier)

让我们逐一解决这些需求，一点一点地构建这个设计系统。

## 组件

我们将使用 ReactJS 构建我们的组件，在本例中，使用 JS 库中的 CSS 对它们进行样式化。你完全可以在你自己的系统中使用 CSS、SASS、LESS 或者任何你喜欢的东西。我选择 JS 中的 CSS 是因为它在设计系统中的好处。

JS [中的 CSS 提供了一些优势](https://hackernoon.com/all-you-need-to-know-about-css-in-js-984a72d48ebc),比如更少的死 CSS，按需优化的样式——而不是加载我们不使用的整个 CSS 样式表，以及通过在组件级别上分离 CSS 来实现更多的模块化。你的风格逻辑不仅仅是简单的文件——因为所有的类名都是唯一生成的(`.bem--btn-1389429`)，你不必担心名称空间冲突或风格泄漏。

这允许我们的组件尽可能的轻量级，并且在不需要会与其他系统冲突的全局风格的情况下进出其他应用程序。

> 如果你不知道 JS 中的 CSS 是什么，这里有[你需要知道的一切](https://hackernoon.com/all-you-need-to-know-about-css-in-js-984a72d48ebc)。这里是[为什么你可能不想使用它。](https://medium.com/@gajus/stop-using-css-in-javascript-for-web-development-fa32fb873dcc)

我将快速介绍我是如何选择与设计系统相关的 JS 库中的 CSS 的。如果你对这些不感兴趣，直接跳到*“开始你的项目”*。

### JS 大战中的 CSS

市场上有相当多的 JS 选项中的 CSS，从[风格组件](https://www.styled-components.com/)到[阿芙罗狄蒂](https://github.com/Khan/aphrodite)到[情感](https://github.com/emotion-js/emotion)到 JSS，以及它们之间的一切。那么，你如何在它们之间做出选择呢？

我把它归结为几个因素:易用性、包大小和性能(加载和渲染时间)。

如果你喜欢编写看起来更像实际 CSS 的代码，你可能会想使用**风格的组件**或**情感**。或者，如果您正在寻找现成的 React 本地支持，您会选择阿芙罗狄蒂。如果你想通过表现来判断，那么...那些数字[好像都在](https://github.com/oliviertassinari/a-journey-toward-better-style#results)[上方](https://github.com/hellofresh/css-in-js-perf-tests)[处](https://github.com/A-gambit/CSS-IN-JS-Benchmarks)。

### 我的 CSS 在 JS 中选择

最后，我选择了 JSS(特别是 react 集成的 react-jss)。它有很好的性能，小的捆绑包，和大系统一样的材料，用户界面显示了它的价值。

我最初选择了 Emotion，因为我喜欢写真正的 CSS 而不是 JSON。但是带有感情色彩的主题并不像 JSS 那样奏效。当你不使用它的“风格化”组件时，Emotion 在技术上表现得更好，但是当你想在 CSS 中使用主题变量时，它会迫使你使用它们。更少的性能，更多的抽象和依赖性——不是我想要的这个系统。

JSS 是明显的赢家，是时候建立我们的系统了。

## 开始你的项目

1.  为您的项目创建一个新文件夹:`mkdir your-design-system`
2.  在项目文件夹中，初始化一个 NPM 包:`npm init`
3.  创建一个 Git 存储库:`git init`
4.  安装开发依赖项:

```
npm i --save-dev react react-dom babel-cli babel-core babel-preset-env babel-preset-react @storybook/react @storybook/addon-options 
```

Enter fullscreen mode Exit fullscreen mode

1.  安装依赖项:`npm install react-jss`
2.  进入您的`package.json`并添加对等依赖关系:

```
{
  "peerDependencies": {
    "react": "^16.0.0",
    "react-dom": "^16.0.0"
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  在项目根目录下创建一个`.babelrc`文件，并添加这些预置配置:

```
{  "presets":  ["env",  "react"]  } 
```

Enter fullscreen mode Exit fullscreen mode

1.  在项目根目录下创建一个名为`.storybook`的新文件夹，并在该文件夹中创建一个`config.js`文件，配置如下:

```
import { configure } from '@storybook/react';
import { setOptions } from "@storybook/addon-options";

// Option defaults:
setOptions({
  /**
   * Name to display in the top left corner
   * @type {String}
   */
  name: 'JSS Design System',
  /**
   * URL for name in top left corner to link to
   * @type {String}
   */
  url: 'https://github.com/whoisryosuke',
  /**
   * Show story component as full screen
   * @type {Boolean}
   */
  goFullScreen: false,
  /**
   * Display left panel that shows a list of stories
   * @type {Boolean}
   */
  showLeftPanel: true,
  /**
   * Display horizontal panel that displays addon configurations
   * @type {Boolean}
   */
  showDownPanel: false,
  /**
   * Display floating search box to search through stories
   * @type {Boolean}
   */
  showSearchBox: false,
  /**
   * Show horizontal addons panel as a vertical panel on the right
   * @type {Boolean}
   */
  downPanelInRight: false,
  /**
   * Sorts stories
   * @type {Boolean}
   */
  sortStoriesByKind: false,
  /**
   * Regex for finding the hierarchy separator
   * @example:
   *   null - turn off hierarchy
   *   /\// - split by `/`
   *   /\./ - split by `.`
   *   /\/|\./ - split by `/` or `.`
   * @type {Regex}
   */
  hierarchySeparator: null,

  /**
   * Sidebar tree animations
   * @type {Boolean}
   */
  sidebarAnimations: true,

  /**
   * ID to select an addon panel
   * @type {String}
   */
  selectedAddonPanel: undefined // The order of addons in the "Addons Panel" is the same as you import them in 'addons.js'. The first panel will be opened by default as you run Storybook
})

// This will search the /src/components/ folder (and sub-folders) for any files that match <filename>.story.js 
// (e.g /src/components/Button/Button.story.js)
const req = require.context('../src/components/', true, /(\.story\.js$)|(\.story\.jsx$)/);
function loadStories() {
  req.keys().forEach((filename) => req(filename));
}

configure(loadStories, module) 
```

Enter fullscreen mode Exit fullscreen mode

现在，您有了一个开始开发组件的基础项目！让我们来分析一下刚刚发生的事情:

我们创建了一个新项目(NPM、Git 等)，安装了所有的依赖项，并为 Babel 和 Storybook 设置了默认配置。Storybook 配置明确地告诉 Storybook 从带有后缀`.story.js`的`src/components/`文件夹中获取所有的故事。

> 查看 [StorybookJS 的慢速启动指南](https://storybook.js.org/basics/slow-start-guide/),了解更多设置背后的信息。

## 创建第一个组件

我们将把组件放在`/src/components/`文件夹中。每个组件都将存储在它自己的文件夹中，最好是 Pascal 大小写的(ComponentNameExample)。内部将包含任何组件、故事、测试和一个为所有组件提供默认导出的`index.js`。

应该是这样的:

```
components
└─┬ Button
     ├── Button.js
     ├── Button.story.js
     ├── Button.test.js
     ├── ButtonAlternate.js
     ├── ButtonAlternate.story.js
     └── ButtonAlternate.test.js 
```

Enter fullscreen mode Exit fullscreen mode

让我们从在`/src/components/Button/Button.js` :
创建一个新组件开始

```
import React from "react";
// The HOC we wrap our components in to apply styles
import injectSheet from "react-jss";

// Your CSS file - in a JS object
const styles = theme => ({

  // All top level object keys are different class names
  myButton: {
    // Global style applied from theming
    color: theme.text.color,
    margin: {
      // jss-expand gives more readable syntax
      top: 5, // jss-default-unit makes this 5px
      right: 0,
      bottom: 0,
      left: "1rem"
    },

    // And we get SASS/LESS like qualities with the nested &
    "& span": {
      // jss-nested applies this to a child span
      fontWeight: "bold" // jss-camel-case turns this into 'font-weight'
    }
  },
  myLabel: {
    fontStyle: "italic"
  }
});

// Define the component using these styles and pass it the 'classes' prop.
// Use this to assign scoped class names.
const Button = ({ classes, children }) => (
  <button className={classes.myButton}>
    <span className={classes.myLabel}>{children}</span>
  </button> );

// Export component with HOC to apply styles from above
export default injectSheet(styles)(Button) 
```

Enter fullscreen mode Exit fullscreen mode

让我们分解这个组件，了解 JSS 是如何工作的。

我们看到的第一个主要东西是一个用于 CSS 样式的变量，叫做`styles`。在这种情况下，`styles`变量是一个接受`theme`对象的函数，并返回一个 CSS 类的对象。那个`theme`对象包含我们放在`theme.js`文件中的全局值，允许我们像`theme.text.color`一样设置动态值。

> 如果不需要访问任何主题变量，`styles`变量可以只是一个对象(而不是函数)。

在`styles`变量下面是实际的按钮本身，它只是一个功能性的 React 组件。`injectSheet` HOC 处理样式变量，并在按钮组件上提供一个`classes`道具。我们从那里获取类名，并使用`className={classes.buttonClassName}`应用它们。

**所以基本上:**

*   CSS 是作为 Javascript 对象编写的
*   我们将组件包装在一个“HOC”中(见下文)
*   HOC 将 CSS *对象*编译成*实际 CSS* 并注入到 app 中(作为`<head>`中的`<style></style>`元素)
*   HOC 还为我们的组件提供了一个`classes` prop，它包含我们之前在 CSS 对象中编写的任何类名。
*   然后我们使用`classes`对象将类名应用到组件中(有点像 [CSS 模块](https://github.com/css-modules/css-modules)

现在我们有了一个组件，让我们来看看如何看待它。

## 设置开发环境

开发 React 组件的问题是要有一个开发环境来使这个过程成为可能。你通常会发现自己在使用像 **Create React App** 这样的样板文件，或者创建一个**定制 Webpack 配置**来将 JS 编译成页面。我们使用 StorybookJS，而不是*用所有 Create React 应用程序依赖关系来膨胀我们的项目，或者经历设置 Webpack 的麻烦只是为了查看组件。*

[**StorybookJS**](http://storybook.js.org) 是你的组件的一个活的开发环境。在本地启动 StorybookJS 会在您的浏览器中启动一个组件库，并提供实时组件预览(如果您保存了更改，还可以热重装)。通过创建*“故事”*，我们可以浏览我们的组件，甚至创建不同的状态(比如一个活动的或禁用的按钮)。

> *讽刺的是*，StorybookJS 使用 Create React App 来运行你的应用。它只是按需下载。

## 创作故事

我们的 Storybook config ( `/.storybook/config.js`)浏览我们的`src/components/`文件夹，找到任何带有后缀`.story.js`的文件。

我们可以通过在`src/components/Button/Button.story.js` :
中创建一个文件来创建我们的第一个故事

```
import React from 'react';
import { storiesOf } from '@storybook/react';
import { action } from '@storybook/addon-actions';
import { ThemeProvider } from 'react-jss'
import theme from "../../theme/theme";

import Button from "./Button";

storiesOf('Button', module)
  .add('with text', () => (
    <ThemeProvider theme={theme}>
      <Button onClick={action('clicked')}>Hello Button</Button>
    </ThemeProvider> 
  ))
  .add('with some emoji', () => (
    <ThemeProvider theme={theme}>
      <Button onClick={action('clicked')}><span role="img" aria-label="so cool">😀 😎 👍 💯</span></Button>
    </ThemeProvider>
  )); 
```

Enter fullscreen mode Exit fullscreen mode

Storybook 使用一个名为`storiesOf`的函数来创建组件的故事。我们将`.add(description, component)`方法与组件的变化联系起来。

我们在这里做的唯一特别的事情是将我们的组件包装在一个`<ThemeProvider>`组件中。这来自于`react-jss`，并使用 React 的上下文提供者系统将`theme`对象传递给我们的`<Button>`。如果我们不包装它，我们就不能在组件中使用`theme`变量。

## 运行你的故事书

启动 StorybookJS 就像`npm run storybook`一样简单。这将启动位于 [http://localhost:9001/](http://localhost:9001/) 的开发服务器。在这里你可以找到故事书仪表板，以及你在`src/components/`中创建的任何故事。

如果你在 Storybook dev server 运行时对你的组件做了任何更改并保存，它将重新构建并重新加载任何更改。

## 就这么简单！

您会惊讶于开始开发 React 组件并将其部署为 NPM 包是多么容易。

在一天结束的时候，**你只需要编写 React 代码**并提交给 git。**没有必要设置疯狂的构建过程**像 Webpack 或 package，甚至 Babel 真的。您的组件将被导入到其他人的项目中，这些人将在他们那端处理传输。你的项目越简单，实际上就越好。

你可以在这里看到我最后的 [JSS 设计系统样板](https://github.com/whoisryosuke/jss-design-system)，也可以看看标有‘starter’的分支。或者查看 [Emotion JS 设计系统样板](https://github.com/whoisryosuke/emotion-semantic-ui)进行对比。

在教程的下一部分，我将介绍添加测试、林挺和自动化组件文档！

如果你想把你的设计系统添加到 NPM，[点击这里查看他们的指南。](https://docs.npmjs.com/getting-started/publishing-npm-packages)

摇滚起来🤘
良

**参考文献**

*   [react-jss](https://github.com/cssinjs/react-jss)
*   [反应的故事书](https://storybook.js.org/basics/guide-react/)
*   [JS 性能中的 CSS 1](https://github.com/oliviertassinari/a-journey-toward-better-style#results)
*   [JS 性能中的 CSS 2](https://medium.com/@tkh44/emotion-ad1c45c6d28b)
*   [JS 性能中的 CSS 3](https://github.com/hellofresh/css-in-js-perf-tests)
*   [JS 性能中的 CSS 4](http://cssinjs.org/performance/?v=v9.8.7)
*   [JS 性能中的 CSS 5](https://github.com/A-gambit/CSS-IN-JS-Benchmarks)
*   [JS 性能中的 CSS 6-为什么 Material UI 选择了 JSS 而不是样式化组件](https://github.com/mui-org/material-ui/issues/6115)
*   [使用情感的电路界面设计系统](https://github.com/sumup/circuit-ui)
*   [视频:西达尔特·克谢特帕尔——我们需要谈谈我们的前端工作流程——反应测试](https://www.youtube.com/watch?v=bLgZwFRYTJ4)
*   [视频:Jon Gold - react-sketchapp:作为数据函数的设计](https://www.youtube.com/watch?v=iMunXpy2Ezo)
*   [风格指南指南](http://bradfrost.github.io/style-guide-guide/)