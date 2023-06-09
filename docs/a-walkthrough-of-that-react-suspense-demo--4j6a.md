# 反应悬念演示的演练

> 原文：<https://dev.to/swyx/a-walkthrough-of-that-react-suspense-demo--4j6a>

## 2018 年 11 月起更新:下面的 API 已经过时，查看 https://github.com/sw-yx/fresh-concurrent-react获取最新指南！

* * *

[![the suspense is real!](img/d5a7cdc53d7c26c627bd2afd32273929.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---zw0tEHc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/3624098/36625698-50323b20-18d9-11e8-87bf-de1133b023ec.gif)

* * *

底线提前:在这个 300 多行电影搜索演示的演练中，我们学习了 React 悬念 API 的各个方面:

*   `simple-cache-provider.SimpleCache` -在`createContext`中放置一个`cache`
*   `simple-cache-provider.createResource` -它 1)为您的数据获取一个**承诺**，2)输出一个函数，该函数获取一个`cache`和一个 arg 来调用您的**承诺**(也称为**挂起器**
*   如何使用`ReactDOM.unstable_deferredUpdates`将更新委托给较低的优先级
*   `createResource`如何通过**抛出承诺**异步加载数据(！！！)
*   仅仅给你一个布尔值，用于在孩子和回退之间切换
*   如何用`createResource`做**异步镜像加载**(！！！)

如果你想学习反应悬念，请继续读下去！

* * *

JSConf 冰岛上的 Async React 演示不负众望:时间切片和 React 悬念即将推出！(详见[官方博文](https://reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html)、[视频](https://www.youtube.com/watch?v=v6iR3Zk4oDY)、 [HN 讨论](https://news.ycombinator.com/item?id=16492973))。观看[视频](https://www.youtube.com/watch?v=v6iR3Zk4oDY)是本文其余部分的先决条件！

Dev Twitter 上有很多杰出的开发人员在研究 Async React 对于从[React-loaded](https://twitter.com/jamiebuilds/status/969166935836344321?s=21)到 [React Router](https://twitter.com/ryanflorence/status/969235637244018688?s=21) 到 [Redux](https://twitter.com/wsokra/status/969284466043703297?s=21) 的各种应用的意义，而一直保持警惕的 Apollo 团队甚至推出了用 Async React 和 Apollo 构建的演示应用[！](https://twitter.com/apollographql/status/969313298440126466)

不用说，人们很兴奋(阅读全文，非常有趣):

> ![Jani Eväkallio profile image](img/d63f9a28599b8092cb42764056f21161.png)Jani evkallio[@ jevakallio](https://dev.to/jevakallio)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)再过一个小时 [@dan_abramov](https://twitter.com/dan_abramov) 将登上 [@jsconfis](https://twitter.com/jsconfis) 的舞台，进行大会历史上最被炒作的演讲之一。你知道这意味着什么吗？
> 
> 是的，现在是直播热拿推特跟帖的时间👇2018 年 03 月 01 日上午 09:01[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=969135585909792768)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=969135585909792768)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=969135585909792768)

而 [spectrum.chat](https://spectrum.chat/?t=586129b0-845c-4025-bd0e-f4a2200a971b) 的人都非常兴奋:

[![image](img/e72459eae72a87d751b1cb8ebba41d0c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LED10XuU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/6764957/36888678-88ba5d66-1dc4-11e8-9813-7f6c034c07a2.png)

令人兴奋的东西。这是一个长达数年的过程的高潮，从 2014 年 Jordan Walke 的这条推文开始，到 [Lin Clark 对 React Fiber 的介绍](https://www.youtube.com/watch?v=ZCuYPiUIONs)(你可以看到时间切片几乎在一年前就开始工作了)，到[2017 年 9 月 React Fiber 的实际发布](https://code.facebook.com/posts/1716776591680069/react-16-a-look-inside-an-api-compatible-rewrite-of-our-frontend-ui-library/)，到 [Sebastian 在 2017 年 12 月推出吊带 API](https://twitter.com/acdlite/status/969172311067713537) 。

但是如果你只是一个普通的 React——像我一样的 Joe，你会觉得有点落后(这是应该的——这是高级的东西，甚至还不是最终版本，所以如果你是一个 React 新手，停止阅读并学习 React)。

我通过实践来学习，我真的不擅长通过谈论来理解抽象的东西。

幸运的是，安德鲁·克拉克在 CodeSandbox 上发布了电影搜索演示的版本。所以我想我应该只浏览这一点，因为这实际上是我们所有的演示使用代码(除了 Apollo 演示，它是这部电影搜索演示的一个分支)，我不想浏览整个源代码(我现在也碰巧真的生病了，但学习让我很开心:)。

最后，一些免责声明，因为人们有时很容易被触发:

1.  我是最近的训练营毕业生。你不是在读某个思想领袖的预言。我只是一个在公共场合学习的人。
2.  这种 API 非常不稳定，并且容易发生变化。所以忘记细节，只考虑这些概念对你是否有意义。
3.  如果你是一个反应新手，你根本不需要知道这些。这些都不需要出现在任何 React 初学者课程中。我会把这个放在你学习后的 Redux 中，并在学习完 [React 上下文 API](https://reactjs.org/docs/context.html) 后

但是学习是有趣的！事不宜迟:

# 潜入 React 悬念

当你阅读这篇文章的时候，请让[演示](https://codesandbox.io/s/5zk7x551vk)在另一个屏幕中打开，这样会更有意义。

* * *

再一次提醒那些略读的人:

# [嘿！你！在您继续阅读之前，请打开演示！](https://codesandbox.io/s/5zk7x551vk)

* * *

## 遇见`simple-cache-provider.SimpleCache`

应用程序的大部分包含在`index.js`中，所以这是我们开始的地方。我喜欢从上到下深入到树中，这在代码中意味着你从底部向上阅读。在第 303 行，我们看到顶部的容器用`withCache` HOC 包装。这是在`withCache.js` :
中定义的

```
import React from 'react';
import {SimpleCache} from 'simple-cache-provider';

export default function withCache(Component) {
  return props => (
    <SimpleCache.Consumer>
      {cache => <Component cache={cache} {...props} />}
    </SimpleCache.Consumer>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们看到第二个 React API 采用了子渲染道具(参见[肯特·多兹对第一个](https://dev.to/kentcdodds/reacts--new-context-api-dpi)的总结)，它只是为传递给它的任何组件提供了一个`cache`道具。[简单缓存提供者](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js)的源代码只有不到 300 行流类型代码，你可以看到它在幕后使用了 [createContext](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L297) 。你可能听说过很多关于“抛出模式”的大惊小怪，但是这在`simple-cache-provider`中已经被很好地抽象出来了，你实际上从来不需要在你自己的代码中使用它。

因为它真的很酷，你可以在第 187 行的[中检查它，在那里承诺被抛出，然后在第 128](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L187) 行的[中调用`load`函数。我们将进一步探讨这个问题。](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L128)

## 渲染中的副作用

电影搜索演示的主体在`MoviesImpl`组件中:

```
class MoviesImpl extends React.Component {
  state = {
    query: '',
    activeResult: null,
  };
  onQueryUpdate = query => this.setState({query});
  onActiveResultUpdate = activeResult => this.setState({activeResult});
  clearActiveResult = () => this.setState({activeResult: null});
  render() {
    const cache = this.props.cache;
    const state = this.state;
    return (
      <AsyncValue value={state} defaultValue={{query: '', activeResult: null}}>
      /*just renders more JSX here */
      </AsyncValue>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先要注意的是`render`之外没有副作用。停下来想一想你通常如何在 React 组件中产生副作用——要么用生命周期方法如`componentDidMount`或`componentDidUpdate`产生副作用，要么用你的事件处理程序如上面的`onQueryUpdate`和`onActiveResultUpdate`产生副作用。当你在输入框中输入查询时，这个应用程序是如何更新的？

这就是事情开始变得奇怪的地方。答案在 AsyncValue 组件中。

## 满足 react DOM . unstable _ deferredUpdates

答案是 42，和所有的事情一样。具体来说，向上滚动到第 42 行，找到`AsyncValue` :
的源代码

```
class AsyncValue extends React.Component {
  state = {asyncValue: this.props.defaultValue};
  componentDidMount() {
    ReactDOM.unstable_deferredUpdates(() => {
      this.setState((state, props) => ({asyncValue: props.value}));
    });
  }
  componentDidUpdate() {
    if (this.props.value !== this.state.asyncValue) {
      ReactDOM.unstable_deferredUpdates(() => {
        this.setState((state, props) => ({asyncValue: props.value}));
      });
    }
  }
  render() {
    return this.props.children(this.state.asyncValue);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`ReactDOM.unstable_deferredUpdates`是一个没有文档记录的 API，但它不是新的，可以追溯到 2017 年 4 月[(以及](https://twitter.com/koba04/status/854926955463950337?lang=en) [unstable_AsyncComponent](https://github.com/koba04/react-fiber-resources) )。我无知的猜测是，这将把`asyncValue`(即`query`和`activeResult`)中的任何东西作为比 UI 更新优先级更低的更新。

## 跳过主细节、标题和搜索

太好了！回到解析`AsyncValue`的内部结构。

```
 <AsyncValue value={state} defaultValue={{query: '', activeResult: null}}>
        {asyncState => (
          <MasterDetail
            header={<Header />} // just a string: 'Movie search'
            search={ // just an input box, we will ignore
            }
            results={ // uses <Results />
            }
            details={ // uses <Details />
            }
            showDetails={asyncState.activeResult !== null}
          />
        )}
      </AsyncValue> 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么太大的争议，我们这里有一个带有四个渲染道具的`MasterDetail`组件(哟，我听说你喜欢渲染道具...).`MasterDetail`唯一的工作是 CSS-in-JS，所以我们暂时跳过它。`Header`只是一个字符串，`Search`只是一个输入框，所以我们也可以跳过这些。所以我们关心的剩余组件是`Results`和`Details`。

## 深挖`simple-cache-provider.createResource`

原来两者引擎盖下用的都是差不多的东西。这里是第 184 行的`Results`:

```
function Results({query, cache, onActiveResultUpdate, activeResult}) {
  if (query.trim() === '') {
    return 'Search for something';
  }
  const {results} = readMovieSearchResults(cache, query);
  return (
    <div css={{display: 'flex', flexDirection: 'column'}}>
       /* some stuff here */
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

关键位为`readMovieSearchResults`，定义如下:

```
import {createResource} from 'simple-cache-provider';

// lower down...

async function searchMovies(query) {
  const response = await fetch(
    `${TMDB_API_PATH}/search/movie?api_key=${TMDB_API_KEY}&query=${query}&include_adult=false`,
  );
  return await response.json();
}

const readMovieSearchResults = createResource(searchMovies); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`Results`组件仍然在整个应用程序的“渲染”部分。我们正在将`searchMovies`承诺传递给新的`createResource` API，它在`simple-cache-provider` [源](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L251)中

现在 createResource 使用了一些我不完全理解的黑魔法，对演示来说也不是绝对必要的，但是请原谅我。粗略的过程从

*   [创建第 251 行定义的资源](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L251)
*   [第 268 行调用 cache . read](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L268)
*   [第 175 行定义的 cache . read](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L175)
*   由于缓存状态为空，[在第 187 行](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L187)抛出挂起器！！！
*   我们有一个承诺！我们在哪里抓住它！
*   我没有。该死的。线索。哪里都没有`catch`！(来自未来的更新: [Andrew 证实](https://twitter.com/acdlite/status/969488267073630208)这是 React 的特别版本的一部分，他们把它放在一起)
*   在某一点上，承诺冒泡到`createCache`(我们用`SimpleCache`声明它一直在顶层)并且`load`在缓存上被调用。我怎么知道这个？[128 线是整个 app](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L128) 中唯一的`.then`。
*   从这里开始，就变得容易了。高速缓存处于`Resolved`或`Rejected`状态。如果`Resolved`，则[记录值](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L192)被返回，并作为新的`activeResult`出现在`AsyncValue`中，重新呈现整个事件。

这种迂回曲折的方法是 React 悬念的核心创新，你可以告诉我这只是有点超出我现在的理解水平。但这就是你在渲染中获得副作用的方式(不会导致无限循环)。

**这是关键的洞察力:“悬念”是在上面的代码示例中同步使用`readMovieSearchResults(cache, query)`的地方。如果`cache`不包含您的`query`的结果(使用哈希作为`Map`存储在内部)，它会“暂停”渲染并抛出承诺。**

阿波罗和其他人将有替代的缓存实现。

哎呀，那是粗糙的！如果我做错了什么，请在评论中告诉我。我也在学。

所以(大部分)已经完成了。上`Details`！

## 细节决定成败

实际上，`Details`只是`MovieInfo`周围的一层薄薄的包装，T1 是在第 227 行定义的:

```
function MovieInfo({movie, cache, clearActiveResult}) {
  const fullResult = readMovie(cache, movie.id);
  return (
    <Fragment>
      <FullPoster cache={cache} movie={movie} />
      <h2>{movie.title}</h2>
      <div>{movie.overview}</div>
    </Fragment>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

`readMovie`是一个类似于`readMovieSearchResults`的缓存调用，它只是用不同的 URL 调用新的`createResource`到`fetch`。我想强调的是`FullPoster` :

```
function FullPoster({cache, movie}) {
  const path = movie.poster_path;
  if (path === null) {
    return null;
  }
  const config = readConfig(cache);
  const size = config.images.poster_sizes[2];
  const baseURL =
    document.location.protocol === 'https:'
      ? config.images.secure_base_url
      : config.images.base_url;
  const width = size.replace(/\w/, '');
  const src = `${baseURL}/${size}/${movie.poster_path}`;
  return (
    <Timeout ms={2000}>
      <Img width={width} src={src} />
    </Timeout>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一堆新的事情要处理。又是一个缓存调用(看看我们是如何在渲染需要时随意调用这些调用的？)，然后在我们结束使用`Timeout`和`Img`组件之前，我们有一些正常的变量按摩。

## 介绍`React.Timeout`

这里是`Timeout.js` :

```
import React, {Fragment} from 'react';

function Timeout({ms, fallback, children}) {
  return (
    <React.Timeout ms={ms}>
      {didTimeout => (
        <Fragment>
          <span hidden={didTimeout}>{children}</span>
          {didTimeout ? fallback : null}
        </Fragment>
      )}
    </React.Timeout>
  );
}

export default Timeout; 
```

Enter fullscreen mode Exit fullscreen mode

是的，这是新的([这里的 PR 添加它](https://github.com/facebook/react/pull/12279/files#diff-50d8f0a9fb4af9baa0c2d4b8905567a7)，它与一堆其他的 React Fiber 代码混合在一起，所以风险自负)。但是它很直观:输入一个`ms`属性，然后它控制一个布尔值`didTimeout`，如果为真，则隐藏`children`并显示`fallback`，如果为假，则显示`children`并隐藏`fallback`。第三个使用渲染道具的 React API，适用于所有人！

突击测验:为什么这个子/回退行为使用`<span hidden>`而不是将整个事情封装在`{didTimeout ? fallback : children}`中，并且根本没有`<span>`标签？有趣的事情，如果你以前没有考虑过(如果你不确定，请在评论中回复！)

谈另一件事。

## 异步镜像加载，或者说，如何让仅仅传递一个字符串不无聊

这里是`Img.js` :

```
import React from 'react';
import {SimpleCache, createResource} from 'simple-cache-provider';
import withCache from './withCache';

function loadImage(src) {
  const image = new Image();
  return new Promise(resolve => {
    image.onload = () => resolve(src);
    image.src = src;
  });
}

const readImage = createResource(loadImage);

function Img({cache, src, ...props}) {
  return <img src={readImage(cache, src)} {...props} />; }

export default withCache(Img); 
```

Enter fullscreen mode Exit fullscreen mode

这是什么！我们正在创建另一个缓存！是的，我们没有理由不能将多个缓存连接到不同的组件，因为我们“只是”在幕后使用`createContext`，就像我们已经建立的那样。但是我们使用它的目的是新的:异步图像加载！w00t！也就是说:

*   使用`Image()`构造函数(是的，我也不知道这是一个东西，[阅读 MDN 和哭泣](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/Image)
*   用`Promise`包起来，并设置`src`
*   将这个`Promise`传递给`createResource`,由它来完成自己的工作(别问了..仅仅..向上滚动，这是我为你准备的全部)
*   当加载完成后，我们将它传递给`<img src`！

花一点时间来欣赏这是多么有创意。在一天结束的时候，我们将把一个字符串`src`传递给一个字符串`<img src`。再简单不过了。但是在这之间，我们插入了整个疯狂的`createResource`进程来异步加载图像，同时`<img src`什么也没有渲染，所以它什么也没有显示。

**你好，关键又来了:如果`cache`没有`src`的哈希值，我们就“暂停”渲染，并抛出承诺，直到`image`加载完毕，React 才知道要重新渲染`Img`。**

吊杆麦克风下降。

这看起来眼熟吗？传递字符串现在有副作用。这就跟传 JSX 有副作用一样。让你在任何声明性的东西中插入副作用，不仅仅是 JSX！

## 作业

只剩下两个需要探索的了:`Result`和`PosterThumbnail`，但是你现在应该可以从我们对`FullPoster`和`Img`的分析中认出代码模式了。我把它留给读者做练习。

所以退一步说:我们今天学到了什么？

*   `simple-cache-provider.SimpleCache` -在`createContext`中放置一个`cache`
*   `simple-cache-provider.createResource` -它 1)为您的数据获取一个**承诺**，2)输出一个函数，该函数获取一个`cache`和一个 arg 来调用您的**承诺**(也称为**挂起器**
*   如何使用`ReactDOM.unstable_deferredUpdates`将更新委托给较低的优先级
*   `createResource`如何通过**抛出承诺**异步加载数据(！！！)
*   仅仅给你一个布尔值，用于在孩子和回退之间切换
*   如何用`createResource`做**异步镜像加载**(！！！)

300 行代码中包含了大量内容！那不是很疯狂吗？我当然不是光看演讲就明白了这一点；我希望这也能帮助你处理一些更好的细节。

以下是会后聊天中一些值得注意的后续内容:

对于那些想使用 createFetcher 的人来说(尽管 [simple-cache-provider 目前是官方实现](https://twitter.com/acdlite/status/969168681644179456)):

> ![𝕁𝕒𝕞𝕚𝕖 𝕂 profile image](img/adc5d9c9593b750deaaeea6ca1e10c77.png)[@ Jamie builds](https://dev.to/jamiebuilds)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)TL；DR
> 
> 函数 createFetcher(method){
> let resolved = new Map()；
> return {
> read(key)=>{
> if(！resolved.has(key)) {
> 抛出方法(...args)。然后(val = > resolved.set(key，val))；
> }
> return resolved.get(关键)；
> }
> }；
> }2018 年 03 月 01 日上午 11:15[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=969169357094842368)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=969169357094842368)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=969169357094842368)

*(阅读整个帖子，而不仅仅是这条推文)*

想看看 createFetcher(没有简单缓存提供者)的运行吗？[杰米在这个沙盒演示中出现了](https://codesandbox.io/s/zk0y314yqp)

需要更多演示吗？Dan Abramov 不知何故仍在编写实例(使用他的`createFetcher`实现):

> ![unknown tweet media content](img/2d3ed8e1dd20759efe4a6dc91446cd31.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/DXPNbWlXUAAmuxr.mp4" type="video/mp4"></video>![Dan Abramov profile image](img/8410abe414a98a3109390b1a1f7377ee.png)丹阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)状态更新是同步的，所以占位符会立即生效。让我们将同步(对于输入)和异步(对于翻译组件)值分开，降低 API 的速度。现在不显示“正在翻译…”马上就去。[gist.github.com/gaearon/376cfa…](https://t.co/Ehb78rLB3s)2018 年 03 月 01 日 22 点 51 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=969344290567704577)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=969344290567704577)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=969344290567704577)

如果你担心多投:

> ![Sophie Alpert profile image](img/841c5b0263ecfdd387021c123bce2dfc.png)【Sophie alpert】@ sophietes![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Dan _ abramov](https://twitter.com/dan_abramov)-我...。fns)=>{
> constr RES =[]prom =[]；
> fns . foreach(fn =>{
> 【try {
> RES . push(fn())；
> }抓(e) {
> 如果(！伊斯玛仪(e)){
> 抛 e；
> }
> prom . push(e)；
> }
> }：
> 如果(prom.length) {
> 抛出 promise . all(prom)；
> }
> 返回 RES
> }；【2018 年 3 月 19 日下午 20 分至 01 日】[>](https://twitter.com/intent/tweet?in_reply_to=969291270064451584)

*(阅读整个帖子，而不仅仅是这条推文)*

如果你仍然不确定抛出承诺是不是一件好事，你并不孤单(这应该是有争议的！):

> ![Johannes Ewald profile image](img/536bff1b7061dde0b257b538606109c2.png)Johannes e wald@ jhnnns![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)ICYMI:[@ Dan _ abra mov](https://twitter.com/dan_abramov)在 React 中展示了一个即将推出的 API，其中 React 抛出了一个中止用户/应用程序代码的承诺。2018 年 03 月 01 日下午 16:54[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=969254508978155520)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=969254508978155520)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=969254508978155520)

*(阅读整个帖子，而不仅仅是这条推文)*

为什么要用承诺？如果我想取消提取，该怎么办？为什么不是发电机？还是可观的？

> ![Jay Phelps profile image](img/04a1e20cf95ce8d17ec6dbc56a4a693a.png)杰菲尔普斯@ _ 杰菲尔普斯![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ ACD lite](https://twitter.com/acdlite)[@ Dan _ abra mov](https://twitter.com/dan_abramov)对请求取消有什么想法？例如，通过 Symbol.observable 支持 observable 还是只支持订阅/取消订阅对？20:41PM-2018 年 3 月 01 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=969311753015185408)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=969311753015185408)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=969311753015185408)

*(阅读整个帖子而不仅仅是这条推文- [幂等](https://twitter.com/acdlite/status/969173378937470977)是关键词)*

哪里可以不使用挂起？安德鲁·克拉克抓住了你:

> ![Andrew Clark profile image](img/0e3d1effc010330edb91638551667232.png)Andrew Clark@ ACD lite![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)你知道吗
> 
> 你可以从 getDerivedStateFromProps 内部暂停。它“工作正常”是因为它是 React 渲染阶段的一部分。
> 
> 你也可以在一个 setState reducer 内部挂起(第一个参数)。2018 年 03 月 02 日上午 04:26[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=969428655238557697)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=969428655238557697)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=969428655238557697)

*(阅读整个帖子，而不仅仅是这条推文)*

我错过了什么或做错了什么？请在下面告诉我！干杯！

* * *

编辑 2018 年 3 月 27 日

我现在正在重新观看合并的 JSConf 和 T2 react test 演示，以找出悬念用例。开始了。

*   [https://youtu.be/v6iR3Zk4oDY?t=15m50s:](https://youtu.be/v6iR3Zk4oDY?t=15m50s:)创建抓取器-基本抓取
*   https://youtu.be/v6iR3Zk4oDY?t=17m43s:需要告诉 React 这可以是异步的
*   [https://youtu.be/v6iR3Zk4oDY?t=18m5s:](https://youtu.be/v6iR3Zk4oDY?t=18m5s:)表明它是交互式的，没有竞争条件
*   [https://youtu.be/v6iR3Zk4oDY?t=18m53s:](https://youtu.be/v6iR3Zk4oDY?t=18m53s:)高延迟演示-占位符、延迟、回退。如果它的任何一个孩子需要数据，它会等待他们。屏幕保持互动
*   [https://youtu.be/v6iR3Zk4oDY?t=21m51s:](https://youtu.be/v6iR3Zk4oDY?t=21m51s:)第二个 createFetcher 演示-电影评论。将 MovieDetails 和 MovieReviews 作为兄弟等待。即使加载了一个，它也不会显示，直到它的兄弟也被加载。
*   https://youtu.be/v6iR3Zk4oDY?t=22m43s::如果你不想让兄弟姐妹们互相等待呢？再做一个占位符！
*   [https://youtu.be/v6iR3Zk4oDY?t=24m1s:](https://youtu.be/v6iR3Zk4oDY?t=24m1s:)有装载的视觉指示。使用加载！简单的内嵌旋转器，以及由`isLoading`渲染道具控制的导航。没有比赛条件。
*   [https://youtu.be/v6iR3Zk4oDY?t=26m9s:](https://youtu.be/v6iR3Zk4oDY?t=26m9s:)使用 createFetcher 轻松拆分代码
*   [https://youtu.be/v6iR3Zk4oDY?t=27m56s:](https://youtu.be/v6iR3Zk4oDY?t=27m56s:)避免图片加载时的页面跳转——异步获取图片！使用 createFetcher 创建 ImageFetcher，并将其放入 src！
*   https://youtu.be/v6iR3Zk4oDY?t=30m13s::IO 演示结束:我们已经为组件建立了一个通用的方法来在加载异步数据时暂停渲染。
*   [https://youtu.be/v6iR3Zk4oDY?t=31m32s:](https://youtu.be/v6iR3Zk4oDY?t=31m32s:)饭桶比喻
*   [https://youtu.be/v6iR3Zk4oDY?t=33m12s:](https://youtu.be/v6iR3Zk4oDY?t=33m12s:)CPU+IO =异步渲染

反应测试

*   [https://youtu.be/6g3g0Q_XVb4?t=2m6s](https://youtu.be/6g3g0Q_XVb4?t=2m6s)我们正在解决的问题
*   [https://youtu.be/6g3g0Q_XVb4?t=5m15s](https://youtu.be/6g3g0Q_XVb4?t=5m15s)硬编码电影演示
*   [https://youtu.be/6g3g0Q_XVb4?t=7m34s](https://youtu.be/6g3g0Q_XVb4?t=7m34s)基本 createFetcher 演示-解决竞争条件
*   [https://youtu.be/6g3g0Q_XVb4?t=10m52s](https://youtu.be/6g3g0Q_XVb4?t=10m52s)占位符演示——解决潜在的滞后连接问题
*   [https://youtu.be/6g3g0Q_XVb4?t=12m56s](https://youtu.be/6g3g0Q_XVb4?t=12m56s)添加第二个抓取器——即使在第一个抓取器之前加载，第二个也不会渲染
*   [https://youtu.be/6g3g0Q_XVb4?t=14m43s](https://youtu.be/6g3g0Q_XVb4?t=14m43s)添加占位符以显示非阻塞
*   [https://youtu.be/6g3g0Q_XVb4?t=15m29s](https://youtu.be/6g3g0Q_XVb4?t=15m29s)保持旧屏幕的交互性是一种需要
*   [https://youtu.be/6g3g0Q_XVb4?t=16m3s](https://youtu.be/6g3g0Q_XVb4?t=16m3s)所以用`<Loading />`！
*   [https://youtu.be/6g3g0Q_XVb4?t=18m35s](https://youtu.be/6g3g0Q_XVb4?t=18m35s)代码拆分
*   [https://youtu.be/6g3g0Q_XVb4?t=21m41s](https://youtu.be/6g3g0Q_XVb4?t=21m41s)图像加载
*   [https://youtu.be/6g3g0Q_XVb4?t=23m37s](https://youtu.be/6g3g0Q_XVb4?t=23m37s)用占位符缓慢加载图像
*   [https://youtu.be/6g3g0Q_XVb4?t=24m48s](https://youtu.be/6g3g0Q_XVb4?t=24m48s)N+1 预压:`hidden={true}` **这个超级酷，是新的**
*   [https://youtu.be/6g3g0Q_XVb4?t=29m15s](https://youtu.be/6g3g0Q_XVb4?t=29m15s)反应出悬念的好处
*   [https://youtu.be/6g3g0Q_XVb4?t=30m1s](https://youtu.be/6g3g0Q_XVb4?t=30m1s)饭桶比喻