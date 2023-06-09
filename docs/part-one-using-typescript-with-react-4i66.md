# 第一部分:在 React 中使用 Typescript

> 原文：<https://dev.to/ganderzz/part-one-using-typescript-with-react-4i66>

[![Edit React Typescript](img/0b3f0135583496627e3621355d8e9248.png)T2】](https://codesandbox.io/s/n3138x65p0?fontsize=14)

# 第一部分:使用带有 React 的类型脚本

用 React 学习 Typescript 可能很有挑战性，在我的情况下，诅咒微软并对每个变量抛出`any`。本系列的目标是记录我在 React 中开发应用程序时学到的东西，并努力尝试使用 Typescript。像许多事情一样，最初的学习曲线可能是惊人的，但是一旦齿轮开始转动，添加类型检查器就有回报了。*有一点需要注意:*这不是一篇关于设置打字稿的帖子。我们将假设一个 **tsconfig.json** 存在，并将我们的代码编译成有效的 JavaScript。

我们将通过创建一个计数器，并在需要的地方添加类型来完成第一部分。

### App

开始时，我们有一个应用程序的脚手架。

```
// Note, Typescript requires the whole React package to be imported.
// More information can be found: https://stackoverflow.com/a/37491916
import * as React from "react";

class App extends React.Component {
  public render() {
    return (
      <div>
        test
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来像是一个有效的 React 类组件，但是一些类型脚本的差异很明显。首先，因为 React lib 没有默认导出，Typescript 要求我们导入整个包(`import * as React from "react";`)。第二，所有的 React 方法都被定义为`public` ( *private 或 protected* 将不起作用)，如 **render** 方法所示。*我们可以去掉**public* *键盘，组件还是一样的工作。*我喜欢明确定义我的方法的范围，以帮助区分我的方法和 React 的方法。通常，我将我的方法定义为`private`，除非另有需要。这使得该方法的范围仅限于组件，并防止了不必要的副作用。

### 状态

我们需要一种存储计数器状态的方法。让我们实现这一点。

```
// Note, Typescript requires the whole React package to be imported.
// More information can be found: https://stackoverflow.com/a/37491916
import * as React from "react";

interface IState {
  count: number;
}

class App extends React.Component<{}, IState> {
  public readonly state = {
    count: 0
  };

  public render() {
    return (
      <div>
        {this.state.count}
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事情，所以我们将逐个分析。

##### 界面

```
interface IState {
  count: number;
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们创建一个新的[接口](https://www.typescriptlang.org/docs/handbook/interfaces.html)，它定义了组件状态的*形状*。

注意，作为一种实践，我们以大写字母**“I”**开始接口名称。这是借用了 OOP 语言，比如 C#，让我们更好的识别变量 *vs* 类型 *vs* 接口。

我采用的另一个实践是:在 PascalCase 中定义 Typescript 类型，在 camelCase 中定义 Javascript 变量。另一个有用的命名方案是防止尝试使用类型定义作为可执行代码！

接下来，我们将组件的状态定义为只有一个字段，count。在 Typescript 中，float `0.0001`和 int `10`没有区别。要说某样东西是“数状的”，我们给它的类型是`number`。

##### 类定义

```
class App extends React.Component<{}, IState> { 
```

Enter fullscreen mode Exit fullscreen mode

`React.Component`(也叫`React.PureComponent`)是通用类型，允许我们提供组件的道具和状态的*形状*。组件带有几个预定义的属性(`children`和`ref`等等)。因为 App 没有任何道具，我们就用一个空的对象。React Typescript 定义将我们传入的类型与默认组件类型相结合，因此即使是空对象，继承的组件属性仍然可用(例如 children 和 ref)。对于组件的状态，我们将告诉 Typescript 我们希望使用在 IState 中定义的状态形状。

把它拆开:

*   定义一个只有状态的组件:`React.Component<{}, IState>`
*   定义一个只有道具的组件:`React.Component<IProps>`
*   定义状态和道具:`React.Component<IProps, IState>`

##### 定义状态

```
public readonly state = {
  count: 0
}; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们定义组件的状态。记住，到目前为止我们只告诉了 Typescript 我们州的*形状*是什么。这是我们在 React 中定义其实际值的地方。因为 React 需要状态，所以我们将其定义为`public`。此外，因为我们不希望任何人直接改变状态，所以我们添加了`readonly`。每当我们试图将状态直接赋值给一个值(例如`this.state.count = this.state.count + 1; // Error!`)时，就会抛出一个 Typescript 错误。接下来，我们定义我们的公共 readonly 变量，使其名称为*状态为*，并为其分配一个与我们在 **IState** 中定义的形状相匹配的对象。因为我们用 **IState** 定义了`React.Component`作为我们的状态形状，所以 Typescript 知道状态应该有一个带数值的 **count** 字段。

### 添加事件

让我们通过添加一些按钮和一个减少或增加计数的 click 事件来完成我们的计数器。

```
// Note, Typescript requires the whole React package to be imported.
// More information can be found: https://stackoverflow.com/a/37491916
import * as React from "react";

interface IState {
  count: number;
}

class App extends React.Component<{}, IState> {
  public readonly state = {
    count: 0
  };

  private handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
    const type: string = event.currentTarget.title;

    this.setState(({ count }) => ({
      count: type === "decrement" ? count - 1 : count + 1
    }));
  };

  public render() {
    return (
      <div>
        <button title="decrement" onClick={this.handleClick}>
          -
        </button>
        {this.state.count}
        <button title="increment" onClick={this.handleClick}>
          +
        </button>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最大的变化是增加了一个新方法。

```
private handleClick = (event: React.MouseEvent<HTMLButtonElement>) => { 
```

Enter fullscreen mode Exit fullscreen mode

我们创建了一个名为`handleClick`的新的私有方法来处理我们的按钮点击事件。注意，由于 React 使用合成事件——我们必须使用 React 的一个类型。该事件由鼠标点击触发，因此我们将使用`React.MouseEvent`。**做出反应。MouseEvent** 是一个泛型类型，它接受触发事件的元素的类型。在我们的例子中，它是默认的 HTML 按钮元素(定义为`HTMLButtonElement`)。最后，我们根据按钮的标题增加或减少计数。

我们的计数器现在已经完成了类型脚本类型！

待续第二部分...