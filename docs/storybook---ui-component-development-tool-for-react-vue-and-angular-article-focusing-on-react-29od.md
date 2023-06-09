# story book——用于 React、Vue 和 Angular 的 UI 组件开发工具(文章关注 React)

> 原文：<https://dev.to/madhu/storybook---ui-component-development-tool-for-react-vue-and-angular-article-focusing-on-react-29od>

[![Storybook](img/b6103ec9f11463e0ca65cf7f0f1e3e63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0REcx4xZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h0tkhbillluy2661maka.png)

[Storybook](https://storybook.js.org/basics/guide-react/) 是你 UI 组件的 UI 开发环境。有了它，您可以可视化 UI 组件的不同状态，并以交互方式开发它们。能够快速可视化组件并与之交互非常有用。

有了 [Storybook](https://storybook.js.org/basics/guide-react/) ，很容易利用这些 React 组件进行 UI 测试。

一旦您创建了 React 组件，您将需要添加样式并测试该组件如何使用不同的数据集进行渲染。您可以将数据传递到一个组件中，而 [Storybook](https://storybook.js.org/basics/guide-react/) 将自己呈现该组件。

这对于特殊情况很有帮助，比如没有数据(可能会显示“没有结果”消息)，或者内容较长，可能会搞乱 UI。故事书测试允许您测试这些 UI 案例，而不必摆弄数据库中的测试数据、硬编码到组件中等等。

## 开始使用

[Storybook](https://storybook.js.org/basics/guide-react/) 在开发模式下与你的应用一起运行。它帮助您构建与应用程序的业务逻辑和上下文无关的 UI 组件。

### 设置 React 故事书

要设置 React Storybook，首先需要一个 React 项目。如果您目前没有合适的，您可以使用`create-react-app`轻松创建一个。

开始使用 Storybook 最简单的方法是使用 getstorybook 工具，这是一个 CLI，它可以扫描您的应用程序，并进行(小)更改以使 storybook 正常工作。你可以这样使用它:

```
npm i --save-dev @storybook/react

cd [your-app]
getstorybook 
```

Enter fullscreen mode Exit fullscreen mode

注意:我在我的项目中使用了 yarn package manager，因为我在我的 npm 项目中发现了一些运行`getstorybook`命令的问题。

`getstorybook`会在你的 react 应用中添加一个名为`.storybook/`的文件夹，其中包含一个文件`config.js`和`addons.js`。这个文件是你的故事书的“入口点”,从这里你需要每个包含任何组件的故事的文件。默认情况下，只是从一个名为 stories/index.js 的文件开始，尽管您可以自定义这个文件。

要运行 Storybook，执行`yarn run storybook`并打开显示的地址( [http://localhost:9009/](http://localhost:9009/) )。该应用程序应该如下所示:

[![Storybook](img/edd42469958bbccc41dbbc7d381f0e06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nw6_PDi0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k3fpdsu678ef41qb9hl0.png)

## 写新故事

#### 配置

我们必须让`Storybook`知道`.storybook/config.js`中的故事在哪里。

```
import { configure} from '@storybook/react';

const req = require.context('../src/components', true, /\.stories\.js$/);

function loadStories() {
   req.keys().forEach(path => req(path)); // customized stories path
}

configure(loadStories, module); 
```

Enter fullscreen mode Exit fullscreen mode

它可以是直接的故事路径，也可以是定制的故事路径，其中包含所有与 regex `/\.stories\.js$/`匹配的文件。

#### 一个简单的故事

编写一个简单的`welcome.js`组件，其中只包含欢迎文本。

```
import React, { Component } from 'react'

export default class Welcome extends Component {

 render() {
   return (
     <div>
       <h1>{this.props.message}</h1>
     </div>
   )
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

创造一个简单的`welcome.stories.js`故事。

```
import React from 'react';
import { storiesOf } from '@storybook/react';
import Header from '../components/Welcome';

const stories = storiesOf('Welcome', module);

stories.add('welcome message', () => (

   <Header
     message="Welcome to Storybook!"
   />

)); 
```

Enter fullscreen mode Exit fullscreen mode

故事书会是这样的。

[![Storybook](img/955c92c70c6e6a48e6a9baf0a50f1355.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t9inXfeI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dullh3t5keejdyk2weez.png)

## 插件

故事书本身非常有用，但为了让事情变得更好，它还有一些插件。在本文中，我们将只涉及其中的一部分，但请务必稍后查看官方列表。

注意:插件的版本应该与故事书的版本兼容。

*   [故事书-插件-jsx](https://github.com/storybooks/addon-jsx#storybook-addon-jsx)

这本故事书向你展示了故事的 JSX。例如，查看您设置的道具可能会很有用。

```
yarn add -D @storybook/addons

yarn add -D storybook-addon-jsx 
```

Enter fullscreen mode Exit fullscreen mode

安装后，我们需要在`.storybook/config.js`中对其进行全局配置。

```
import { configure} from '@storybook/react';
import JSXAddon from 'storybook-addon-jsx';

setAddon(JSXAddon);

const req = require.context('../src/components', true, /\.stories\.js$/);

function loadStories() {
   req.keys().forEach(path => req(path)); // customized stories path
}

configure(loadStories, module); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们要用`.addWithJSX`代替`.add`。

```
stories.addWithJSX('welcome message', () => (

   <Header
     message="Welcome to Storybook!"
   />

)); 
```

Enter fullscreen mode Exit fullscreen mode

故事书插件部分看起来像这样。

[![Addons](img/e6c977b755bd85f6f4d8275ccb93c65a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8XNOeMG9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fam920y1tu3yl0z6ri8x.png)

*   [@故事书/插件旋钮](https://www.npmjs.com/package/@storybook/addon-knobs?activeTab=readme)

故事书插件旋钮允许你使用故事书用户界面动态编辑 React 道具。你也可以在 Storybook 中使用 Knobs 作为动态变量 inside stories。

```
yarn add -D @storybook/addon-knobs 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须添加`.addDecorator(withKnobs)`来使用我们的`welcome.stories.js`中的旋钮。

```
import React from 'react';
import { storiesOf } from '@storybook/react';
import Header from '../components/Welcome';

import { withKnobs, text, boolean, number } from '@storybook/addon-knobs/react';

const stories = storiesOf('Welcome', module);

stories.addDecorator(withKnobs).add('welcome message', () => (

   <Header
     message={text('headline','Welcome to Storybook!')}
   />

)); 
```

Enter fullscreen mode Exit fullscreen mode

故事书插件部分看起来像这样。

[![Addons](img/13a448b32af810ca724c56dc306a4f92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fvtc8VkC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2hv2m01ylt4d24mhols3.png)

在使用这些插件之前，我们必须在`.storybook/addons.js`文件中注册这些插件(一些特定的插件不需要这样注册，请参考 [NPM 站点](https://www.npmjs.com/)了解每个插件的详细信息)。

```
import '@storybook/addon-actions/register';
import '@storybook/addon-links/register';
import '@storybook/addon-knobs/register';
import 'storybook-addon-jsx/register'; 
```

Enter fullscreen mode Exit fullscreen mode

很少有其他插件是；

*   [故事书-插件-剪刀](https://www.npmjs.com/package/storybook-addon-scissors)
*   [故事书信息插件](https://github.com/storybooks/storybook/tree/master/addons/info#storybook-info-addon)