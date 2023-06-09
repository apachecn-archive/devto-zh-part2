# 带悬念的反应中的异步渲染

> 原文：<https://dev.to/bnevilleoneill/async-rendering-in-react-with-suspense-4a13>

### 异步渲染在反应中带有悬念

[![](img/62ad4297eb31431f4841486106feae8a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TDiCBDYp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ay6C4nSvy2Woe0m7bWEn4BA.png)

**什么是悬念？**

悬念是 React 的一项新功能，最近在冰岛举行的 [JSConf 会议上宣布。它旨在帮助处理分别与 CPU 能力和数据获取有关的异步操作。](https://2018.jsconf.is/)

暂记允许您延迟应用程序树的部分呈现，直到满足某些条件(例如，从端点或资源加载数据)。

在本文中，我们将探索悬念，看看这个特性将对 React 应用程序的构建方式产生什么样的潜在影响。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

**为什么要悬疑？**

您很有可能遇到过利用加载图标作为正在获取数据的指示器的 spa。这是一种常用方法，用于确保从外部来源获取数据的应用程序具有良好的 UX。您所要做的就是检查数据是否已经成功获取，如果没有，就显示一个微调器。

但是，当数据获取过程变得复杂时，这可能无法扩展:

*   当父组件和子组件都有加载状态时
*   当您需要一个组件在其他(子)组件加载后才加载时

使悬念起作用的关键模块是 createFetcher 函数。它在 npm 上作为[简单缓存提供者](https://www.npmjs.com/package/simple-cache-provider)提供，其工作原理如下:

*   在 render()方法中，从缓存中读取一个值
*   如果该值已经被缓存，渲染会像正常一样继续
*   如果该值尚未缓存，缓存将引发错误
*   当承诺解决时，反应从它停止的地方继续

```
import { createResource } from 'simple-cache-provider';

const someFetcher = createResource(async () => {  
  const res = await fetch(`https://api.github.com/search/users?q=yomete`);
  return await res.json();
}); 

export default someFetcher; 
```

我们使用来自[简单缓存提供者](https://www.npmjs.com/package/simple-cache-provider)包的 createResource 创建一个获取器函数。

> 注意:简单缓存提供者仍然是一个实验性的特性，不能在产品应用中使用。

当启动 createResource 时，传递一个函数，该函数将返回一个承诺。如果承诺解决了，React 继续并呈现结果，否则，抛出一个错误。

然后，可以在呈现函数中使用该函数来显示结果。

我们来看一个悬疑动作的例子。

**悬念演示**

演示的代码库可以在 [GitHub](https://github.com/yomete/react-suspense-demo) 上访问，现场演示可以在[这里](https://build-uzpsujvyrl.now.sh/)访问。

我们将使用 [create-react-app 包](https://github.com/facebook/create-react-app)创建一个新的 react 项目，并做一些修改。在你的终端上运行下面的命令来生成一个 React 应用:

```
npx create-react-app react-suspense 
```

这将创建一个名为 react-悬念的文件夹，其中包含 React 应用程序。现在，让我们进行前面提到的修改。为了利用 simple-cache-provider 等实验性特性，package.json 文件中的 React 版本需要升级到 alpha 版本。

因此，您的 package.json 文件(dependencies 对象)应该用下面的代码片段进行更新:

```
"react": "16.4.0-alpha.0911da3", 
```

```
"react-dom": "16.4.0-alpha.0911da3", 
```

上面显示的 alpha 版本是我们进行测试所需的 React 版本。运行 npm install 命令来更新所有依赖项。

让我们用下面的终端命令安装[简单缓存提供者](https://www.npmjs.com/package/simple-cache-provider)包:

```
npm install simple-cache-provider 
```

安装完所有的依赖项后，让我们继续编写代码来演示悬念。

这里的想法是从 [TV Maze API](https://www.tvmaze.com/api) 获得一个节目列表，然后使用悬念显示结果。

首先，我们需要在 App.js 文件中进行一些导入。第一个是在 App.js 文件中导入 createResource 函数。这将从简单缓存提供者:
导入

```
import { createResource } from 'simple-cache-provider'; 
```

接下来，我们将导入一个尚未创建的组件，名为 withCache。这是一个高阶组件，有助于悬念渲染:

```
import { withCache } from './components/withCache'; 
```

创建一个文件夹，将其命名为 components，在其中创建一个. withCache.js 文件，并使用下面的代码进行编辑:

```
import React from 'react';
import { SimpleCache } from 'simple-cache-provider';

export function withCache(Component) {
  return props => (
    <SimpleCache.Consumer>
      {cache => <Component cache={cache} {...props} />}
    </SimpleCache.Consumer>
  );
} 
```

withCache 组件是与 [SimpleCache 连接的高阶组件。消费者](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js)并将缓存放在包装的组件上。

接下来，我们将导航回 App.js 并创建 createResource 函数来获取数据:

```
const sleep = ms => new Promise(r => setTimeout(() => r(), ms));

const readShows = createResource(async function fetchNews() {
  await sleep(3000);
  const res = await fetch(`http://api.tvmaze.com/search/shows?q=suits`);
  return await res.json();
}); 
```

下面是 createResource 函数正在做的事情:

1.  它创建一个资源获取器(createResource())，该获取器由一组参数调用，在本例中，是一个异步函数，仅在睡眠函数中“等待”指定的持续时间后，获取标题为套装的节目列表
2.  它返回 API 调用的结果

需要注意的是，sleep 函数仅用于模拟本例中较长的 API 调用。

创建 createResource 函数后，我们需要从上面的异步函数中获取结果，然后构建视图来显示结果。在 App.js 文件中，继续添加下面的代码块:

```
const Movies = withCache( (props) => {

  return (
    <React.Fragment>
      <div className="column is-4">
        <div className="movie">
          <div className="movie__left">
            <img src />
          </div>
          <div className="movie__right">
            <div className="movie__right__title">Name: </div>
            <div className="movie__right__subtitle">Score: </div>
            <div className="movie__right__subtitle">Status: </div>
            <div className="movie__right__subtitle">Network: </div>
            <a href target="_blank" className="movie__right__subtitle">Link</a>
          </div>
        </div>
      </div>
    </React.Fragment>
  )
}); 
```

在上面的代码中，创建了一个[无状态组件](https://reactjs.org/docs/components-and-props.html)，并将其包装在 withCache 高阶组件中。它返回必要的 HTML 来构建显示 API 结果所需的视图。

此外，[布尔玛](https://bulma.io/) CSS 框架被用来帮助造型。这可以通过将下面的代码行添加到 index.html 来添加到项目中:

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.6.2/css/bulma.min.css"> 
```

下一步是从 createResource()函数中实际读取数据，然后将其发送到 Movies 组件中。

在 Movie.js 组件中，就在 return 函数之前，添加下面的代码行:

```
const result = readShows(props.cache); 
```

这里我们使用的是 readShows(props_。cache_)，它要么解析承诺值，要么抛出一个错误。因为 readShows 是 createResource 函数，所以它需要一个 cache 参数，在本例中是 props.cache。高速缓存正作为一个属性从 withCache 特别传递。

API 调用的结果存储在 result 变量中。获取 API 结果后，我们现在可以用它来填充视图:

```
const Movies = withCache( (props) => {

  const result = readShows(props.cache);

  return (
    <React.Fragment>
      {result &&
          result.length &&
            result.map(item => (
              <div className="column is-4">
                <div className="movie">
                  <div className="movie__left">
                    <img src={item.show.image.original} />
                  </div>
                  <div className="movie__right">
                    <div className="movie__right__title">{item.show.name}</div>
                    <div className="movie__right__subtitle">Score: {item.show.rating.average}</div>
                    <div className="movie__right__subtitle">Status: {item.show.status}</div>
                    <div className="movie__right__subtitle">Network: {item.show.network ? item.show.network.name : 'N/A'}</div>
                    <a href={item.show.url} target="_blank" className="movie__right__subtitle">Link</a>
                  </div>
                </div>
              </div>
            ))
        }
    </React.Fragment>
  )
}); 
```

还记得我们上面提到的，通过推迟渲染应用程序树的一部分，直到获取了一些数据或资源，悬念有助于异步渲染。这非常重要，因为它可以用来显示一些加载消息，作为对在屏幕上等待数据的用户的反馈。

让我们继续在我们的应用程序中实现它:

```
const Placeholder = ({ delayMs, fallback, children }) => {
  return (
    <Timeout ms={delayMs}>
      {didExpire => {
        return didExpire ? fallback : children;
      }}
    </Timeout>
  );
} 
```

上述组件接受以下内容:

*   ms prop，表示我们希望看到回退内容的时间。这将作为 delayMS 传递给占位符组件
*   回退是获取数据时显示的加载状态
*   应该是“子功能”或“渲染道具”功能的子功能；将使用一个参数调用该函数，该参数指示指定的时间是否已经过去

我们使用占位符组件来捕获获取器的抛出，并知道正在加载的数据的状态。

综上所述，您可以使用下面的代码块编辑应用组件:

```
export default class App extends React.Component {

  render() {

    return (
      <React.Fragment>        
        <div className="App">
          <header className="App-header">
            <h1 className="App-title">React Suspense Demo</h1>
          </header>

          <div className="container">
          <div className="columns is-multiline">
              <Placeholder delayMs={1000} fallback={<div>Loading</div>}>
                <Movies />
              </Placeholder>
            </div>
          </div>
        </div>
      </React.Fragment>
    );
  }
} 
```

如上所述，占位符组件是电影组件的父组件。占位符组件上的后备属性被发送到一个漂亮而简单的加载文本。

好了，你可以用 npm start 命令启动应用程序，你应该会看到悬念。

#### **结论**

通过暂停，您可以在加载异步数据时暂停组件渲染。您可以暂停任何状态更新，直到数据准备就绪，并且您可以向树深处的任何组件添加异步加载，而无需通过您的应用程序检查所有属性和状态并提升逻辑。

这为快速网络带来了即时和流畅的用户界面，并为慢速网络带来了有意设计的加载状态，而不是一般的加载状态。

值得注意的是，这些 API 仍处于实验模式，不适合生产。最好始终与 React 团队保持一致，了解任何 API 变化和悬念功能的更新。

上面演示的代码库可以在 [GitHub](https://github.com/yomete/react-suspense-demo) 上访问，现场演示可以在[这里](https://build-uzpsujvyrl.now.sh/)访问。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

《悬疑反应》中的异步渲染帖子最先出现在[博客](https://blog.logrocket.com)上。