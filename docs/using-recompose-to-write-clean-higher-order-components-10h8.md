# 使用重新组合来编写干净的高阶组件

> 原文：<https://dev.to/bnevilleoneill/using-recompose-to-write-clean-higher-order-components-10h8>

### 使用重新组合来编写干净的高阶组件

[![](img/2a295a0aac453ede641b4afc07cf4e6a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9rPIryvV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYwgQkhqNcJZGgym5I-VSMQ.png)

如果您喜欢在 React 中保持简单，通过使用功能组件语法创建小组件，并使用它们作为片段来创建更大的组件， [*Recompose*](https://github.com/acdlite/recompose) 可以帮助您对高阶组件(hoc)做同样的事情。

使用*重组*可以更容易地创建小的高阶组件，这些组件可以被组合成更复杂的组件。使用由 *Recompose* 鼓励的方法，你将不再需要更多的类语法来创建 React 组件。

但是在进入细节之前，让我们开始回顾一些概念…

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### 高阶函数

在 Javascript 中，我们有一种特殊类型的函数，叫做**高阶函数:**

> 一个[高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)是一个处理其他函数的函数，要么是因为它接收其他函数作为参数(在身体函数的某个点执行它们)，要么是因为它在被调用时返回一个新函数，或者两者都有。

```
const sum = (a, b) => a + b
const multiplication = (a, b) => a * b

// Our Higher-Order Function
const getResultOperation = op => (a, b) => `The ${op.name} of ${a} and ${b} is ${op(a, b)}`

const getSumResult = getResultOperation(sum)
const getMultiplicationResult = getResultOperation(multiplication)

console.log( getSumResult(2, 5) ) // The sum of 2 and 5 is 7 
console.log( getMultiplicationResult(2, 5) ) // The multiplication of 2 and 5 is 10 
```

*获取结果操作*

在上面的例子中，getResultOperation 接收一个函数并返回一个新函数。所以它是一个高阶函数。

> *Javascript 中最流行的*高阶函数*有数组方法* [*map*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) *，*[*filter*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)*或*[*reduce*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)*。它们都应用一些函数*作为参数*传递给数组的元素，以得到一些结果*

### 高阶分量

在 React 中，我们有高阶函数的等价物，但对于组件，即所谓的高阶组件。

> 一个[高阶分量](https://reactjs.org/docs/higher-order-components.html)是一个接受一个分量并返回一个新分量的函数。

高阶元件什么时候有用？嗯，主要是为了重用涉及跨组件行为的逻辑。让我们用下面的场景来解释一下。

让我们假设我们已经有了一个组件按钮

```
const Button = ({ type = "primary", children, onClick }) => (
  <button className={`btn btn-${type}`} onClick={onClick}>
    {children}
  </button>
); 
```

我们希望基于此按钮创建另一个 ButtonWithTrack(Button 上的相同属性也应该在 ButtonWithTrack 上工作，并应用相同的样式)，但具有改进的行为(如跟踪它被单击的次数并在按钮本身上显示该值)。

为此，我们可以做…

```
import Button from "./Button";

class ButtonWithTrack extends Component {
  constructor(props) {
    super(props);
    this.state = {
      times: 0
    };
  }
  handleClick = e => {
    let { times } = this.state;
    const { onClick } = this.props;
    this.setState({ times: ++times });
    onClick && onClick();
  };
  render() {
    const { children } = this.props;
    const { times } = this.state;
    return (
      <span onClick={this.handleClick}>
        <Button type={times > 5 ? "danger" : "primary"}>
          {children} <small>{times} times clicked</small>
        </Button>
      </span>
    );
  }
} 
```

我们重新使用了原来的按钮，所以现在一切正常。

再来看另一个组件链接:

```
const Link = ({ type = "primary", children, href, onClick }) => (
  <a style={styles} className={`badge badge-${type}`} href={href} onClick={onClick}>
    {children}
  </a>
); 
```

我们希望添加与添加到按钮上的行为完全相同的行为。

那怎么办呢？我们应该在两个文件中重复 90%的代码吗？或者，有没有一种方法可以将添加到 ButtonWithTrack 中的逻辑去掉，使其可以应用于按钮和链接组件？

**高阶组件**前来救援！！

为了解决这个问题，我们可以创建一个更高阶的组件，这是一个函数，它接受一个组件，并返回该组件的增强版本，具有我们想要的行为。

例如，我们可以这样做:

```
const withClickTimesTrack = WrappedComponent =>
  class extends Component {
    constructor(props) {
      super(props);
      this.state = {
        times: 0
      };
    }
    handleClick = e => {
      e.preventDefault();
      let { times } = this.state;
      const { onClick } = this.props;
      this.setState({ times: ++times });
      onClick && onClick();
    };
    render() {
      const { children, onClick, ...props } = this.props;
      const { times } = this.state;
      return (
        <span onClick={this.handleClick}>
          <WrappedComponent
            type={times > 5 ? "danger" : "primary"}
            {...props}
          >
            {children} <small>({times} times clicked)</small>
          </WrappedComponent>
        </span>
      );
    }
  }; 
```

因此，我们可以通过使用这样的 withClickTimesTrack 来简化从 Button 创建 componentButtonWithTrack 的过程:

```
import withClickTimesTrack from "./hoc/withClickTimesTrack";

const Button = ({ type = "primary", children, onClick }) => (
  <button className={`btn btn-${type}`} onClick={onClick}>
    {children}
  </button>
);

const ButtonWithTrack = withClickTimesTrack(Button); 
```

现在，我们可以轻松地将相同的增强应用到其他组件，如 Link:

```
import withClickTimesTrack from "./hoc/withClickTimesTrack";

const Link = ({ type = "primary", children, href, onClick }) => (
  <a style={styles} className={`badge badge-${type}`} href={href} onClick={onClick}>
    {children}
  </a>
);
const LinkWithTrack = withClickTimesTrack(Link); 
```

很酷，不是吗？

但是我们可以认为这个 HOC 同时添加了太多的行为(处理程序、状态和新的 UI)。

如果我们将 HOC 背后的逻辑分成更小的部分，不是更好吗？

### 作曲 HOCs

好吧，就这么定了！我们希望将 HOC 的这三种行为隔离开来，这样我们就可以在其他组件中独立地重用它们:

*   添加时间状态
*   添加自定义手柄单击
*   显示元素内部的时间状态

为此，我们可以创建 3 个 hoc，每个 hoc 将添加一个特定的行为…

```
const withStateTimes = WrappedComponent =>
  class extends Component {
    constructor(props) {
      super(props);
      this.state = {
        times: 0
      };
    }
    setTimes = (times) => {
      this.setState({ times })
    }
    render() {
      const { times } = this.state
      const { setTimes } = this
      return (
        <WrappedComponent times={times} setTimes={setTimes} { ...this.props } />
      );
    }
  }; 
```

*withStateTimes.js*

```
const withHandlerClick = WrappedComponent => props => {

  let { times, setTimes, children, onClick, ..._props } = props;

  const handleClick = e => {
    e.preventDefault();
    setTimes( ++times );
    onClick && onClick();
  };

  return (
    <WrappedComponent times={times} handleClick={handleClick} { ..._props }>
      {children}
    </WrappedComponent>
  );

} 
```

*withHandlerClick.js*

```
const withDisplayTrack = WrappedComponent => props => {
  const { children, onClick, handleClick, times, ..._props } = props;
  return (
    <span onClick={handleClick}>
      <WrappedComponent
        type={times > 5 ? "danger" : "primary"}
        {..._props}
      >
        {children} <small>({times} times clicked)</small>
      </WrappedComponent>
    </span>
  )
} 
```

*withDisplayTrack.js*

有了这 3 个 hoc，我们就可以用这种方式将它们应用到我们的元素中…

```
const ButtonWithTrack = withStateTimes(withHandlerClick(withDisplayTrack(Button))); 
```

这是怎么回事？嗯，withDisplayTrack(Button)返回一个在 withHandlerClick 的调用中使用的组件，该组件也将返回一个在 withStateTimes 的调用中使用的组件，该调用将返回我们的最终组件(ButtonWithTrack)。

正如你所看到的，这个想法很好，因为我们可以以这种方式重用我们的代码，但是创建这些 hoc 有点复杂，并且以这种方式应用它们也有点难以理解。

这有什么改进吗？

[***改编***](https://github.com/acdlite/recompose) 来救援了！！:)

### 重新构图

什么是*重组*？

用他们自己的话说:

> [重组](https://github.com/acdlite/recompose)是功能组件和高阶组件的 React 实用带。把它想象成反应的[洛达什](https://lodash.com/docs/4.17.10#lodash)。

因此，这是一套我们可以用来改进我们的 HOC 的组织、创建和应用的方法，鼓励使用与 HOC 的组合相结合的功能性无状态组件。

先说最常用的*重新组合*的方法叫做 compose。

#### [作曲](https://github.com/acdlite/recompose/blob/master/docs/API.md#compose)

使用 compose，我们可以将多个高阶分量合成为一个高阶分量。

在我们的场景中，使用 compose，我们现在可以这样表达我们的 HOC 的应用:

```
import { compose } from "recompose";

...

const ButtonWithTrack = compose(
  withStateTimes,
  withHandlerClick,
  withDisplayTrack
)(Button) 
```

*Button.js*

更清晰易读，对吗？

#### [与国家](https://github.com/acdlite/recompose/blob/master/docs/API.md#withstate)

在我们的场景中，另一个有用的重组的方法是 withState。

这个方法创建了一个与我们在 withStateTimes.js 中实现的行为几乎相同的特设。

*   它添加了一个状态属性
*   它创建一个*处理程序*来设置这个状态属性的值
*   它允许我们设置一个初始值

所以，用*重组*，现在我们可以这样表达同样的逻辑……

```
...
import { withState } from "recompose";
const withStateTimes = withState('times', 'setTimes', 0)
... 
```

*withStateTimes.js*

真的吗？是的，真的:)

*重组*的效用开始有意义了吧？

#### [带手柄](https://github.com/acdlite/recompose/blob/master/docs/API.md#withhandlers)

让我们继续改进我们场景的代码。让我们用 HandlerClick。为了改进这个特设的创建，我们可以使用带有处理程序*的方法来重组*。

```
import { withHandlers } from "recompose";

const withHandlerClick = withHandlers({
  handleClick: props => e => {
    let { times, onClick, setTimes } = props;
    e.preventDefault()
    setTimes( ++times );
    onClick && onClick();
  }
}) 
```

*withHandlerClick.js*

带有 Handlers 的方法接受处理程序创建者的对象映射。传递给 withHandlers 的这个对象的每个属性应该是一个高阶函数，它接受一组 props 并返回一个函数处理程序。通过这种方式，我们可以生成一个可以访问组件属性的处理程序。

#### [setDisplayName](https://github.com/acdlite/recompose/blob/master/docs/API.md#setdisplayname)

在我们的例子中，如果我们用 [React 开发工具](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)调试代码，withDisplayTrack 返回的组件显示为 Unknown。

为了解决这个问题，我们可以使用*的 setDisplayName 重新组合*来导出最终的 HOC，它将返回一个名为 ComponentWithDisplayTrack 的组件。

```
export default compose(
  setDisplayName('ComponentWithDisplayTrack'),
  withDisplayTrack
); 
```

#### [生命周期](https://github.com/acdlite/recompose/blob/master/docs/API.md#lifecycle)

通过方法生命周期，我们可以将生命周期方法添加到我们的函数语法组件中。

在我们的场景中，我们可以添加一个不同版本的按钮来显示待处理消息的数量。

我们可以使用 messages props:
创建一个 HOC 来返回按钮的不同视图

```
import React from "react";
import { compose, setDisplayName } from "recompose";

const withDisplayMessages = WrappedComponent => props => {
  const { children, messages, loading, ..._props } = props;
  return (
    <WrappedComponent {..._props}>
      {children}
      {loading ? (
        <span className="fas fa-spinner fa-pulse"> </span>
      ) : (
        <span className="badge badge-light">{messages}</span>
      )}
    </WrappedComponent>
  );
};

export default compose(
  setDisplayName("withDisplayMessages"),
  withDisplayMessages
); 
```

*withDisplayMessages.js*

我们可以向我们的组件添加一个 componentDidMount 生命周期方法，它将添加:

*   当我们的*假请求*开始时加载状态设置为真，当它结束时设置为假
*   一条消息声明哪个值将被我们的*假请求*返回的随机数更新

这里被管理的的加载和消息状态*将在返回的组件中各添加一个新的*属性*，用于传播相应的值:* 

```
import { lifecycle } from "recompose";

const getPendingMessages = () => {
  const randomNumber = Math.ceil(Math.random() * 10);
  return new Promise(resolve => {
    setTimeout(() => resolve(randomNumber), randomNumber * 1000);
  });
};

const withDidMountStateMessages = lifecycle({
  componentDidMount() {
    this.setState({ loading: true });
    getPendingMessages().then(messages => {
      this.setState({ loading: false, messages });
    });
  }
});

export default withDidMountStateMessages; 
```

*withdimountstatemessages . js*

有了这些新的 HOC，我们现在可以快速创建我们的新型按钮:

```
const ButtonWithMessages = compose(
  withDidMountStateMessages, 
  withDisplayMessages
)(Button) 
```

#### [默认道具](https://github.com/acdlite/recompose/blob/master/docs/API.md#defaultprops)

有了这些 hoc，我们可以用很少的几行代码把这些新行为转换成一个链接。我们可以添加 defaultProps 来更改链接的默认类型。

```
const LinkWithMessages = compose(
  defaultProps({ type: "info" }),
  withDidMountStateMessages,
  withDisplayMessages
)(Link); 
```

*Link.js*

### 结论

使用这些方法，我们可以轻松地创建另一个版本的按钮(只是为了显示这种模式的灵活性)来完成我们的演示，该按钮跟踪从 3 到 0 的点击，并添加另一个道具，以便我们可以在倒计时到达 0 时更改类型。

```
const ButtonWithTrackCountdown = compose(
  withState('times', 'setTimes', 3),
  withState('type', 'setType', 'primary'),
  withHandlers({
    handleClick: props => e => {
      let { times, onClick, setTimes, setType } = props;
      e.preventDefault()
      if ( times <= 0 ) {  setType('secondary') }
      else { setTimes( --times ) }
      onClick && onClick();
    }
  }),
  withDisplayTrack
)(Button) 
```

如你所见，使用*重组*可以更容易地将逻辑委托给小的高阶组件，然后*将它们组合成更复杂的 HOC，我们可以用它来创建不同版本的组件，重用我们的大部分代码。*

此外， *Recompose* 不鼓励使用类语法来创建组件，而鼓励使用功能性无状态组件和更高级的组件相结合。

仅使用功能组件的最重要优势是:

*   他们鼓励代码更加可重用和模块化。
*   他们不鼓励庞大复杂的组件做太多的事情。

基本上，一旦你知道了*如何重组*方法，它就简化了 React 组件的开发和组织。

有更多的方法可以用来以更简单的方式产生更多的高阶分量。

在[官方回购](https://github.com/acdlite/recompose)中，你可以找到一些对你的项目有用的 [*重组*配方](https://github.com/acdlite/recompose/wiki/Recipes)。

此外，这里有这篇文章中使用的代码和结果的现场演示。

[https://codesandbox.io/embed/jp392km6vy](https://codesandbox.io/embed/jp392km6vy)

那么，现在你对*重新作曲*有了更多的了解……你的第一印象是什么？在创建组件时，你认为这是一个好方法吗？

我的看法是…我喜欢！！我真的很喜欢由 *Recompose* 鼓励的面向创建小而简单的片段(组件和 HOC)的模式，这些片段可以用来以易读的方式创建更复杂的片段，并且是面向函数式编程的。

嗯，那是我的看法。你的呢？

* * *

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[使用重新组合来编写更干净的高阶组件](https://blog.logrocket.com/using-recompose-to-write-clean-higher-order-components-3019a6daf44c/)首先出现在[的博客](https://blog.logrocket.com)上。