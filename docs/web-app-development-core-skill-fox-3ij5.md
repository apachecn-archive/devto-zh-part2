# 像狐狸一样狡猾

> 原文：<https://dev.to/booyaa/web-app-development-core-skill-fox-3ij5>

*照片由[让·布莱克默](https://unsplash.com/photos/g8a9oCVI_TA)在 Unsplash* 上拍摄

*这是我的“写作业”博客系列的一部分，介绍性的文章可以在[这里](https://dev.to/booyaa/blogging-my-homework-3ohf)找到。*

购买者自负:围绕概念的任何错误或误解几乎肯定是我自己的，而不是我的员工(制造技术)。这毕竟是我自己的学习，在学习过程中确实会出现错误。

*9 月 26 日更新 1:*我通过了福克斯考核！
*9 月 26 日更新 26th】第一次嵌入 codepens，如果有人知道如何显示源代码而不是结果。给我写封短信！*

# Web 应用开发核心技能二级又名 Fox

这是继 Hedgehog 之后的下一个级别，这个核心技能的介绍文章可以在我之前的文章中找到，名为 [Web App 开发核心技能:Hedgehog -第一部分](https://dev.to/2018/web_app_dev_core_skill_hedgehog_part_one)。狐狸的具体可以在 learn.madetech.com 的[上找到](https://learn.madetech.com/core-skills/web-application-development-with-react/#fox)

与前一个级别不同，这个评估主要处理 React 开发的纯实践方面。我说主要是因为有一个关于`key`道具用途的问题。

## 假设

在此评估中，您将继续使用在 Hedgehog 中启动的应用程序。

### 故事书

作为我实际评估的一部分，我决定使用 [Storybook](https://storybook.js.org/basics/guide-react/) 来演示我的组件，同时在我的编辑器中显示代码。对于这篇博文，我决定使用 [codepen.io](https://codepen.io) ，因为我觉得展示代码示例比演示故事书更有代表性。

但是，恐怕我不会单独写一篇博文来赞美故事书的优点。真的挺神奇的！

## 变得诡计多端

该评估将让您在应用程序中创建组件，并将对您进行以下方面的评估:

*   包含没有任何属性或状态的组件
*   包含对`key`道具的使用
*   包含一个组件，该组件使用作为属性传入的函数
*   包含根据状态变化以不同方式呈现的组件
*   包含一个响应 onClick 事件而改变状态的组件
*   包含一个没有全局样式的组件
*   包含一个组件，该组件根据传入的以下属性类型进行不同的呈现:
    *   布尔型
    *   一根绳子
    *   `children`道具

有一个小的理论部分增加了对`key`道具用途的评估。我们将在演示“包含对`key`道具的使用”时解决这个问题。

现在，让我们将评估的每个部分作为一个单独的小节来处理！

### 关于示例代码的一句话

下面的所有示例代码都假设 HTML 有一个 id 为`root` :
的 div

```
<div id="root"></div> 
```

Enter fullscreen mode Exit fullscreen mode

您将看到的示例代码片段只是创建组件的 JSX 部分。完整的工作演示可以在 codepen 代码片段中找到。

### 包含一个没有任何道具或状态的组件

```
const element = <h1>Hello, World!</h1>;

ReactDOM.render(element, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/booyaa/embed/rZbMQj?height=600&default-tab=result&embed-version=2](https://codepen.io/booyaa/embed/rZbMQj?height=600&default-tab=result&embed-version=2)

### 包含一个使用的关键道具

属性如此重要的原因是，当你试图合并两组子组件时，它可以防止 React 试图改变所有子组件。如果你有 React dev 工具，如果你的孩子没有`key`道具，你会得到警告。

```
function NumberList(props) {
    const numbers = props.numbers;

    const listItems = numbers.map((number) =>
      <li key={number.toString()}>{number}</li>
    );

    return (<ul>{listItems}</ul>);
}

const numbers = [1, 2, 3, 4, 5];

ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/booyaa/embed/WgWaxQ?height=600&default-tab=result&embed-version=2](https://codepen.io/booyaa/embed/WgWaxQ?height=600&default-tab=result&embed-version=2)

参考

*   [列表和按键–反应](https://reactjs.org/docs/lists-and-keys.html)
    *   [索引作为一个键是一个反模式——Robin Pokorny——Medium](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)
    *   [协调–反应](https://reactjs.org/docs/reconciliation.html#recursing-on-children)

### 包含一个组件，它使用一个作为道具传入的函数

请参见下一节中的示例。

### 包含一个组件，该组件根据传入的以下属性类型进行不同的渲染

*   布尔型
*   一根绳子
*   儿童道具

```
class Printer extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    let childCount = React.Children.count(this.props.children);
    let message;

    if (childCount < 1) {
      message = (
        <span>js type: {typeof(this.props.value)}</span>
      );
    } else {
      message = (<span>not a js type: {this.props.children}</span>);
    }

    return (<h1>{message}</h1>);
  }
}

function Func() {
  return 1;
}

function Child() {
  return 'I am a child component!';
}

function App() {
  return (
    <div>
<Printer value="i am a string" /><br />
<Printer value={true} /><br />
<Printer value={Func} /><br />
<Printer><Child/></Printer>
    </div>
  );
}

ReactDOM.render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/booyaa/embed/rZbMoj?height=600&default-tab=result&embed-version=2](https://codepen.io/booyaa/embed/rZbMoj?height=600&default-tab=result&embed-version=2)

### 包含一个根据状态变化进行不同渲染的组件

这是无耻地从官方[反应](https://reactjs.org/docs/state-and-lifecycle.html)文件中窃取的！

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState( {
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>{this.state.date.toLocaleTimeString()}</h1>
        <h2>This component is changing based on it's state</h2>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/booyaa/embed/BOEqyg?height=600&default-tab=result&embed-version=2](https://codepen.io/booyaa/embed/BOEqyg?height=600&default-tab=result&embed-version=2)

### 包含一个响应 onClick 事件而改变状态的组件

```
class App extends React.Component {
  constructor(props) {
    super(props);

    this.state = { playing: false };
  }

  playPause = () => {
    if (this.state.playing) {
      this.setState({ playing: false });
    } else {
      this.setState({ playing: true });
    }
  };

  render() {
    const { playing } = this.state;

    return (
      <div className="app">
        <button onClick={this.playPause}>{playing ? "Pause" : "Play"}</button>
      </div>
    );
  }
}

ReactDOM.render(<App />, document.getElementById("root")); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/booyaa/embed/WgWgme?height=600&default-tab=result&embed-version=2](https://codepen.io/booyaa/embed/WgWgme?height=600&default-tab=result&embed-version=2)

参考资料:

*   [状态和生命周期–反应](https://reactjs.org/docs/state-and-lifecycle.html)

### 包含一个已经样式化但没有全局样式的组件

```
const divStyle = {
  color: 'blue'
};

function App() {
  return <h1 style={divStyle}>Inner Style Example</h1>;
}

ReactDOM.render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/booyaa/embed/NLmLEZ?height=600&default-tab=result&embed-version=2](https://codepen.io/booyaa/embed/NLmLEZ?height=600&default-tab=result&embed-version=2)

参考资料:

*   [DOM 元素–反应](https://reactjs.org/docs/dom-elements.html#style)

[![booyaa image](img/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 在博客上写我的作业

### 标记 Sta Ana Sep 16 ' 181min read

#study #learning #fullstack](/booyaa/blogging-my-homework-3ohf)[![booyaa image](img/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 做一只刺猬-第一部分

### 标记 Sta Ana Sep 18 ' 18 15 分钟读取

#learning #study #react #webappdev](/booyaa/web-app-development-core-skill-hedgehog---part-one-2144)[![booyaa image](img/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 做一只刺猬-第二部分

### 标记 Sta Ana Sep 21 ' 186min read

#learning #study #react #webappdev](/booyaa/web-app-development-core-skill-hedgehog---part-two-3p1i)