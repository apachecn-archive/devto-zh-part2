# React 故事书入门

> 原文：<https://dev.to/nickytonline/getting-started-with-react-storybook-9jh>

更新:自从这本书写出来后，故事书的世界发生了很大的变化。如果你要将故事书添加到你的项目中，请查看故事书网站上的[教程](https://www.learnstorybook.com/intro-to-storybook/)，学习如何使用他们的 CLI 启动并运行故事书

## 故事什么？

Storybook 是开发和展示组件的好工具。我太喜欢它了，去年夏天我在 [js-montreal](http://js-montreal.org) 做了一次关于它的[演讲。Storybook 迫使你将组件开发成组件，这是一件好事，因为你不在实际的应用程序中。它支持](https://story.iamdeveloper.com)[反应](https://storybook.js.org/basics/guide-react)，反应原生， [Vue](https://storybook.js.org/basics/guide-vue) 和[角度](https://storybook.js.org/basics/guide-angular)。

## 在你的项目中安装并运行故事书

我们假设您已经创建了一个 React 项目。

*   如果你已经安装了`npx`，运行`npx @storybook/cli`。更多关于`npx`的信息，请查看[介绍 npx:一个 npm 包裹运送者——Kat Marchán——Medium](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)。如果你没有安装`npx`，你需要通过`npm install @storybook/cli -g`全局安装 CLI。
*   确保您位于前端项目的根目录中。
*   从命令行运行。`getstorybook`。因为您已经将 React 安装为依赖项，`getstorybook`将知道为 Storybook 安装必要的包，以便 React 作为开发依赖项。

```
"@storybook/addon-actions": "3.3.15",
"@storybook/addon-links": "3.3.15",
"@storybook/addons": "3.3.15",
"@storybook/react": "3.3.15", 
```

Enter fullscreen mode Exit fullscreen mode

*   如果您查看您的`package.json`文件，您将有两个新的脚本。

```
"storybook": "start-storybook -p 6006",
"build-storybook": "build-storybook" 
```

Enter fullscreen mode Exit fullscreen mode

*   从命令行运行`npm run storybook`。
*   导航到`http://localhost:6006`(或者任何故事书在`storybook` npm 脚本中给你的端口。
*   嘣！你在童话世界里。去拍拍自己的背。

[![Screenshot of Storybook in action](../Images/775100639bf171f97593275be64265cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YE6_iwqF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7a9brlemrfxvrizy8ipu.gif)

*   另一个脚本`build-storybook`，如果运行，将会生成一个静态故事书站点，您可以将它部署到您喜欢的任何地方。默认情况下，它会被生成到一个名为`storybook-static`的文件夹中。

## 解剖一个故事

既然您已经在项目中运行了 Storybook，那么您可能已经看过了它附带的开箱即用的故事。

让我们分析一下其中一个示例故事中发生了什么。

```
// We need the storiesOf function to write our stories.
import { storiesOf } from '@storybook/react';

// A function that allows you to simulate an action.
import { action } from '@storybook/addon-actions';

// The React component that we want to use in our Storybook stories.
import { Button } from '@storybook/react/demo';

// Here Button is the component name that you will see in the collapsible component tree
// in the Storybook application.
storiesOf('Button', module)

  // A first story to show what the button looks like with text.
  // Notice the simulated action as well.
  .add('with text', () => <Button onClick={action('clicked')}>Hello Button</Button>) 
  // A second story to show what the button looks like with emojis.
  .add('with some emoji', () => <Button onClick={action('clicked')}>😀 😎 👍 💯</Button>); 
```

Enter fullscreen mode Exit fullscreen mode

这差不多就是写故事的全部内容。它真的很容易使用，是一个非常好的工具。在我的下一篇文章中，我们将深入探究故事书的一些很酷的特性。

## 参考文献

*   [故事书](https://storybook.js.org)，这里有很多很棒的文档。
*   故事书中 airbnb 的一个很好的例子。
*   我去年演讲的幻灯片，[story.iamdeveloper.com](http://story.iamdeveloper.com)