# 学习 React——用 Typescript 控制组件状态

> 原文：<https://dev.to/jonhilt/learning-react---exercise-control-over-your-component-state-with-typescript-2ai>

上一篇文章给我们留下了一点问题。

尽管我们的用户组件从 ASP.NET API 中检索和显示数据，但它也会在真正的数据显示出来之前闪现一些硬编码的数据。

[![](../Images/e400252e356d888af8c4aabc4b401d75.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6OwDPzGN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-3/ReactFlicker.gif)

看起来我们可以删除硬编码的数据，让我们试试...

```
 public state = {
        "users": []
    }; 
```

Enter fullscreen mode Exit fullscreen mode

看似合理，除了一个问题，不编译。

是的，没错，javascript 现在被编译了！在这种情况下，我们会遇到一个错误。

[![](../Images/6d4ab20208ef3cfbf143f977311c48e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0mWuQVsU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-4/2018-07-12-16-12-50.png)

起初这看起来很奇怪，但它以前是有效的。

结果是，当我们有了硬编码的数据时，Typescript 能够从这些数据的结构中推断出数据的类型。

```
public state = {
    "users": [
        { "id": 1, "name": "Jon Hilton", "summary": "36 / Lead Developer" },
        { "id": 2, "name": "Janine Smith", "summary": "32 /  Senior Engineer" }
    ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

因此，因为该数据包含一个 id 属性，所以一切都“正常工作”。

现在我们没有任何数据，编译器没有任何东西可以启动，所以当它运行到这一行的时候就爆炸了...

```
{this.state.users.map(user => <UserRow key={user.id} user={user} />)} 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，如果我们不再懒惰，而是为我们的状态定义类型，这就不是问题了。

```
export interface IState {
    users: IUser[];
}

export interface IUser {
    id: number,
    name: string,
    summary: string
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以声明组件状态的类型...

```
export default class MyUsers extends React.Component<any, IState>{ 
```

Enter fullscreen mode Exit fullscreen mode

最后，在我们设置初始状态的地方调整代码(以指示类型)。

```
public state: IState = {
    "users": []
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都编译好了，而且我们的好处是消除了状态的模糊性。

您将在 Visual Studio 代码中获得适当的智能感知，并且周围的不确定性更少！

在我们继续之前，如果你想看看这个系列的源代码，请点击这里:-)

## 装载...

这已经删除了我们的数据闪存，但如果我们想在数据被检索时显示一个加载指示器呢？

如果我们正在加载数据，我们需要一种方法来呈现不同的内容，然后在数据可用时显示列表。

与 React 中的所有东西一样，如果我们可以在进行 API 调用时更改底层状态，用户界面可以对此做出反应，并显示某种加载指示器。

我们可以给 IState 添加一个`loading`布尔值。

```
export interface IState {
    loading: boolean;
    users: IUser[];
} 
```

Enter fullscreen mode Exit fullscreen mode

首先将其设置为 false。

```
public state: IState = {
    loading: false,
    "users": []        
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后，当我们从后端加载数据时，翻转此标志。

```
public async componentDidMount() {
    this.setState({ loading: true }); // toggle on
    const result = await fetch('https://localhost:44348/api/user');
    const users = await result.json();
    this.setState({ users, loading: false }); // toggle off
} 
```

Enter fullscreen mode Exit fullscreen mode

最后让我们的 html 对此做出反应，并呈现某种加载指示器(当`state.loading`为真时)。

```
<h1>My Users</h1>
{this.state.loading && <div>Loading...</div>} // add this
<table className="user-list">
    <tbody>
        {this.state.users.map(user => <UserRow key={user.id} user={user} />)}
    </tbody>
</table> 
```

Enter fullscreen mode Exit fullscreen mode

当你第一次看到它的时候，它看起来有点奇怪，所以让我们来分析一下。

```
{this.state.loading && <div>Loading...</div>} 
```

Enter fullscreen mode Exit fullscreen mode

如果`this.state.loading`评估为真，这将显示加载 div。

作为一名 C#开发人员，你会习惯更经典的`if`语句。

那么这种`&&`疯狂是什么？！

原来，在 javascript 中，`&&`逻辑运算符返回一个指定操作数的值。

如果你有这样的表情。

```
const isSunday = true;
const result = isSunday && 'It\'s Sunday'; 
```

Enter fullscreen mode Exit fullscreen mode

结果会是“今天是星期天”。

```
const isSunday = false;
const result = isSunday && 'It\'s Sunday'; 
```

Enter fullscreen mode Exit fullscreen mode

这将返回 false！

这里的表格比我希望的解释得更好！

[Javascript 逻辑运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators)

[![](../Images/93b8b16f1bc0f5ceff50e3084748d392.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T6DQM93X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jonhilton.net/img/diary-of-a-net-developer-part-4/2018-07-12-17-03-51.png)

所以最后，这只是“如果`this.state.loading`为真，则显示加载指示器”的一种简洁的说法。

## 你的状态打出来了！

虽然到处使用`any`来避免声明类型很有诱惑力，但是这种捷径最终会使一些事情变得更加困难，并且从一开始就取消了使用 Typescript 的好处。

创建您的类型，使用条件呈现，您的组件将以一种可预测的方式做出反应，完全基于组件的底层状态。

**记住，如果你想看看这个系列的源代码，你可以[点击这里:-)](https://jonhilton.net/devto/react-source/)**

*图片来源:Pranav Bhatt [标志，南非](http://www.flickr.com/photos/58403098@N08/39463590482)途经 [photopin](http://photopin.com) [(许可证)](https://creativecommons.org/licenses/by-nc-sa/2.0/)*