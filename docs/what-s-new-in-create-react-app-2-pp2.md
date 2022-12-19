# Create React App 2 的新增功能

> 原文：<https://dev.to/bnevilleoneill/what-s-new-in-create-react-app-2-pp2>

[![](../Images/0b45eb6f0ad4b21474b716eab924e83a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nNCEDFhC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMmSekcpfVq3Ckath3wlJ_Q.jpeg)

[Create React App 2.1](https://github.com/facebook/create-react-app/releases/tag/v2.1.0) ，2018 年 10 月底发布，增加了对 TypeScript 的官方支持。这一点，加上 Create React App 第二版带来的所有改进和特性，大大降低了创建和配置 React App 的复杂性。

在本文中，我将介绍 Create React App 2 的以下特性:

*   类型脚本支持
*   服务人员(默认情况下未注册)
*   Sass、CSS 模块和 PostCSS
*   反应片段短语法
*   巴别塔宏
*   自定义代理实现
*   纱线即插即用模式

关于所有特性的完整列表(和一些突破性的变化)，你可以查看关于这个版本的官方帖子和这个项目的[变更日志](https://github.com/facebook/create-react-app/blob/master/CHANGELOG.md#203-october-1-2018)。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

#### 支持打字稿

对于新的应用程序，您只需执行 create-react-app with-typescript 选项:

```
npx create-react-app sample-typescript-app --typescript 
```

如果 npx 没有执行最新的版本(在撰写本文时是 2.1.1)，请指定 create-react-app 的版本:

```
npx create-react-app@2.1.1 sample-typescript-app --typescript 
```

这将安装与 TypeScript 相关的包，并创建一个默认的 tsconfig.json 文件。下面是该命令输出的摘录:

```
Installing react, react-dom, and react-scripts...
+ react-dom@16.6.3
+ react@16.6.3
+ react-scripts@2.1.1
+ @types/react-dom@16.0.9 + @types/react@16.7.3 + @types/jest@23.3.9 + @types/node@10.12.6 + typescript@3.1.6
...
We detected TypeScript in your project (srcApp.test.tsx) and created a tsconfig.json file for you.
Your tsconfig.json has been populated with default values. 
```

然而，文件 src/App.tsx 将与它的 JavaScript 副本相同，例如，如果您向该文件添加一个函数组件，如下所示:

```
//...
const Content = (props) => {
  return <div><h1>{props.title}</h1>{props.text}</div>;
 }

class App extends Component {
  // ...
} 
```

将引发关于 props 参数类型的错误:

[![](../Images/80b64cc1c8d50585078b6cb47ca850c0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KYynpeWt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AD1eYgpP1HejmRs7U)

因为现在，你必须使用 [React 声明一个类型定义。证监会](https://www.sohamkamani.com/blog/react/2018-08-22-using-react-with-typescript/) :

```
interface ContentProps {
  title: string,
  text: string,
 }

const Content: React.SFC<ContentProps> = (props) => {
  return <div><h1>{props.title}</h1>{props.text}</div>;
} 
```

如果您的 IDE 支持它(下图中的 Visual Studio 代码)，它会向您显示组件属性的类型，还会捕捉类型错误:

[![](../Images/76ec6afb9c5af9368e24d1dc54a2817b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AVdlpOrk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/556/0%2AbMmEwUtCY8FhwCR3)

另一方面，要将 TypeScript 添加到现有的 Create React App 应用程序中，首先，手动安装与 TypeScript 相关的包:

```
npm install --save typescript @types/node @types/react @types/react-dom @types/jest 
```

接下来，将文件的扩展名从`*.js`更改为`*.tsx`，并重启应用程序(如果它正在运行的话)。

#### 服务人员默认不注册

Create React App 创建的项目结构保持不变。

唯一的变化与服务人员有关。

首先，文件 src/registerServiceWorker.js 被重命名为 src/serviceWorker.js，现在，在该文件内部，一个 config 对象被传递给函数 registerValidSW 以启用回调 onUpdate 和 onSuccess:

[![](../Images/1c0a1b3f70ac05c1cd01d74bab968a75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hWghq3x---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A0LAwUAe3CT2yNC64)

第二个变化是在 src/index.js 中，默认取消注册服务工作者:

```
// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: http://bit.ly/CRA-PWA
serviceWorker.unregister(); 
```

你可以在这里了解更多关于服务人员和渐进式网络应用的信息[。](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

#### Sass、CSS 模块和 PostCSS

Create React App 2 为您提供了更多选项来设计您的应用程序，而无需复杂的配置、弹出应用程序或使用 [react-app-rewired](https://github.com/timarney/react-app-rewired) 。

第一个选项是 [Sass](https://sass-lang.com/) 。要使用它，首先安装[节点-萨斯](https://github.com/sass/node-sass) :

```
npm install --save node-sass 
```

现在您可以创建一个 Sass 文件(src/Message.scss)。例如:

```
$theme-color: green; 
```

。my-message {
color:$ theme-color；
-

```
And use it in a component (src/Message.js) this way: 
```

jsx
import React 来自‘React’
import’。/Message . scss '
const Message = props =>{
return

*   {儿童}*   } export default Message;

    ```
    On the other hand, with [CSS Modules](https://www.javascriptstuff.com/what-are-css-modules/), you don’t have to install more dependencies, you just have to follow the [name].module.css file naming convention.

    For example, if you have a Message.js component (shown in the previous example), you can define its styles in the file Message.module.css (remember that with CSS Modules you can [compose](https://bambielli.com/til/2017-08-11-css-modules-composes/) classes): 
    ```

    jsx
    。我的主题{
    颜色:绿色；
    }
    。我的消息{
    组成:我的主题；
    字体大小:20px
    }

    ```
    And in the component, use the styles like this: 
    ```

    从'导入样式。/Message.module.css'
    //...

    *   {儿童}

```
This way, at runtime, a CSS class with a semi-random name will be generated to locally scope those styles to the component: 
```

jsx

*   ```
     Also, [postcss-flexbugs-fixes](https://github.com/luisrudge/postcss-flexbugs-fixes) and [postcss-preset-env](https://github.com/csstools/postcss-preset-env) ([supporting](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/config/webpack.config.js#L104) only [stage3+ features](https://preset-env.cssdb.org/features#stage-3)) are included in Create React App 2, which means vendor prefixes are added automatically and new CSS features for older browsers are polyfilled. You can learn more about it [here](https://facebook.github.io/create-react-app/docs/post-processing-css).

    ### React fragments short syntax

    It’s been a while since React 16.2 added support for [fragments](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html) to return multiple children from a component’s render method: 
    ```

    jsx
    render(){
    return(

    # 我的消息

    消息 1

    消息 2

    ); }

    ```
    However, as the JSX Fragment Syntax (or short syntax): 
    ```

    jsx
    render(){
    return(
    T5>T4】

    # 我的消息

    消息 1

    消息 2

    </> ); }

    ```
    It’s only supported by Babel 7, you can use it now that Create React App 2 uses this version of Babel.

    However, remember that `<></>` is just syntax sugar for `<React.Fragment>` but without the option to use keys or other attributes.

    ### Babel macros

    In the context of Create React App 2, Babel macros allow you to use a special type of Babel plugins without any configuration.

    Let me explain.

    Babel plugins allow you to manipulate and transform code at build time. For example, there’s a [plugin](https://babeljs.io/docs/en/babel-plugin-transform-arrow-functions) that transforms arrow functions: 
    ```

    jsx
    const a =(b)=>b；

    ```
     To regular JavaScript functions: 
    ```

    jsx
    const a =函数(b) {
    返回 b；
    }；

    ```
     However, one problem with Babel plugins is that you have to configure each one you want to use. Usually, this is done in .babel.rc, but when using Create React App, you don’t have access to this file unless you eject the application.

    Luckily, there is [babel-plugin-macros](https://www.npmjs.com/package/babel-plugin-macros), a plugin that defines a standard interface for…macros (i.e. plugins that perform build-time transformations).

    This way, you only have to add this plugin to your project (which is what Create React App does) to use any number of macros you want.

    And since macros are processed at build-time and not required at runtime, they should be specified as devDependencies.

    You can search [npm for the keyword babel-plugin-macros](https://www.npmjs.com/search?q=keywords:babel-plugin-macros) to find macros.

    Take, for example, the Babel plugin/macro [tagged-translations](https://github.com/vinhlh/tagged-translations), which translates text at build-time.

    Just add this library to a Create React App project with: 
    ```

    jsx
    npm 安装-保存-开发标记-翻译

    ```
     Add the file translations/default.json at the root of the project with a translation like the following: 
    ```

    jsx
    {
    《你好世界》:“你好世界”
    }

    ```
     And use it as a macro in your components (not as a plugin, [there’s a difference](https://github.com/kentcdodds/babel-plugin-macros#whats-the-difference-between-babel-plugins-and-macros)): 
    ```

    jsx
    从“标记翻译/宏”导入 t

    类 App 扩展组件{
    render() {
    return (

    {t`Hello world`} ); } }

    ```
    As you can see in the following image, the translation happens when the bundle is created at build-time:

    ![](https://cdn-images-1.medium.com/max/954/0*TdByJTaiKWD2jFw2)

    ### Custom proxy implementation

    In the first version of Create React App, when making an API request you either had the option of hard-coding the complete URL of the request like this: 
    ```

    jsx
    fetch([http://localhost:3001/messages '](http://localhost:3001/messages'))
    。然后(res = > {
    //...
    })

    ```
    Or add a proxy field to the package.json file: 
    ```

    jsx
    ' proxy ':'[http://localhost:3001/](http://localhost:3001/)'

    ```
     To just use the path of the resource in the fetch call: 
    ```

    jsx
    fetch('/messages ')
    。然后(res = > {
    //...
    })

    ```
    With Create React App 2, in addition to the methods shown above, you can configure a custom proxy by installing [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware): 
    ```

    jsx
    npm 安装-保存 http-代理-中间件

    ```
     And creating the file src/setupProxy.js to configure the proxy: 
    ```

    jsx
    const proxy = require(' http-代理-中间件')；
    module . exports = function(app){
    //...
    }；

    ```
    This file will be imported automatically when the application starts, and it gives you access to an [Express](https://expressjs.com/) instance to do something like this: 
    ```

    jsx
    const proxy = require(' http-代理-中间件')；
    module . exports = function(app){
    app . use(proxy('/API '，{ target:'[http://localhost:3001/](http://localhost:3001/)' })；
    app.use(proxy('/public '，{ target:'[http://localhost:3002/](http://localhost:3002/)' })；
    }；

    ```
    #### Yarn Plug’n’Play mode

    Yarn Plug’n’Play mode allows your application to work without a `node_modules` directory.

    This way, the app dependencies are loaded from Yarn’s cache, rather than requiring copying them into the `node_modules` directory during the installation step. This has the added benefit of faster app creations.

    To use this feature, you’ll need [Yarn 1.12+](https://yarnpkg.com/lang/en/docs/install/), Node.js 8.9+, and be sure to create your React application with the option --use-pnp: 
    ```

    jsx
    npx create-react-app light-app-use-PNP

    ```
     If the command executed successfully, in the file package.json, you will find an additional configuration option: 
    ```

    jsx
    {
    ...
    “安装配置”:{
    “PNP”:真
    }，
    ...
    }

    ```
     Also, you’ll get a .pnp directory with links to directories of Yarn’s cache and the file .pnp.js, which validates dependencies and provides the ability to search for them from the global cache instead of the `node_modules` directory.

    This is an experimental feature(at the time of this writing) so it might not work in all situations (for example, I didn’t work on my Windows 10 machine) and with tools that work with the `node_modules` directory directly.

    ### Conclusion

    Create React App 2 adds more options to ease the creation and configuration of React applications. Personally, the three new features I find most helpful are TypeScript, Saas and Babel macros.

    However, I have to say that for an existing application, if you don’t need these new features or if you have a working configuration using [react-app-rewired](https://github.com/timarney/react-app-rewired) or [craco](https://github.com/sharegate/craco), it’s probably not worth upgrading.

    Even the Create React App team [advise this](https://reactjs.org/blog/2018/10/01/create-react-app-v2.html):

    > Don’t feel pressured to upgrade anything. If you’re satisfied with the current feature set, its performance, and reliability, you can keep using the version you’re currently at! It might also be a good idea to let the 2.0 release stabilize a little bit before switching to it in production.

    But for new applications, you have many new features at your disposal.

    Happy hacking!
    * * *
    ### Plug: [LogRocket](https://logrocket.com/signup/), a DVR for web apps

    [![](https://cdn-images-1.medium.com/max/1024/1*s_rMyo6NbrAsP-XtvBaXFg.png)](https://logrocket.com/signup/)<figcaption><a href="https://logrocket.com/signup/">https://logrocket.com/signup/</a></figcaption>

    [LogRocket](https://logrocket.com/signup/) is a frontend logging tool that lets you replay problems as if they happened in your own browser. Instead of guessing why errors happen, or asking users for screenshots and log dumps, LogRocket lets you replay the session to quickly understand what went wrong. It works perfectly with any app, regardless of framework, and has plugins to log additional context from Redux, Vuex, and @ngrx/store.

    In addition to logging Redux actions and state, LogRocket records console logs, JavaScript errors, stacktraces, network requests/responses with headers + bodies, browser metadata, and custom logs. It also instruments the DOM to record the HTML and CSS on the page, recreating pixel-perfect videos of even the most complex single page apps.

    [Try it for free.](https://logrocket.com/signup/)
    * * *
    The post [What's new in Create React App 2](https://blog.logrocket.com/whats-new-in-create-react-app-2-fe8f4f448c75/) appeared first on [LogRocket Blog](https://blog.logrocket.com). 
    ```