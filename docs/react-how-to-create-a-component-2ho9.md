# React:如何创建组件

> 原文：<https://dev.to/iam_timsmith/react-how-to-create-a-component-2ho9>

组件是任何 React 应用程序的重要组成部分。在这篇文章中，我们将学习如何创建组件来做我们想做的事情。

把组件想象成我们应用程序中的小块，我们可以在整个站点中重用它们。让我们假设我们正在创建一个社交网络，并希望在每个帖子下面放置一个赞、分享和评论按钮。我们可以在我们想要的每个区域写代码，但如果我们在 6 个不同的位置显示它并需要改变它，这是一个麻烦。

通过为此创建一个组件，我们可以编写一次实际的代码，然后把它放到我们想要的任何地方。

#### 创建一个类组件

为了创建一个类组件，我们必须给它一个名字。在下面的代码中，我们将把我们的类组件称为“Example”。组件的所有代码都将放在省略号(“...”)处)是。

```
import React from 'react';

class Example1 extends React.Component {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

那很简单！应该注意的是，如果这是它自己的文件中的一个组件，它将必须被导出。有两种方法可以做到这一点。我们可以在我们的类实例化之前包含“导出默认值”,或者我们可以简单地在导出它的类之后加一行。这方面的例子如下:

```
export default class Example2 extends React.Component {
  ...
}

or

class Example3 extends React.Component {
  ...
}
export default Example3; 
```

Enter fullscreen mode Exit fullscreen mode

#### 给类组件添加状态

类组件的另一个巨大好处是状态。正如我前面提到的，状态允许我们管理组件中的数据。状态是使用 React 的最大优势之一(尽管这个概念不是 React 特有的),为了使用它，我们需要一个类组件。

为了开始状态，我们需要向我们的类组件添加一个构造函数。你可以在这里阅读更多关于构造函数[的内容，但是现在只需要知道这是向我们的组件添加状态所必需的。通常我们看到“道具”作为构造函数的参数被传入，所以我们可以使用传入这个组件的任何道具。下面的例子说明了这一点。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/constructor) 

```
class Example4 extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      ...
    }
  }
}
export default Example4; 
```

Enter fullscreen mode Exit fullscreen mode

#### 用渲染方法渲染我们的 JSX

在构造函数之后，我们可以添加一个返回 JSX 的渲染方法。需要注意的是，在 React 中，返回(不管组件类型)只能返回一个直接元素。这意味着我们为创建该组件而编写的所有 JSX 都必须包装在一个外部元素中，比如一个

。下面的例子说明了这是如何建立的。

```
class Example5 extends React.Component {
  ...
  render() {
    return (
      <div>
        <p>Hello World!</p>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 自定义我们组件中的函数

类组件的另一个好处是能够编写在组件中使用的函数。这很简单。我们可以在构造函数和渲染方法之间写一个函数。我们可以像传递任何其他 JavaScript 函数一样传递任何必要的参数。不过，要做到这一点，还需要一个额外的步骤。在构造函数内部，我们需要将“this”关键字绑定到方法，这样它就可以在任何地方使用。为此，我们可以使用下面的代码:

```
 this.customFunction = this.customFunction.bind(this); 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章底部的一个例子将更好地说明这一点，这样你就可以更好地了解这些东西是如何结合在一起的。

#### 使用一个组件

如果我们将组件放在不同的文件中，我们首先需要将组件导入到我们想要使用它的文件中。我们可以用这个代码:

```
import Example6 from './path/to/component'; 
```

Enter fullscreen mode Exit fullscreen mode

导入后，我们可以使用导入时分配给它的名称将其添加到我们的 JSX 中。例如，我们可以添加上面导入的组件如下:

```
<Example6 /> 
```

Enter fullscreen mode Exit fullscreen mode

#### 道具

那么，如果我们想将数据传递到组件中，这样我们就不必在组件中硬编码任何东西或进行任何类型的查询，该怎么办呢？这就是道具的用武之地。道具是我们传递到组件实例中的数据片段，我们可以随意命名，这样我们就可以在组件中访问它。

假设我们想将字符串“Hello World”传递到组件中。当我们分配道具时，我们需要为道具取一个名字。这应该能告诉我们它到底是什么。由于这是一个演示，我将只使用“文本”,但在真正的应用程序中，它应该更具描述性。我们可以这样做:

```
<Example6 text="Hello World" /> 
```

Enter fullscreen mode Exit fullscreen mode

#### 接收道具内部组件

好了，我们已经把道具传入了组件，现在呢？我之前说过，当我们创建组件的构造函数时，我们通常会将道具放入参数中。这就是我们的组件接收我们传递给它的道具的方式。一旦到了那里，我们就可以在我们的 JSX 中使用 this.props.NameOfProp 来访问它们，如下所示:

```
<p>{ this.props.text }</p> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将在一个 p 标签中呈现“Hello World”。放松点，对吧？

#### 完整代码

```
// First we create our class
class Greeting extends React.Component {

    // Then we add our constructor which receives our props
    constructor(props) {
        super(props);
        // Next we establish our state
        this.state = {
            name: '',
            greeting: `Good ${this.props.time}, `
        }
        // To use the 'this' keyword, we need to bind it to our function
        this.onChange = this.onChange.bind(this);
    }

    // A custom function to change the name in our state to match the user input
    onChange(e) {
        this.setState({
            name: e.target.value
        })
    }
    // The render function, where we actually tell the browser what it should show
    render() {
        return (
            <div>
                <section className="section">
                    <label className="label">Name:</label>
                    <input className="input" name="name" placeholder="Enter your name..." onChange={this.onChange} />
                </section>
                <section className="section">
                    <p>{this.state.greeting} {this.state.name}</p>
                </section>
            </div>
        )
    }
}

ReactDOM.render(<Greeting time="morning" />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

您还可以在这里看到运行中的组件[。](https://codepen.io/iamtimsmith/pen/xaRydm/?editors=0010)

### 结论

虽然这些只是基础，但是在 React 中构建东西，它们会让你走得很远。尝试摆弄东西，将道具传递到新组件中，或者向组件添加状态以呈现数据。这些是 React 的基础构件。

另外，我很想看看你用 React 构建的东西，所以请在下面的评论中留下它们吧！