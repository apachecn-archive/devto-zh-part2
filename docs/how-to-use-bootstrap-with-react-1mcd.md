# 如何在 React 中使用 Bootstrap

> 原文：<https://dev.to/bnevilleoneill/how-to-use-bootstrap-with-react-1mcd>

[![](img/b032a961794642714094a79f1bca5786.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CCXD3gIH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/546/1%2A36D6oCrl2Fpif_8NzK2lYA.png)

随着过去几年单页面应用程序的日益流行，出现了许多前端 JavaScript 框架，如 [Angular](https://angular.io/) 、 [React](https://reactjs.org/) 、 [VueJS](https://vuejs.org/) 、 [Ember](https://emberjs.com/) ，不胜枚举。因此，使用像 jQuery 这样的 DOM 库不再是构建 web 应用程序的必要条件。

另一方面，也出现了一些 CSS 框架来帮助满足构建响应式 web 应用程序的需求。几乎每个前端开发人员都必须使用或听说过 [Bootstrap](https://getbootstrap.com/) 、 [Foundation](https://foundation.zurb.com/) 或[布尔玛](https://bulma.io/)——每一个都是具有健壮特性和内置实用程序的响应式(移动优先)CSS 框架。

React 已经成为构建 web 应用程序最常用的 JavaScript 框架，而 Bootstrap 是最受欢迎的 CSS 框架，为数百万个互联网网站提供支持。因此有必要探索在 React 应用中使用 Bootstrap 的各种方式，这也是本教程的目的。

本教程不以任何方式试图详细教授 React 或 Bootstrap。你应该已经有一些使用 React 和/或 Bootstrap 的经验。如果你需要任何帮助，查看 [React 文档](https://reactjs.org/docs)和[引导文档](https://getbootstrap.com/docs/4.1/getting-started/introduction/)。

### 添加自举

可以通过几种方式将 Bootstrap 添加到 React 应用程序中。在本教程中，我们只对三种最常见的方式感兴趣:

1.  使用引导 CDN
2.  作为依赖项的引导
3.  React 引导包

### 使用自举 CDN

这是将 Bootstrap 添加到应用程序的最简单方法。不需要安装或下载。您只需将一个<link>放入应用程序的部分，如下面的代码片段所示。

```
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/css/bootstrap.min.css" 
integrity="sha384-9gVQ4dYFwwWSjIDZnLEWnxCjeSWFphJiwGPXr1jddIhOegiu1FwO5qRGvFXOdJZ4" 
crossorigin="anonymous"> 
```

如果您对使用 Bootstrap 附带的 JavaScript 组件感兴趣，您需要将下面的

```
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" 
integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" 
crossorigin="anonymous"></script>

<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.0/umd/popper.min.js" 
integrity="sha384-cs/chFZiN24E4KMATLdqdvsezGxaGsi4hLGOzlXwp5UZB1LY//20VyM2taTB4QvJ" 
crossorigin="anonymous"></script>

<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/js/bootstrap.min.js" 
integrity="sha384-uefMccjFJAIv6A+rW+L4AHf99KvxDjWSu1z9VI8SKNVmz4sk7buKt/6v9KI65qnm" 
crossorigin="anonymous"></script> 
```

正如你所看到的，Bootstrap 4 的 JavaScript 组件需要 [jQuery](http://jquery.com/) 和 [Popper.js](https://popper.js.org/) 。在上面的代码片段中，我们使用了 jQuery 的精简版，尽管您也可以使用完整版。

对于 React 应用程序，这些代码片段通常会添加到应用程序的索引页面中。如果您使用 [create-react-app](https://github.com/facebook/create-react-app) 来创建您的应用程序，那么您的 public/index.html 页面应该看起来像下面的代码片段:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="theme-color" content="#000000">
    <!--
      manifest.json provides metadata used when your web app is added to the
      homescreen on Android. See https://developers.google.com/web/fundamentals/engage-and-retain/web-app-manifest/
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json">
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
    <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.
      Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/css/bootstrap.min.css" 
    integrity="sha384-9gVQ4dYFwwWSjIDZnLEWnxCjeSWFphJiwGPXr1jddIhOegiu1FwO5qRGvFXOdJZ4" 
    crossorigin="anonymous">

    React App
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
    <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.
      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.
      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->

    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" 
    integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" 
    crossorigin="anonymous"></script>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.0/umd/popper.min.js" 
    integrity="sha384-cs/chFZiN24E4KMATLdqdvsezGxaGsi4hLGOzlXwp5UZB1LY//20VyM2taTB4QvJ" 
    crossorigin="anonymous"></script>

    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/js/bootstrap.min.js" 
    integrity="sha384-uefMccjFJAIv6A+rW+L4AHf99KvxDjWSu1z9VI8SKNVmz4sk7buKt/6v9KI65qnm" 
    crossorigin="anonymous"></script>

  </body>
</html> 
```

现在，您可以开始在 React 应用程序组件中使用内置的引导类和 JavaScript 组件。

[![](img/184dfcc3649f17c50808cfdfb9409f6a.png)T2】](https://logrocket.com/?cid=banner_b)

### 自举为依赖

如果您正在使用构建工具或模块捆绑器，如 [Webpack](https://webpack.js.org/) ，那么这是将引导添加到 React 应用程序的首选选项。您需要安装 Bootstrap 作为应用程序的依赖项。

```
npm install bootstrap 
```

或者如果你用的是 [**纱**](https://yarnpkg.com/) :

```
yarn add bootstrap 
```

一旦安装了 Bootstrap，您就可以将它包含到应用程序的入口 JavaScript 文件中。如果你用的是 [create-react-app](https://github.com/facebook/create-react-app) ，这应该是你的 src/index.js 文件。

```
import 'bootstrap/dist/css/bootstrap.min.css';
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(<App />, document.getElementById('root'));
registerServiceWorker(); 
```

请注意，我们已经导入了自举迷你 CSS 作为第一个依赖项。这样，我们就可以在 React 应用程序组件中使用内置的引导类了。然而，在您的应用程序中使用 Bootstrap 的 JavaScript 组件之前，您需要安装 jquery 和 popper.js(如果它们尚未安装的话)。

```
npm install jquery popper.js 
```

接下来，您将对 src/index.js 文件进行额外的更改，以添加新的依赖项，如下面的代码片段所示。

```
import 'bootstrap/dist/css/bootstrap.min.css';
import $ from 'jquery';
import Popper from 'popper.js';
import 'bootstrap/dist/js/bootstrap.bundle.min';
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(<Dropdown />, document.getElementById('root'));
registerServiceWorker(); 
```

这里我们添加了$和 Popper 的导入。我们还导入了引导 JavaScript minified bundle 文件。现在，您可以在 React 应用程序中使用引导 JavaScript 组件。

### 反应自举包

我们可以将引导程序添加到 React 应用程序的第三种方法是使用一个包，该包已经重新构建了引导程序组件，特别是作为 React 组件工作。在 [**npm**](https://www.npmjs.com/) 资源库中有几个这样的包，但在本教程中我将重点介绍其中最受欢迎的两个，即:

1.  [反应-引导](https://react-bootstrap.github.io/)
2.  [反应陷阱](https://reactstrap.github.io/)

这两个包都是在 React 应用中使用 Bootstrap 的绝佳选择，尽管你不一定需要使用它们中的任何一个。他们有非常相似的特征。

### 使用内置的引导类和组件

通过像应用任何其他类一样应用内置类，您可以在 React 应用程序中的元素和组件上直接使用 Bootstrap。让我们构建一个简单的主题切换器 React 组件来演示如何使用引导类和组件。

[![](img/420434108e00cffbc2818ccb2353e35a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ej3VD1-b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/512/0%2AFKEuOi6FpIfGzdOO.gif) 

<figcaption>主题切换器演示</figcaption>

如本演示所示，我们使用 Bootstrap 中可用的下拉组件来实现我们的主题切换器。我们还使用内置的按钮类来设置下拉按钮的大小和颜色。

我们将继续为 ThemeSwitcher 组件编写代码。确保您已经设置了 React 应用程序。为组件创建一个新文件，并将下面的代码片段添加到其中:

```
import React, { Component } from 'react';

class ThemeSwitcher extends Component {

  state = { theme: null }

  resetTheme = evt => {
    evt.preventDefault();
    this.setState({ theme: null });
  }

  chooseTheme = (theme, evt) => {
    evt.preventDefault();
    this.setState({ theme });
  }

  render() {

    const { theme } = this.state;
    const themeClass = theme ? theme.toLowerCase() : 'secondary';

    return (
      <div className="d-flex flex-wrap justify-content-center position-absolute w-100 h-100 align-items-center align-content-center">

        <span className={`h1 mb-4 w-100 text-center text-${themeClass}`}>{ theme || 'Default' }</span>

        <div className="btn-group">

          <button type="button" className={`btn btn-${themeClass} btn-lg`}>{ theme || 'Choose' } Theme</button>

          <button type="button" className={`btn btn-${themeClass} btn-lg dropdown-toggle dropdown-toggle-split`} data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            <span className="sr-only">Toggle Theme Dropdown</span>
          </button>

          <div className="dropdown-menu">

            <a className="dropdown-item" href="#" onClick={e => this.chooseTheme('Primary', e)}>Primary Theme</a>
            <a className="dropdown-item" href="#" onClick={e => this.chooseTheme('Danger', e)}>Danger Theme</a>
            <a class="dropdown-item" href="#" onClick={e => this.chooseTheme('Success', e)}>Success Theme</a>

            <div className="dropdown-divider"></div>

            <a className="dropdown-item" href="#" onClick={this.resetTheme}>Default Theme</a>
          </div>

        </div>

      </div>
    );

  }

}

export default ThemeSwitcher; 
```

这里，我们利用 Bootstrap 的 dropdown 组件和几个内置类构建了一个非常简单的主题切换器组件。您可以使用此处的引导下拉菜单了解更多关于[的信息。](https://getbootstrap.com/docs/4.1/components/dropdowns/)

首先，我们用一个主题属性设置组件的状态，并将其初始化为 null。接下来，我们在组件类上定义了两个点击事件处理程序 resetTheme()和 chooseTheme()，分别用于选择主题和重置主题。

在 render()方法中，我们呈现了一个包含三个主题的拆分按钮下拉菜单:主要、危险和成功。每个菜单项都附加了一个用于相应操作的 click 事件处理程序。注意我们如何使用 theme.toLowerCase()来获取下拉按钮和文本的主题颜色类。如果没有设置主题，我们默认使用次要颜色作为主题颜色。

在这个例子中，我们看到了在 React 应用程序中使用 Bootstrap 的内置类和组件是多么容易。查看[引导文档](https://getbootstrap.com/docs/4.1/getting-started/introduction/)以了解更多关于内置类和组件的信息。

### 使用 react-bootstrap

现在，我们将使用 react-bootstrap 重新构建我们的主题切换器。我们将使用 create-react-app 命令行工具创建我们的应用程序。确保您的机器上安装了 [create-react-app](https://github.com/facebook/create-react-app) 工具。

使用 create-react-app 创建一个新的 React 应用程序，如下所示:

```
create-react-app react-bootstrap-app 
```

接下来，继续安装依赖项，如下所示:

```
yarn add bootstrap@3 react-bootstrap 
```

`react-bootstrap`当前目标是引导 v3。但是，提供 Bootstrap v4 支持的工作正在积极进行中。

在项目的 src 目录下创建一个名为 ThemeSwitcher.js 的新文件，并将以下内容放入其中。

```
import React, { Component } from 'react';
import { SplitButton, MenuItem } from 'react-bootstrap';

class ThemeSwitcher extends Component {

  state = { theme: null }

  chooseTheme = (theme, evt) => {
    evt.preventDefault();
    if (theme.toLowerCase() === 'reset') { theme = null }
    this.setState({ theme });
  }

  render() {

    const { theme } = this.state;
    const themeClass = theme ? theme.toLowerCase() : 'default';

    const parentContainerStyles = {
      position: 'absolute',
      height: '100%',
      width: '100%',
      display: 'table'
    };

    const subContainerStyles = {
      position: 'relative',
      height: '100%',
      width: '100%',
      display: 'table-cell',
      verticalAlign: 'middle'
    };

    return (
      <div style={parentContainerStyles}>
        <div style={subContainerStyles}>

          <span className={`h1 center-block text-center text-${theme ? themeClass : 'muted'}`} style={{ marginBottom: 25 }}>{theme || 'Default'}</span>

          <div className="center-block text-center">
            <SplitButton bsSize="large" bsStyle={themeClass} title={`${theme || 'Default'} Theme`}>
              <MenuItem eventKey="Primary" onSelect={this.chooseTheme}>Primary Theme</MenuItem>
              <MenuItem eventKey="Danger" onSelect={this.chooseTheme}>Danger Theme</MenuItem>
              <MenuItem eventKey="Success" onSelect={this.chooseTheme}>Success Theme</MenuItem>
              <MenuItem divider />
              <MenuItem eventKey="Reset" onSelect={this.chooseTheme}>Default Theme</MenuItem>
            </SplitButton>
          </div>

        </div>
      </div>
    );

  }

}

export default ThemeSwitcher; 
```

在这里，我们试图使用 react-bootstrap 尽可能多地模拟我们的初始示例。我们从 react-bootstrap 包中导入两个组件，即:SplitButton 和 MenuItem。参见 react-bootstrap [下拉文档](https://react-bootstrap.github.io/components/dropdowns/)了解更多信息。

首先，我们用一个主题属性设置组件的状态，并将其初始化为 null。接下来，我们定义一个 chooseTheme() click 事件处理程序，用于选择主题或重置主题。

因为我们使用的是 Bootstrap 3.3.7，所以我们在 render()方法中创建了一些容器样式来帮助我们实现水平和垂直居中。

注意我们是如何使用 bsSize 属性在 SplitButton 组件上指定按钮大小的。另外，请注意我们是如何将 themeClass 传递给 bsStyle prop 来根据状态的主题动态更改按钮颜色的。

我们将主题名称传递给每个 MenuItem 组件的 eventKey 属性。我们还将 onSelect 处理程序设置为我们前面定义的 this.chooseTheme()。MenuItem 组件将 eventKey 和事件本身传递给 onSelect 处理程序，如下所示:

```
(eventKey: any, event: Object) => any 
```

最后，我们将修改 src/index.js 文件，如下所示:

```
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap/dist/css/bootstrap-theme.min.css';
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import ThemeSwitcher from './ThemeSwitcher';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(<ThemeSwitcher />, document.getElementById('root'));
registerServiceWorker(); 
```

这里，我们首先导入 Bootstrap 的缩小 CSS 文件。我们还导入了 ThemeSwitcher 组件，并将其呈现到 DOM 中。

如果你现在用纱线启动或 npm 启动命令运行程序。您的应用程序应该在端口 3000 上启动，看起来应该如下所示:

[![](img/e6023dbf5ea71cbd19c5edfb769c057a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7fTctbxr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/0%2ALgjq4lRE-HSIPRX8.gif)

<figcaption>react-自举演示</figcaption>

### 使用 reactstrap

这一次，我们将继续使用 reactstrap 重新构建我们的主题切换器。我们将使用 create-react-app 命令行工具创建我们的应用程序。确保您的机器上安装了 [create-react-app](https://github.com/facebook/create-react-app) 工具。

使用 create-react-app 创建一个新的 React 应用程序，如下所示:

```
create-react-app reactstrap-app 
```

接下来，继续安装依赖项，如下所示:

```
yarn add bootstrap reactstrap 
```

在项目的 src 目录下创建一个名为 ThemeSwitcher.js 的新文件，并将以下内容放入其中。

```
import React, { Component } from 'react';
import { Button, ButtonDropdown, DropdownToggle, DropdownMenu, DropdownItem } from 'reactstrap';

class ThemeSwitcher extends Component {

  state = { theme: null, dropdownOpen: false }

  toggleDropdown = () => {
    this.setState({ dropdownOpen: !this.state.dropdownOpen });
  }

  resetTheme = evt => {
    evt.preventDefault();
    this.setState({ theme: null });
  }

  chooseTheme = (theme, evt) => {
    evt.preventDefault();
    this.setState({ theme });
  }

  render() {

    const { theme, dropdownOpen } = this.state;
    const themeClass = theme ? theme.toLowerCase() : 'secondary';

    return (
      <div className="d-flex flex-wrap justify-content-center position-absolute w-100 h-100 align-items-center align-content-center">

        <span className={`h1 mb-4 w-100 text-center text-${themeClass}`}>{theme || 'Default'}</span>

        <ButtonDropdown isOpen={dropdownOpen} toggle={this.toggleDropdown}>
          <Button id="caret" color={themeClass}>{theme || 'Custom'} Theme</Button>
          <DropdownToggle caret size="lg" color={themeClass} />
          <DropdownMenu>
            <DropdownItem onClick={e => this.chooseTheme('Primary', e)}>Primary Theme</DropdownItem>
            <DropdownItem onClick={e => this.chooseTheme('Danger', e)}>Danger Theme</DropdownItem>
            <DropdownItem onClick={e => this.chooseTheme('Success', e)}>Success Theme</DropdownItem>
            <DropdownItem divider />
            <DropdownItem onClick={this.resetTheme}>Default Theme</DropdownItem>
          </DropdownMenu>
        </ButtonDropdown>

      </div>
    );

  }

}

export default ThemeSwitcher; 
```

这里，我们使用 reactstrap 重新构建了最初的示例。我们从 reactstrap 进口了一些组件。参见 reactstrap [按钮下拉文档](https://reactstrap.github.io/components/button-dropdown/)了解更多信息。

首先，我们用两个属性设置组件的状态:

1.  主题属性初始化为空
2.  dropdownOpen 初始化为 false。reactstrap 中的 ButtonDropdown 组件将使用该属性来维护下拉列表的切换状态。

我们还定义了一个 toggleDropdown()方法来切换下拉列表的打开状态。这也将在 ButtonDropdown 组件中使用。

*   *reactstrap 还提供了一个不受控制的组件* *不受控制的 ButtonDropdown，它不需要* *isOpen prop 或* *toggle handler prop 来工作。在大多数情况下，这可以用来代替使用* *ButtonDropdown。*

下拉菜单中的每个项目都是使用 DropdownItem 组件呈现的。注意我们是如何使用 size prop 在 DropdownToggle 组件上指定按钮大小的。还要注意我们如何将 themeClass 传递给 Button 和 DropdownToggle 组件上的 color prop，以便根据状态的主题动态更改按钮的颜色。

我们还像以前一样，使用我们之前定义的 chooseTheme()和 resetTheme()处理程序，在每个 DropdownItem 上设置 onClick 处理程序。

最后，我们将修改 src/index.js 文件，如下所示:

```
import 'bootstrap/dist/css/bootstrap.min.css';
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import ThemeSwitcher from './ThemeSwitcher';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(<ThemeSwitcher />, document.getElementById('root'));
registerServiceWorker(); 
```

这里，我们首先导入引导程序缩小的 CSS 文件。我们还导入了 ThemeSwitcher 组件，并将其呈现到 DOM 中。

如果你现在用纱线启动或 npm 启动命令运行程序。您的应用程序应该在端口 3000 上启动，看起来应该如下所示:

[![](img/db42ccdd89a9c559777dd92719ee6f97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AA_Ga7DT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/0%2AQx3moMpxNkq_pUvL.gif) 

<figcaption>reactstrap 演示</figcaption>

### 搭建一个详细的 app

让我们更进一步，创建一个包含更多细节的应用程序。我们将尝试使用尽可能多的引导类和组件。我们还将使用 reactstrap 来集成 Bootstrap 和 React，因为它支持 Bootstrap 4。

我们将使用 create-react-app 命令行工具创建我们的应用程序。确保您的机器上安装了 [create-react-app](https://github.com/facebook/create-react-app) 工具。这是我们将要创建的内容的截图。

[![](img/1ea954f1fee51cd65ef7e26cf4105db0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W2FnJ-6y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AiiY1zBFuNVjzf3GS.png) 

<figcaption>App 截图</figcaption>

使用 create-react-app 创建一个新的 React 应用程序，如下所示:

```
create-react-app sample-app 
```

接下来，继续安装依赖项，如下所示:

```
yarn add axios bootstrap reactstrap 
```

注意这里我们安装了 [axios](https://github.com/axios/axios) 作为依赖项。 [Axios](https://github.com/axios/axios) 是一个基于 promise 的 HTTP 客户端，用于浏览器和 Node.js。它将使我们能够从[**BaconIpsum JSON API**](https://baconipsum.com/json-api/)获取帖子。

对 src/index.js 文件做一点修改，以包含引导程序缩小的 CSS 文件。它应该类似于下面的代码片段:

```
import 'bootstrap/dist/css/bootstrap.min.css';
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(<App />, document.getElementById('root'));
registerServiceWorker(); 
```

在项目的 src 目录中创建一个名为 components 的新目录。在刚刚创建的组件目录下创建一个新的文件 Header.js，内容如下:

```
import React from 'react';
import logo from '../logo.svg';

import {
  Container, Row, Col, Form, Input, Button, Navbar, Nav,
  NavbarBrand, NavLink, NavItem, UncontrolledDropdown,
  DropdownToggle, DropdownMenu, DropdownItem
} from 'reactstrap';

const AVATAR = 'https://www.gravatar.com/avatar/429e504af19fc3e1cfa5c4326ef3394c?s=240&d=mm&r=pg';

const Header = () => (
  <header>
    <Navbar fixed="top" color="light" light expand="xs" className="border-bottom border-gray bg-white" style={{ height: 80 }}>

      <Container>
        <Row noGutters className="position-relative w-100 align-items-center">

          <Col className="d-none d-lg-flex justify-content-start">
            <Nav className="mrx-auto" navbar>

              <NavItem className="d-flex align-items-center">
                <NavLink className="font-weight-bold" href="/">
                  <img src={AVATAR} alt="avatar" className="img-fluid rounded-circle" style={{ width: 36 }} />
                </NavLink>
              </NavItem>

              <NavItem className="d-flex align-items-center">
                <NavLink className="font-weight-bold" href="/">Home</NavLink>
              </NavItem>

              <NavItem className="d-flex align-items-center">
                <NavLink className="font-weight-bold" href="/">Events</NavLink>
              </NavItem>

              <UncontrolledDropdown className="d-flex align-items-center" nav inNavbar>
                <DropdownToggle className="font-weight-bold" nav caret>Learn</DropdownToggle>
                <DropdownMenu right>
                  <DropdownItem className="font-weight-bold text-secondary text-uppercase" header disabled>Learn React</DropdownItem>
                  <DropdownItem divider />
                  <DropdownItem>Documentation</DropdownItem>
                  <DropdownItem>Tutorials</DropdownItem>
                  <DropdownItem>Courses</DropdownItem>
                </DropdownMenu>
              </UncontrolledDropdown>

            </Nav>
          </Col>

          <Col className="d-flex justify-content-xs-start justify-content-lg-center">
            <NavbarBrand className="d-inline-block p-0" href="/" style={{ width: 80 }}>
              <img src={logo} alt="logo" className="position-relative img-fluid" />
            </NavbarBrand>
          </Col>

          <Col className="d-none d-lg-flex justify-content-end">
            <Form inline>
              <Input type="search" className="mr-3" placeholder="Search React Courses" />
              <Button type="submit" color="info" outline>Search</Button>
            </Form>
          </Col>

        </Row>
      </Container>

    </Navbar>
  </header>
);

export default Header; 
```

我们刚刚在这个代码片段中创建的组件是包含导航菜单的标题组件。接下来，我们将在 components 目录下创建一个名为 SideCard.js 的新文件，其内容如下:

```
import React, { Fragment } from 'react';

import {
  Button, UncontrolledAlert, Card, CardImg, CardBody,
  CardTitle, CardSubtitle, CardText
} from 'reactstrap';

const BANNER = 'https://i.imgur.com/CaKdFMq.jpg';

const SideCard = () => (
  <Fragment>

    <UncontrolledAlert color="danger" className="d-none d-lg-block">
      <strong>Account not activated.</strong>
    </UncontrolledAlert>

    <Card>
      <CardImg top width="100%" src={BANNER} alt="banner" />
      <CardBody>
        <CardTitle className="h3 mb-2 pt-2 font-weight-bold text-secondary">Glad Chinda</CardTitle>
        <CardSubtitle className="text-secondary mb-3 font-weight-light text-uppercase" style={{ fontSize: '0.8rem' }}>Web Developer, Lagos</CardSubtitle>
        <CardText className="text-secondary mb-4" style={{ fontSize: '0.75rem' }}>Full-stack web developer learning new hacks one day at a time. Web technology enthusiast. Hacking stuffs @theflutterwave.</CardText>
        <Button color="success" className="font-weight-bold">View Profile</Button>
      </CardBody>
    </Card>

  </Fragment>
);

export default SideCard; 
```

接下来，在 components 目录中创建一个名为 Post.js 的新文件，并向其中添加以下代码片段:

```
import React, { Component, Fragment } from 'react';
import axios from 'axios';
import { Badge } from 'reactstrap';

class Post extends Component {

  state = { post: null }

  componentDidMount() {
    axios.get('https://baconipsum.com/api/?type=meat-and-filler&paras=4&format=text')
      .then(response => this.setState({ post: response.data }));
  }

  render() {
    return (
      <Fragment>
        { this.state.post && <div className="position-relative">

          <span className="d-block pb-2 mb-0 h6 text-uppercase text-info font-weight-bold">
            Editor's Pick
            <Badge pill color="success" className="text-uppercase px-2 py-1 ml-3 mb-1 align-middle" style={{ fontSize: '0.75rem' }}>New</Badge>
          </span>

          <span className="d-block pb-4 h2 text-dark border-bottom border-gray">Getting Started with React</span>

          <article className="pt-5 text-secondary text-justify" style={{ fontSize: '0.9rem', whiteSpace: 'pre-line' }}>{this.state.post}</article>

        </div> }
      </Fragment>
    );
  }

}

export default Post; 
```

这里我们创建了一个 Post 组件，它在页面上呈现一篇文章。我们用设置为 null 的 post 属性初始化组件的状态。当组件挂载时，我们使用 axios 从[**BaconIpsum JSON API**](https://baconipsum.com/json-api/)中随机获取 4 个段落的 post，并更新状态中的 post 属性。

最后，修改 src/App.js 文件，使其看起来像下面的代码片段:

```
import React, { Fragment } from 'react';
import axios from 'axios';
import { Container, Row, Col } from 'reactstrap';

import Post from './components/Post';
import Header from './components/Header';
import SideCard from './components/SideCard';

const App = () => (
  <Fragment>

    <Header />

    <main className="my-5 py-5">
      <Container className="px-0">
        <Row noGutters className="pt-2 pt-md-5 w-100 px-4 px-xl-0 position-relative">

          <Col xs={{ order: 2 }} md={{ size: 4, order: 1 }} tag="aside" className="pb-5 mb-5 pb-md-0 mb-md-0 mx-auto mx-md-0">
            <SideCard />
          </Col>

          <Col xs={{ order: 1 }} md={{ size: 7, offset: 1 }} tag="section" className="py-5 mb-5 py-md-0 mb-md-0">
            <Post />
          </Col>

        </Row>
      </Container>
    </main>

  </Fragment>
);

export default App; 
```

在这里，我们简单地将标题、边卡和帖子组件包含到 App 组件中。请注意我们如何使用 Bootstrap 提供的几个响应实用程序类来使我们的应用适应不同的屏幕尺寸。

如果你现在用纱线启动或 npm 启动命令运行程序。您的应用程序应该在端口 3000 上启动，并且应该与我们之前看到的屏幕截图一模一样。

### 结论

在本教程中，我们探索了几种不同的方法来将 [Bootstrap](https://getbootstrap.com/) 与我们的 [React](https://reactjs.org/) 应用程序集成在一起。我们还看到了如何使用两个最流行的 React 引导库，即: [**react-bootstrap**](https://react-bootstrap.github.io/) 和 [**reactstrap**](https://reactstrap.github.io/) 。

我们在本教程中只使用了几个引导组件，如提醒、徽章、下拉菜单、导航条、导航、表单、按钮、卡片等。仍然有一些引导组件你可以尝试，比如表格、模态、工具提示、轮播、大屏幕、分页、标签等等。

您可以查看我们在本教程中使用的包的文档，找到它们的更多用法。本教程中所有演示应用的源代码都可以在 [GitHub](https://github.com/gladchinda/react-with-bootstrap-demo) 上找到。

* * *

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[如何在 React](https://blog.logrocket.com/how-to-use-bootstrap-with-react-a354715d1121/) 中使用 Bootstrap 首先出现在[的博客](https://blog.logrocket.com)上。