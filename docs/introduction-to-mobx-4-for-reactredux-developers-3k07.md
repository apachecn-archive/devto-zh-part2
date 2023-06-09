# 面向 React/Redux 开发人员的 MobX 4 简介

> 原文：<https://dev.to/swyx/introduction-to-mobx-4-for-reactredux-developers-3k07>

MobX 使用可观测量的“魔力”来管理状态和副作用。这不仅有一个学习曲线，而且是一个完全不同的编程范式，并且没有很多关于如何使用 React with Mobx 的最新培训材料，而关于使用 React with Redux 的内容则多得多。

在这个介绍中，我们将逐步构建一个简单的应用程序，该应用程序 pings 一个模拟 API 来查看 MobX 如何与 React 一起工作，然后制作一个 MobX + React 看板来展示 MobX 的强大功能！

[![it will look like this](img/cadbefe437f14f9238c9c6cff317cf92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VjwhytmQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6764957/37550034-5a31617a-295d-11e8-8799-60c6f57bde0a.gif)

我们将如何继续:

*   **例一**。构建一个基本的应用程序，让你键入文本**输入**，并反映在**显示**。*我们展示了建立`observable` s 和`observer`组件的基础。*
*   **例子 B** 。我们将**输入**和**显示**分成兄弟姐妹，以模拟一个更复杂的应用程序。我们还通过 ping 一个模拟 API 来引入异步状态更新。*为此，我们使用`mobx-react` `Provider`将 MobX 状态放入 React 上下文中，以演示类似于`react-redux`的简单的兄弟对兄弟或兄弟对父母的通信。*
*   **例子 C** :我们在应用程序中添加了一个辅助**显示屏**。*展示了`computed`变量的用处(Mobx 的一个概念)。*
*   例 D :我们把我们的应用程序放大到任意数量的显示器。*演示了如何为我们的 MobX 状态使用数组和映射。*
*   例子 E :调整和清理！*我们添加了 MobX 开发工具，将整个应用置于`useStrict`模式，并解释了 MobX `action`和`transaction`的正式用法，以获得更好的应用性能。*

本教程将使用[最近发布的 MobX 4](https://medium.com/@mweststrate/mobx-4-better-simpler-faster-smaller-c1fbc08008da) 和 MobX-React 5。很多人将 MobX 与装饰者联系在一起，这只是一个[第二阶段提案](https://www.github.com/tc39/proposal-decorators)。这(理所当然地)引起了一些人的犹豫，但是 MobX 4 引入了基于非修饰的语法，所以我们再也没有那个借口了！然而；对于教程作者来说，这是一个问题，因为你必须决定要么教一个，要么教另一个，或者两个都教。为了解决这个问题，这里的每个例子都将使用非 decorator 语法作为主要版本，但是将有一个使用 decorator 的克隆来显示等价的实现(例如，例子 A 对 Decorators A)。

读者请注意:我们并没有试图推荐 MobX 而不是 Redux，反之亦然。这只是为了向像我这样只熟悉 Redux 的人介绍 MobX 的核心概念。我将试图得出一些结论，但通情达理的人不会同意。此外，Michel Weststrate 反复声明[两个库满足完全不同的需求和价值观](https://codeburst.io/the-curious-case-of-mobx-state-tree-7b4e22d461f)。

# 例 A1: React + MobX

下面是我们使用 React + MobX 的非常基础的应用:

```
import { decorate, observable } from "mobx";
import { observer } from "mobx-react";

const App = observer(
  class App extends React.Component {
    text = ""; // observable state
    render() {
      // reaction
      return (
        <div>
          Display: {this.text} <br />
          <input
            type="text"
            onChange={e => {
              this.text = e.target.value; // action
            }}
          />
        </div>
      );
    }
  }
);
decorate(App, { text: observable }); 
```

*( [举例 A1](https://codesandbox.io/s/236xqx6qn0) ，[装修工 A1](https://codesandbox.io/s/n7ynrm72op) )*

您可以在这里看到,`observer`连接了`App`的可观察的`text`属性，这样每当您更新`text`时，它就会重新呈现。

虽然这很好，但它确实与使用`state`和`setState`没有任何区别。如果你有 React，你不需要 MobX 来做这些。

# 例 A2:那又怎样？

让我们试着将状态和视图模型的关注点分开:

```
// this deals with state
const appState = observable({
  text: "" // observable state
});
appState.onChange = function(e) { // action
  appState.text = e.target.value;
};

// this deals with view
const App = observer(
  class App extends React.Component {
    render() { // reaction
      const { text, onChange } = this.props.store;
      return (
        <div>
          Display: {text} <br />
          <input type="text" onChange={onChange} />
        </div>
      );
    }
  }
);

// you only connect state and view later on...
// ... 
<App store={appState} /> 
```

*( [举例 A2](https://codesandbox.io/s/n995rqm67j) ，[装修工 A2](https://codesandbox.io/s/wqowjv9rvw) )*

这里的`store`:

*   被显式地作为道具传入(我们稍后将使用`Provider`模式)
*   自带自己的动作处理程序(不需要导入单独的 reducers)

# 例 A3:但那不是 OO

看看上面代码的这一部分。

```
const appState = observable({
  text: "" // observable state
});
appState.onChange = function(e) { // action
  appState.text = e.target.value;
}; 
```

是的，我不喜欢那样。方法没有封装在可观察对象中。我们能让它更面向对象吗？

```
// import { decorate } from 'mobx'

class State {
  text = ""; // observable state
  onChange = e => (this.text = e.target.value); // action
};
decorate(State, { text: observable });
const appState = new State() 
```

*( [举例 A3](https://codesandbox.io/s/5xmo5n513n) ，[装修工 A3](https://codesandbox.io/s/wo8k6j190k) )*

啊。好多了(尤其是不需要使用`decorate`的 Decorators 例子)！

# 例 B1:但是我讨厌道具钻！

就像`react-redux`让你把商店放在`Provider`里一样，`mobx-react`也有一个`Provider`以同样的方式工作。我们将把我们的显示和输入组件重构为兄弟应用:

```
 import { inject, observer, Provider } from "mobx-react";

class State {
  text = ""; // observable state
  onChange = e => (this.text = e.target.value); // action
}
decorate(State, { text: observable });
const appState = new State();

const Display = inject(["store"])(
  observer(({ store }) => <div>Display: {store.text}</div>) );

const Input = inject(["store"])(
  observer(
    class Input extends React.Component {
      render() {
        // reaction
        return <input type="text" onChange={this.props.store.onChange} />;
      }
    }
  )
);

// look ma, no props
const App = () => (
  <React.Fragment>
    <Display />
    <Input />
  </React.Fragment> );

// connecting state with context with a Provider later on...
// ...
<Provider store={appState}>
    <App />
  </Provider> 
```

*( [举例 B1](https://codesandbox.io/s/n71y76okym) ，[装修工 B1](https://codesandbox.io/s/mjz6jlyj0j) )*

注意，如果我要添加一个-second- store，我可以简单地定义另一个`observable`，并将其作为另一个属性传递给`Provider`，然后我可以从任何子节点调用它。不再有雷杜风格`combineReducers`！

使用提供者还有助于避免创建全局存储实例，这是 MobX React 最佳实践中强烈反对的。

*MobX 4 注意:如果你只是试图使用旧的 MobX `observer(['store'])`简写，它总是与`observer` + `inject(['store'])`同义，你会得到一个非常好的反对警告，不要再这样做了。*

我发现这个注入/观察器语法有点复杂，所以这是一个很好的小实用函数，你可以定义它来减少输入:

```
const connect = str => Comp => inject([str])(observer(Comp)); 
```

嘿！那就像我们的好朋友`react-redux`里的`connect`！API 略有不同，但是您可以定义任何您想要的东西🤷🏼‍♂️.

# 举例 B2:好吧，但是异步呢

对于异步 API 获取，我们有几个选择。我们可以选择:

*   `mobx-thunk`
*   `mobx-observable`
*   `mobx-saga`
*   和大约 300 个其他选项。

它们都是特别的雪花，我们迫不及待地想看看你会选择什么！

*因愤怒而暂停不干了...*

好吧，如果你看不出来，我是在开玩笑。使用可观测量意味着你可以“仅仅”改变可观测量，你的下游状态会相应地做出反应。你可能已经注意到，我已经用`// reaction`、`// action`和`// observable state`注释了上面的代码示例，它们的意思就是它们在英语中通常的意思。我们会回来的。

回到代码！假设我们现在有一个名为`fetchAllCaps`的异步 API。这是一个`Promise`,在 1 秒钟的等待后，它基本上将你传递给它的任何文本大写。因此，这模拟了您想要采取的任何异步操作的基本请求-响应流。让我们把它插入到我们到目前为止的例子中！

```
class State {
  text = ""; // observable state
  onChange = e => {
    // action
    this.text = e.target.value;
    fetchAllCaps(e.target.value).then(val => (this.text = val));
  };
}
decorate(State, { text: observable });
const appState = new State(); 
```

*( [举例 B2](https://codesandbox.io/s/nn957yx004) ，[装修工 B2](https://codesandbox.io/s/mjz6jlyj0j) )*

那是...容易吗？

注意，这里我们使用了[公共类字段](https://tc39.github.io/proposal-class-public-fields/)的第二阶段特性来处理`onChange`属性，而没有使用 decorators，这也是第二阶段。我决定这样做，因为公共类字段在 React 中是如此广泛(例如，它附带了`create-react-app`)以至于您可能已经在 Babel 中设置了它，或者如果需要的话，您可以知道如何设置它)。

* * *

# 概念打破！是时候回顾一下了！

我们已经走了这么远，却没有讨论核心的 MobX 概念，所以它们在这里:

*   可观察状态
*   行动
*   导数(反应和计算值)

在上面的例子中，我们已经使用了**可观察状态**以及修改这些状态的已定义的**动作**，并且我们已经使用了`mobx-react`的`@observer`来帮助将我们的 React 组件绑定到**对状态变化做出反应**。所以是四分之三。我们要检查计算值吗？

* * *

## 例 C:计算值

**计算值**本质上是没有副作用的**反应**。因为默认情况下[观察对象是懒惰的](https://github.com/ReactiveX/rxjs/blob/master/doc/observable.md)，所以 MobX 可以根据需要推迟计算。只要**可观测状态**更新，它们就会更新。换句话说，计算出的值是从可观测的状态中得到的。

让我们添加一个计算值，该值正好反转`text` :
中的内容

```
class State {
  text = "";
  get reverseText() {
    return this.text
      .split("")
      .reverse()
      .join("");
  }
  onChange = e => {
    // action
    this.text = e.target.value;
    fetchAllCaps(e.target.value).then(val => (this.text = val));
  };
}
decorate(State, { text: observable, reverseText: computed });
const appState = new State();

// lower down...
const Display2 = inject(["store"])(
  observer(({ store }) => <div>Display: {store.reverseText}</div>) ); 
```

*( [举例 C1](https://codesandbox.io/s/jnmz0v2189) ，[装修工 C1](https://codesandbox.io/s/qk933p79j4) )*

酷！它“就是管用”(TM)！

在考虑这个问题时，一个合理的问题是:**为什么要麻烦**？？我总是可以将同步业务逻辑放在我的 React `render`函数中，为什么要在 appState 级别计算值呢？

在这个小例子中，这是一个合理的批评，但是想象一下，如果你在你的应用程序中的多个位置依赖于相同的计算值。您必须到处复制相同的业务逻辑，或者将其提取到一个文件中，然后将其导入到任何地方。通过*将计算值定位在更接近状态*而不是更接近*视图*，计算值是建模状态派生的一个好方法。这是一个细微的差别，但可以在大范围内产生影响。

顺便说一下， [vue.js](https://vuejs.org/v2/guide/computed.html) 也有计算变量，而 [Angular](https://stackoverflow.com/questions/43710642/does-angular-have-the-computed-property-feature-like-in-vue-js) 只是隐式地使用它们。

## 举例 D1:可观测阵列

MobX 可以让任何东西变得可见。让我引用一下这些文件:

1.  如果值是 ES6 地图:将返回一个新的[可观察地图](https://mobx.js.org/refguide/map.html)。如果您不想只对特定条目的更改做出反应，也不想对条目的添加或删除做出反应，那么可观察地图非常有用。
2.  如果 value 是一个数组，将返回一个新的[可观察数组](https://mobx.js.org/refguide/array.html)。
3.  如果价值是一个没有原型的对象，那么它当前的所有属性都将是可观察的。见[可观察物体](https://mobx.js.org/refguide/object.html)
4.  如果 value 是一个带有原型、JavaScript 原语或函数的对象，将返回一个[装箱的可观察对象](https://mobx.js.org/refguide/boxed.html)。MobX 不会让带有原型的对象自动可观察；因为这是它的构造函数的责任。请在构造函数中使用 extendObservable，或者在其类定义中使用@observable。

在上面的例子中，到目前为止我们已经制作了[盒状的可观察物体](https://mobx.js.org/refguide/boxed.html)和[可观察物体](https://mobx.js.org/refguide/object.html)，但是如果我们想要制作一个可观察物体的数组呢？

[可观察阵列](https://mobx.js.org/refguide/array.html)是类似对象的阵列*，而不是实际的阵列。这可能会在背后伤人，尤其是在向其他库传递数据时。要转换成普通的 JS 数组，调用`observable.toJS()`或`observable.slice()`。*

但是大多数时候你可以把数组当作数组。这是一个非常简单的使用可观察数组的 Todo 应用:

```
class State {
  text = ["get milk"]; // observable array
  onSubmit = e => this.text.push(e); // action
}
decorate(State, { text: observable });
const appState = new State();

const Display = inject(["store"])(
  observer(({ store }) => (
    <ul>Todo: {store.text.map(text => <li key={text}>{text}</li>)}</ul>
  ))
);

const Input = observer(
  ["store"],
  class Input extends React.Component {
    render() {
      // reaction
      return (
        <form
          onSubmit={e => {
            e.preventDefault();
            this.props.store.onSubmit(this.input.value);
            this.input.value = "";
          }}
        >
          <input type="text" ref={x => (this.input = x)} />
        </form>
      );
    }
  }
);

const App = () => (
  <React.Fragment>
    <Display />
    <Input />
  </React.Fragment> ); 
```

*( [举例 D1](https://codesandbox.io/s/p0m2l15vm) ，[装修工 D1](https://codesandbox.io/s/o9y428q9zq) )*

注意“just `push`”就行！

## 举例 D2:可观测的地图

可观察的物体(我们在例子 A、B 和 C 中使用的)和[可观察的地图](https://mobx.js.org/refguide/map.html)有什么区别？嗯，普通的旧 Javascript 对象和 [ES6 地图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)之间的区别是一样的。我将引用 MobX 文档来解释何时在对象上使用地图:

> 如果您不想只对特定条目的更改做出反应，也不想对条目的添加或删除做出反应，那么可观察地图非常有用。

因此，如果我们想有一堆待办事项列表，我们可以添加新的待办事项列表，这是正确的抽象。因此，如果我们从 D1 的例子中取出那个应用程序，将其重命名为`TodoList`，并在`todolist.js`中做一些其他表面的调整，然后在`index.js`上，我们可以这样做:

```
// index.js
const connect = str => Comp => inject([str])(observer(Comp)); // helper function

const listOfLists = observable.map({
  Todo1: new TodoListClass(),
  Todo2: new TodoListClass()
  // observable map rerenders when you add new members
});
const addNewList = e => listOfLists.set(e, new TodoListClass());

const App = connect("lists")(
  class App extends React.Component {
    render() {
      const { lists } = this.props;
      return (
        <div className="App">
          <span />
          <h1>MobX Kanban</h1>
          <span />
          {Array.from(lists).map((k, i) => (
            <div key={i}>
              {/*Provider within a Provider = Providerception */}
              <Provider todolist={k}>
                <TodoList />
              </Provider>
            </div>
          ))}
          <div>
            <h3>Add New List</h3>
            <form
              onSubmit={e => {
                e.preventDefault();
                addNewList(this.input.value);
                this.input.value = "";
              }}
            >
              <input type="text" ref={x => (this.input = x)} />
            </form>
          </div>
        </div>
      );
    }
  }
); 
```

*( [举例 D2](https://codesandbox.io/s/x3pl9p042w) ，[装修工 D2](https://codesandbox.io/s/ryonwww274) )*

嘿，很快！我们有一个看板(一个可扩展的列表)！

[![kanban](img/cadbefe437f14f9238c9c6cff317cf92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VjwhytmQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6764957/37550034-5a31617a-295d-11e8-8799-60c6f57bde0a.gif)

这是由那个`listOfLists`的动态扩展能力实现的，它是一个可观察到的地图。老实说，你也可以使用数组来实现这一点，但是如果你有一个更适合演示可观察地图的用例，请在下面的评论中告诉我。

# 举例 E1: MobX 开发工具

Redux 开发工具是 Redux 价值的重要组成部分，所以让我们来看看 [MobX React 开发工具](https://github.com/mobxjs/mobx-react-devtools)！

```
import DevTools from 'mobx-react-devtools'; // npm install --save-dev mobx-react-devtools

// somewhere within your app...
        <DevTools /> 
```

*( [举例 E1](https://codesandbox.io/s/7m0xoy9336) ，[装修工 E1](https://codesandbox.io/s/oo2n902jr5) )*

您可以看到弹出的三个图标:

*   可视化重新渲染
*   审核依赖关系树
*   将所有内容记录到控制台(使用浏览器控制台，而不是代码打开控制台)

你不能进行时间旅行，但这是一套很好的工具，可以用来审计你的应用程序中任何意想不到的状态变化。

# 敬请期待...

有一个阻塞错误`mobx-dev-tools`和`mobx`4:【https://github.com/mobxjs/mobx-react-devtools/issues/86】T2，我会在错误修复后完成这个。

然而，与此同时，您可以了解如何显式定义`actions`,以便 MobX 可以将您的状态更改批处理到`transaction`中，这是一个很大的性能节省:

[https://mobx . js . org/ref guide/action . html](https://mobx.js.org/refguide/action.html)

请注意，我们如何能够在不使用`action` s - MobX 的情况下完成所有演示，MobX 有一个(很少)记录在案的严格模式(以前的`useStrict`，现在的`configure({enforceActions: true});`)——参见[MobX 4 文档](https://github.com/mobxjs/mobx/blob/gh-pages/docs/refguide/api.md#configure)。但是我们需要开发工具来真正展示我们的示例应用程序的好处。

## 致谢

这篇简介从 Michel Weststrate 的 egghead.io 课程中借用了很多代码和结构，但是为当前的 Mobx 4 API 更新了 2 年前的课程。我也要感谢我的雇主允许我在公共场合学习。

这里的例子是在[贾维德·阿斯克罗夫](http://twitter.com/askerovlab)、[纳德·达比特](http://twitter.com/dabit3)和[米歇尔](http://twitter.com/mweststrate)的帮助下完成的。

## 其他教程和进一步阅读

其他最新指南

*   [MobX + React Native](https://dev.to/satansdeer/react-native-mobx-tutorial---part-1--2df0)
*   [MobX + React 最佳实践](https://medium.com/dailyjs/mobx-react-best-practices-17e01cec4140)
*   [MobX 4 发布博文](https://medium.com/@mweststrate/mobx-4-better-simpler-faster-smaller-c1fbc08008da)

文件（documents 的简写）

*   [MobX 文档-常见陷阱和最佳实践](https://mobx.js.org/best/pitfalls.html)
*   [MobX 变更日志——对 v3 和 v4 的变更要非常小心](https://github.com/mobxjs/mobx/blob/master/CHANGELOG.md)
*   [官方 MobX+React 10 分钟指南](https://mobx.js.org/getting-started.html)

年长的

*   [一个开发者对 redux vs mobx 的优劣对比](https://dannyherran.com/2017/03/react-redux-mobx-takeaways/)
*   [Adam Rackis 对 MobX 的老评价](https://medium.com/@adamrackis/a-redux-enthusiast-tries-mobx-af675f468c11)

要探索的相关库

*   [MobX 状态树](https://github.com/mobxjs/mobx-state-tree)和关联的[博客文章](https://codeburst.io/the-curious-case-of-mobx-state-tree-7b4e22d461f)

## 投稿

我应该在本指南中包括哪些其他当前(< 1 年)资源？我犯了什么错误吗？下面让我知道！