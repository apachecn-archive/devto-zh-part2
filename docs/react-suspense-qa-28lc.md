# 反应悬念问答

> 原文：<https://dev.to/swyx/react-suspense-qa-28lc>

# 为什么我们要反应悬念，在 GIF 中

[![https://thumbs.gfycat.com/QueasyGrandIriomotecat-size_restricted.gif](img/8ae9c75bf33fa0b0ed9d765364a15d1a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wgyUvGbO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/QueasyGrandIriomotecat-size_restricted.gif)

# 问&答

声明:我不代表 React 团队，这只是我，一个 React 的普通用户，在几十个小时的研究中了解到的信息。

*   **问:什么是 React 悬疑？**
    *   答:[组件在加载异步数据时暂停渲染的通用方式。](https://reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html)
*   问:那到底是什么意思？
    *   答:React 获得了 [2 个新的原始](https://twitter.com/acdlite/status/969704631424053249)能力，这给了你更多的控制来加载*任何东西*、**，同时**保持互动。许多样板文件(例如，在没有竞争条件的情况下获取数据)将变得更容易编写，一些以前不可能的事情(例如，在等待数据时部分呈现组件)将变得容易。
*   问:我已经很累了。我真的要学这个吗？
    *   答:没有。
*   **问:真的？**
    *   是的。如果你不喜欢这些，你不需要改变你做事的方式。 [React 真值 API 稳定性](https://reactjs.org/docs/design-principles.html#stability)。
*   问:那我为什么要关心？
    *   a:这可能会让你的生活轻松很多。
    *   答:也很酷。你喜欢酷的东西，对吗？
*   问:它如何让我的生活更轻松？
    *   答:任何时候你需要显示需要加载的东西，你为它创建一个`fetcher`,然后在你的`render`方法中使用`fetcher`。
        *   如果 React 试图渲染尚未获取的内容，它会停止渲染并获取该内容。
        *   如果某个东西之前被获取过，它会被**缓存**并立即呈现。
    *   如果你想要一个`Placeholder`(例如 spinner)来显示一个获取是否花费了太长时间，那只是几行代码。
    *   如果您希望在获取不同内容时避免级联微调器，这就是默认情况下悬念的工作方式。我们真的不喜欢层叠旋转器。
    *   顺便说一句，级联旋转器意味着[所有无意的加载状态](https://twitter.com/acdlite/status/955160681208168448)
*   问:这难道不是 Redux 或生命周期方法的工作吗？
    *   a:不再是了，如果你不想要的话。不需要使用`componentDidMount`或调度`Redux`动作来获取数据。不需要像`isLoading`或`pastDelay`那样定义额外的状态变量，也不需要处理在不同时间加载多个东西而导致的状态爆炸。我有没有提到你的用户界面在整个过程中都是交互式的？
*   **问:`react-loadable`不是已经这么做了吗？**
    *   答:只有一点点。重点是在加载时保持事物的交互性(通过在“单独的线程”上渲染)...我们将在后面解释)不能在“用户域”中完成，需要在 React(coming)`AsyncMode`中处理。
*   **问:酷，那么这个`fetcher`和`Placeholder`是 React 的新部件？**
    *   答:没有。这就是“高级 API”，随着人们对它们进行实验，你将会在明年看到一批这样的 API 问世。现在不要担心低级别的 API，它们是不稳定的。
*   问:你是在尝试获取信息吗？这永远不会发生。
    *   答:每个人都已经厌倦了那个笑话，现在才三月。请多原创。伟大的电影。
*   **问:为什么叫悬疑？**
    *   答:这是对其内部工作方式的参考。当你试图渲染需要数据的东西时，你的`fetcher` **`throw`是一个承诺**起反应，而反应**暂停**渲染(在[限制](https://twitter.com/dan_abramov/status/971187182621872128)内)同时获取数据。当获取正在进行时，您的 UI 仍然是交互式的，就好像获取是在一个不同的线程上进行的一样(注意:[在技术上不是真的](https://twitter.com/aweary/status/913619299087949824))。
*   问:不管愿意与否，这样做安全吗？
    *   答:这被认为是有争议的，但是到目前为止还没有人找到真正的反对意见。并且已经编写了大量的[测试](https://github.com/acdlite/react/blob/7166ce6d9b7973ddd5e06be9effdfaaeeff57ed6/packages/react-reconciler/src/tests/ReactSuspense-test.js)。你会经常看到“代数效应”这个词，但是为了我们的目的，你可以把它们看作是[可恢复的例外](https://www.youtube.com/watch?v=hrBq8R_kxI0)。作为一个用户，你可能永远也不会自己写这个。
*   **问:缓存是怎么回事？React 正在获取缓存吗？**
    *   答:缓存很棒，是整个“取不取”的核心。React 为用户提供了制作缓存的能力，我们也将在那里看到大量的创新。React 团队已经提供了一个[简单缓存提供者](https://github.com/facebook/react/blob/master/packages/simple-cache-provider/src/SimpleCacheProvider.js#L148)，它可能会在基本应用中非常流行，但是它们没有处理很多细微差别，比如细粒度缓存失效。你可能听说过这很难。
*   问:说得够多了。我在哪里可以看到一些代码？
    *   答:先看看丹的 JSConf 关于时间切片+悬疑的演讲(官方视频是[这里](https://reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html)但是我不能嵌入):

[https://www.youtube.com/embed/v6iR3Zk4oDY](https://www.youtube.com/embed/v6iR3Zk4oDY)

*   **问:更！？**
    *   a:然后检查他刚才提到悬念的后续部分:

[https://www.youtube.com/embed/6g3g0Q_XVb4](https://www.youtube.com/embed/6g3g0Q_XVb4)

*   **问:更！？？**
    *   a:那就在这里播放电影试玩
    *   答:然后查看[实现拉请求](https://github.com/facebook/react/pull/12279)、[测试](https://github.com/acdlite/react/blob/7166ce6d9b7973ddd5e06be9effdfaaeeff57ed6/packages/react-reconciler/src/tests/ReactSuspense-test.js)和[异步反应演示](https://gist.github.com/acdlite/f31becd03e2f5feb9b4b22267a58bc1f)(查看部署的应用[这里](https://build-mbfootjxoo.now.sh/)，这是异步渲染真正让我点击的地方)
*   **问:好吧，太久了，没看。给我解释一下，我可以用反应悬念来做什么？**
    *   答:**深呼吸**
    *   数据提取
    *   代码拆分
    *   图像加载
    *   CSS 加载
    *   去抖动
    *   流媒体服务器渲染
    *   无竞争条件加载
    *   有意加载状态
    *   急切加载/预取
    *   “推测性渲染”也就是在你真正需要之前渲染组件
    *   “透明异步”也就是渲染子组件，而不用担心它们是否准备好被渲染
    *   ...(我们仍在研究这是如何工作的)
    *   更少的样板文件！
    *   如果这听起来很多，那是因为它确实很多。
*   **问:哇。丹·阿布拉莫夫是怎么做到这一切的？？他是个野兽？**
    *   答:丹在(超级棒的)演示上非常非常努力。整个 React 团队在社区的反馈下致力于 React 悬念。
*   **问:太酷了！我现在就想用在生产上！！**
    *   那是一个非常糟糕的主意，你应该为自己感到羞耻。
*   问:但是我什么时候可以拿到？
    *   答:只要在心理上盯住“到 2018 年底”，就不会太失望。
*   问:接下来我应该读什么？
    *   答:如果你想浏览一些代码演示，这里有一个我做的(再次警告:不稳定！)低级 API。
    *   编辑:我还做了一个更深入的 [React 悬疑小抄](https://spectrum.chat/impostor-syndrome?thread=56dc92fb-46a1-49c5-9c42-47d3e90c081e)演示，看看吧！