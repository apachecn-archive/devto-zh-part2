# 向设计系统添加测试、林挺和文档📚

> 原文：<https://dev.to/whoisryosuke/adding-testing-linting-and-docs-to-a-design-system--47nf>

在上一个教程中，我们使用 ReactJS 和 JS 中的 CSS 创建了一个设计系统的基础。

现在我们可以向设计系统添加基本的测试、林挺和自动化文档。

我们将使用:

*   [t1 是 T3](https://jestjs.io/)
*   [**酶**](https://github.com/airbnb/enzyme/)
***   [**巴别塔**T3】](https://babeljs.io/)*   [**ESLint**](https://eslint.org/)*   ***   [**React-Styleguidist**](https://react-styleguidist.js.org/)。****

 ****你可能会问自己:“我已经有一个设计系统了。为什么要这样做？”。这样做的好处很简单:

*   ✅保证我们的组件*实际上*工作(通过**测试**
*   🚫确保我们的代码没有错误(用**林挺**)
*   🔏强制代码样式和格式(用**林挺**)
*   📚允许开发人员和设计人员轻松浏览您的组件库(使用**样式指南**

所以让我们开始吧！

## 安装 Jest +酵素🧙‍

让我们安装 Jest、Enzyme 和其他必要的依赖项:

```
npm install -D jest enzyme enzyme-adapter-react-16 enzyme-to-json babel-jest babel-core regenerator-runtime 
```

当他们在安装的时候⏳，下面是一些软件包的功能:

*   jest - Javascript 测试框架，允许你创建对函数和类进行断言的测试。
*   这扩展了 Jest，允许我们渲染 React 组件来测试它们的功能。
*   这允许 jest 测试使用 babel 编译代码。
*   [Enzyme-adapter-react-16](https://github.com/airbnb/enzyme/tree/master/packages/enzyme-adapter-react-16)-Enzyme 要求每个版本的 React 都有一个适配器。
*   [再生器-运行时](https://www.npmjs.com/package/regenerator-runtime) -启用 JS 生成器和异步。

### 钩上是

为了使用 Jest，我们将运行命令`jest`。我们还可以通过运行`jest --watch`让 Jest 观察我们的测试文件的任何变化。

为了使这个过程变得更简单、语义化和不可知论——我们将在我们的`package.json`中添加一个脚本，用命令`npm run test`为我们运行 Jest。

将此添加到您的`package.json` :

```
 "scripts": {
    "test": "jest",
    "test:watch": "jest --watch"
  },

  // ...other parameters...

  "jest": {
    "setupTestFrameworkScriptFile": "<rootDir>tests/setup/setupEnzyme.js",
    "testPathIgnorePatterns": [
      "<rootDir>/tests/setup/"
    ]
  } 
```

### 设置酶

最后，我们必须设置 Enzyme——你可以在 jest `package.json`配置中看到它。

在`<project-root>/tests/setup/setupEnzyme.js`创建一个新文件，并添加以下内容:

```
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

Enzyme.configure({ adapter: new Adapter() }); 
```

## 创建您的第一个测试⚙️

让我们在名为`Button.test.js`的`<Button>`组件文件夹中创建第一个测试。这非常简单，只需检查组件是否呈现:

```
import React from 'react';
import { shallow, mount, render } from 'enzyme';
import { ThemeProvider } from "react-jss";
import Button from "./Button";
import theme from "../../theme/theme";

// Jest's describe function accepts a test description
// And a function containing assertions
describe('Button Component', () => {

  it('should render without throwing an error', () => {
    expect(shallow(<ThemeProvider theme={theme}><Button>Test</Button></ThemeProvider>).exists()).toBe(true)
  })
}) 
```

## 运行测试⚡️

现在您已经创建了一个测试，让我们运行它吧！`npm run test`且看成败。理想情况下，它应该成功，但是如果没有成功，您将会在控制台中看到任何错误输出。

## 添加林挺🔏

实际上，对于任何项目，我都有一个单独的指南。你可以在这里找到我的指南[使用 ESLint 和 Prettier 给你的项目添加自动代码林挺和格式化。](http://whoisryosuke.com/blog/2018/setting-up-eslint-prettier-on-project/)

## 添加文档📚

我们将使用 [react-styleguidist](https://react-styleguidist.js.org/) 快速将文档添加到我们的设计系统中。有了这些文档，开发人员将能够快速看到所有可用的组件、它们的 prop 类型以及任何特定的活动组件示例或书面指南。这些文档是根据代码中的注释和您提供的 Markdown 文件自动生成的。

它非常容易设置，并允许我们专注于开发我们的系统，而不是建立文档基础设施。

### 安装 Webpack

我们需要安装 **Webpack** (我知道，我在上一个教程中说过我们不需要它——但是 styleguidist *需要*它😅)和 **babel-loader** 来使 Webpack 能够传输我们的 JS:

```
npm install --save-dev webpack babel-loader 
```

在您的项目根:
中创建一个`webpack.config.js`

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      }
    ]
  }
}; 
```

### 安装 React-Styleguidist

现在我们可以安装 react-styleguidist:

```
npm install --save-dev react-styleguidist 
```

将这些脚本添加到您的`package.json` :

```
 "scripts": {
    // ...test scripts, etc...

    "docs": "npx styleguidist server",
    "docs:build": "npx styleguidist build",
  }, 
```

现在您可以运行`npm run docs`来为您的组件启动开发服务器。

> 如果你不想使用 Storybook *(和写故事什么的)*，这也可以是浏览组件变化的一种选择。在这里，您也可以热重装，允许您在 Markdown 中制作组件示例，并在浏览器中检查实时更改。

### 但是等等！它和我的 JSS 主题不搭配？😭

因为我们为 JSS 组件使用了主题，所以如果不在道具中提供主题对象，它们就不能被渲染。我们通常通过将组件包装在`<ThemeProvider>`组件中来实现这一点。我们可以用这个`<ThemeProvider>`包装每个 Markdown 文件中的每个组件——或者我们可以将整个应用程序包装在其中，这样我们的组件可以在任何地方访问主题。

创建名为`.styleguidist/components/Wrapper.js` :
的新文件

```
import React from "react";
import PropTypes from "prop-types";
import { ThemeProvider } from "react-jss";
import theme from "../../src/theme/theme";

class Wrapper extends React.Component {
  render() {
    return <ThemeProvider theme={theme}>{this.props.children}</ThemeProvider>;
  }
}

Wrapper.propTypes = {
  /**
   * Child components (array or single element)
   */
  children: PropTypes.oneOfType([
    PropTypes.arrayOf(PropTypes.node),
    PropTypes.node
  ]).isRequired
};

export default Wrapper; 
```

这用我们自己的组件替换了 react-styleguidist 中默认的`<Wrapper>`组件，它只是将`<ThemeProvider>`包装在组件的子组件周围。

> 我在 react-styleguidist 文档中找到了这个例子，它展示了如果你需要的话如何集成 Redux。

现在我们可以将它添加到我们的`styleguide.config.js` :

```
const path = require("path");

module.exports = {
  // Sets up Styleguidist with our Webpack setup
  webpackConfig: require("./webpack.config.js"),

  // Override Styleguidist doc components
  styleguideComponents: {
    Wrapper: path.join(__dirname, ".styleguidist/components/Wrapper")
  }
}; 
```

它通知 react-styleguidist 用我们自己的组件覆盖该组件。随意改变这里的路径，不确定在哪里填充这个。

### 忽略故事📘

您会注意到，如果您旋转文档，story 组件也会显示出来。

我们可以通过在`storybook.config.js` :
中添加一个忽略参数来禁用它

```
module.exports = {

  // The other config params

  // Files to ignore from docs
  ignore: ["**/*.story.js", "**/*.test.js"]
}; 
```

## 测试，林挺，文档完成！🙌

这个设计系统被一点一点地组合成一个完整的 CSS in JS 设计系统，包括开发环境、文档、测试和林挺。

看着像 [Ant Design](https://github.com/ant-design/ant-design) 或 [Material UI](https://github.com/mui-org/material-ui) 这样的开发设计系统可能会令人生畏，当被迫对架构进行逆向工程时，收集知识就更难了。但是就像任何项目一样，**每个代码库都从一行代码** *(或者 copypasta 样板)*开始。试着浏览提交历史的开头，看看代码重构了多少。

你自己的设计系统一开始也不会是如此庞大、复杂的组件网络。但就像所有其他系统一样，它会支持每一个功能。从小处着手，只构建你需要的东西，然后用同样的思路扩展。**为解决问题而构建。**随着你逐渐成长，一周或一个月后，你会发现迭代的变化是巨大的。

希望这有所帮助！
良

* * *

**参考文献**

*   [使用 Webpack 4 设置 React】](https://medium.freecodecamp.org/part-1-react-app-from-scratch-using-webpack-4-562b1d231e75)
*   [为 React-Styleguidist 创建包装器](https://react-styleguidist.js.org/docs/thirdparties.html#redux)****