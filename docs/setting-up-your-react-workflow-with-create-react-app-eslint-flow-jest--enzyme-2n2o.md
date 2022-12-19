# 使用 Create React App、EsLint、flow、Jest & Enzyme 设置 React 工作流程

> 原文：<https://dev.to/gate3/setting-up-your-react-workflow-with-create-react-app-eslint-flow-jest--enzyme-2n2o>

React 太棒了，它让你有能力创建真正令人惊叹的高性能快速应用程序。但这并不是构建一个应用程序的全部，不是吗？在基于 React 构建了多个大型应用之后，我发现工作流和输出一样重要。有了一个好的工作流，维护将变得容易，错误将会减少，调试也将变得容易。

那么，我们如何才能充分利用这个令人惊叹的图书馆呢？当然是通过使用工具来优化我们的工作流程。正在讨论的工具是 Flow(用于静态类型)、EsLint(用于坚持良好的编码模式)、Jest 和 Enzyme(用于测试)。

# 流量

Javascript 是一种动态类型语言，React 也是(不用说)。这种动态性虽然方便，但也带来了许多错误检测和调试方面的问题。静态类型语言在编译时评估数据类型，在大多数情况下，您甚至可以在运行它们之前在代码编辑器中看到错误，而另一方面，动态类型语言只在运行时评估，这意味着您在程序试图运行后检测类型错误。

看看下面的代码片段

```
 const myObj = 2;
    // A lenghty stretch of code later... 
    myObj(); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段将导致一个错误，特别是“TypeError: myObj 不是一个函数”。如果这是一种静态类型的语言，您会更早地发现这个错误，并在运行之前修复它。虽然这是可能发生的事情的一个过于简单的版本，但是这个小问题有时会花费你很多时间。例如，如果这段代码直到程序的后期才运行，它很容易通过开发人员的初始测试，并在以后导致问题。

为了解决这个问题，我们使用静态类型检查器，在这个例子中是 Flow([https://flow.org/en/](https://flow.org/en/))。Flow 是 Javascript 的静态类型检查器，这意味着它像其他静态类型语言一样在编译时检查您的类型。

将心流整合到你的工作流程中可能一开始会很乏味，实际上有一点学习曲线，但是相信我，好处远远超过额外的努力。

将流应用到上面的代码片段

```
// @flow
const myObj = 2;
// A lenghty stretch of code later... 
myObj(); 
```

Enter fullscreen mode Exit fullscreen mode

Flow 将捕获此错误并在您的代码编辑器中显示信息，或者它也可以在您在 cli 中运行 flow 命令时捕获该错误。这里有一个流将在您的编辑器中输出的示例

[![](../Images/60ef40e27c5c9a4e1ceb81c3015e1599.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---xzDy6dk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/msp8vp79rzaa2bs9mcpa.png)

正如你所看到的，flow 告诉你它不是一个函数，甚至给你关于它是什么类型的进一步信息。使用心流会有所帮助

*   您可以更快、更自信地编码(因为您不需要在看到这些类型错误之前运行您的代码)。

*   更好地理解您的代码

*   在团队中工作得更好(解释更容易，你的代码库更容易理解)。

*   智能感知更好

# esLint

林挺的重要性怎么强调都不为过。林挺是一个代码分析工具，是白盒测试过程的一部分。单元测试将测试你的输出和一般程序行为，而林挺分析你代码的内部结构。

什么是林挺？林挺是检查代码中逻辑和风格错误的过程。Linters 确保你遵守编码标准，提供一致性，并向你显示可能的逻辑错误。linter 是一个程序，它通过遍历你的代码来执行这种分析。在团队中使用 Linter 可以使代码库看起来像是由一个人编写的。

有几个 Linters，但是我最喜欢的是 esLint，因为它有一套健壮的规则，还因为它非常灵活，易于配置。您甚至可以编写自己的代码库必须遵守的规则。

# 笑话和酶

为你的应用程序编写单元测试是一项极其重要的工作，幸运的是，我们有 Jest 和 Enzyme 来让这个过程变得非常简单(谢谢 facebook，谢谢 airbnb)。

[![](../Images/3ac3d063f9674ca8b35411eb9997d30d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hmHr_Ngp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://humorside.com/wp-content/uploads/2017/12/thank-you-meme-02-1.jpg)

尽管单元测试在 React 应用程序中很重要，但我看到很多人对此不屑一顾，我必须说这是一个错误。Jest 和 Enzyme 提供了出色的测试工具，如浅层渲染(只渲染组件，不渲染其子组件进行测试)、快照测试(将组件的渲染输出存储在文件中并与之进行比较，以确保组件不会发生变化)和开箱即用的代码覆盖率。

测试 React 组件可以像
一样简单

```
it('render <ComponentX /> without errors', () => {
    const wrapper = shallow(<ComponentX />);
    expect(wrapper).toMatchSnapshot();
});

// or with a function spy

it('call function on button click', () => {
    const funcToCall = jest.fn();
    const wrapper = shallow(<ComponentX callFunc={funcToCall}/>);
    const btn = wrapper.find('button');
    btn.simulate('click');
    expect(funcToCall).toHaveBeenCalled();
}); 
```

Enter fullscreen mode Exit fullscreen mode

当然，测试可能会变得更复杂，这取决于你想测试的内容，但是你已经大致了解了。Jest 是测试框架本身，它有一个任务运行器、断言框架和良好的模仿支持。另一方面，Enzyme 是一个库，它提供了一个更简单的接口来编写单元测试。

# 现在都在一起了

[![Image: The Oregon Symphony led by Carlos Kalmar Oregon Symphony](../Images/2878c7942f980d5fd7d7a388770fc574.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jBYLAJ8x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0m39wfgfy8ec0brwd3wn.jpg)

## 创建 React App

在这篇文章中，我将使用 [CRA(创建 React 应用)](https://github.com/facebook/create-react-app)，这是启动 React 应用最简单的方法。通过运行
为自己抓取一份副本

```
npx create-react-app <your app name > 
```

Enter fullscreen mode Exit fullscreen mode

通过 cli 进入该文件夹，安装其余的工具。

## 流量

流配置与您的 CRA 一起提供，但是您需要将 flow-bin 安装到您的工作空间中以便使用它(阅读关于 [Flow bin](https://github.com/facebook/flow#installing-flow-per-project) 的更多信息)。

要安装 Flow，请执行以下步骤:

*   运行 *npm install - D 流槽*安装流槽。
*   运行*。/node_modules/。bin/flow init* 创建一个新的。流程配置文件
*   将“flow”:“flow”添加到 package.json 的脚本部分。
*   运行*。/node_modules/。bin/flow* 看是否管用。您应该得到一个没有错误的响应。注意:为了使事情更简单，您应该通过运行 *npm i -g flow-bin* 来全局安装 flow。一旦这样做了，你就不需要*。/node_modules/。bin/flow* 你可以从你的命令行界面运行“flow”。
*   没有错误！消息出现是因为您尚未开始流式键入任何文件。要查看运行中的 flow，请在任一 js 或 jsx 文件的顶部添加// @flow，然后再次运行 flow。您将收到详细说明错误及其所在文件的消息。

## esLint

要开始使用 esLint，请执行以下操作

*   运行 *npm i -D eslint* 安装 eslint。
*   安装完成后，运行下面的命令*。/node_modules/。bin/eslint - init* 。(注意:同样，您可以通过运行 *npm i -g eslint* 来全局安装 eslint)。init 命令将询问您希望使用的林挺规则。你想创建你自己的还是想使用流行的编码风格

[![](../Images/ae460c747a7d14d38f536c6375bd2c62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--73s73yti--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lpfjsmrjl6mlfervnj50.png)

一个流行的选择，我通常使用的是 airbnb 风格。你还会被问到是否使用 React(显然)以及你想使用哪种配置文件类型(JSON、javascript 或 YAML)，我通常使用 Javascript。然后最后你会被要求安装 eslint 的依赖项，安装完成。

*   完成配置后，将为您生成一个 eslintrc.js(文件扩展名取决于您选择的配置文件类型)。您需要将以下命令复制到. eslintrc.js 文件中

```
// original file
module.exports = {
    "extends": "airbnb"
};

// change to this 
module.exports = {
    "extends": ["airbnb", "plugin:flowtype/recommended"],
    "env": {
        "jest": true
    },
    "parser": "babel-eslint",
    "plugins": [
        "flowtype"
    ],
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们差不多完成了，只差一步了。

## 笑话和酶

再次，CRA 背后的好人将 Jest 作为默认的测试运行程序([阅读更多](https://facebook.github.io/create-react-app/docs/running-tests))，但它没有安装酶。要安装酵素运行以下命令

```
npm install --save-dev enzyme enzyme-adapter-react-16 enzyme-to-json 
```

Enter fullscreen mode Exit fullscreen mode

然后通过添加
来更新 package.json 中的 jest 配置

```
"jest": {
    "snapshotSerializers": [
      "enzyme-to-json/serializer"
    ]
  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要为酶创建与 react 16 一起工作的配置。在 src 文件夹中创建一个名为 setupTests.js 的文件，以便我们。/src/setupTests.js 存在。CRA 会自己找到这个文件，但是如果你没有使用 CRA，可以通过添加 *"setupFiles": ["来更新 package.json 中的 jest 配置。/src/setupTests.js"]* 给它。将以下命令添加到 setupTests.js

```
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() }); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们都准备好了。如果一切顺利，您应该已经看到 eslint 用红色下划线修改了您的代码。

## 样本程序

让我们写一个简单的程序，它将被流类型化和单元测试。

假设我有这些组件

```
// App.jsx

import React, { Component } from 'react';
import './App.css';
import MyButton from './components/MyButton';

class App extends Component {
  constructor() {
    super();
    this.state = {
      count: 1,
    };
    this.countFunc = this.countFunc.bind(this);
  }

  countFunc() {
    this.setState({
      count,
    });
  }

  render() {
    const { count } = this.state;
    return (
      <div>
        <h1>{count + 1}</h1>
        <MyButton name="Click Me" countFunc={this.countFunc} />
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

和...

```
//MyButton.jsx
import React from 'react';

const MyButton = ({ name, countFunc }) => (
  <button type="button" onClick={() => countFunc(2)}>{name}</button>
);

export default MyButton; 
```

Enter fullscreen mode Exit fullscreen mode

实际上，它们都只是没有流类型的常规函数。该按钮将一个数字传递回应用程序组件，但如果由于某种原因改变了程序中断或失去意义(逻辑错误)。

```
// @flow

import React, { Component } from 'react';
import './App.css';
import MyButton from './components/MyButton';

type State = {
  count: number,
}

type Props = {}

class App extends Component<Props, State> {
  constructor() {
    super();
    this.state = {
      count: 1,
    };
    this.countFunc = this.countFunc.bind(this);
  }

  countFunc: (count: number)=>void

  countFunc(count: number) {
    this.setState({
      count,
    });
  }

  render() {
    const { count } = this.state;
    return (
      <div>
        <h1>{count + 1}</h1>
        <MyButton name="Click Me" countFunc={this.countFunc} />
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

和...

```
// @flow
import React from 'react';

type Props = {
    name: string,
    countFunc: (count: number) => void
};

const MyButton = ({ name, countFunc }: Props) => (
  <button type="button" onClick={() => countFunc(2)}>{name}</button>
);

export default MyButton; 
```

Enter fullscreen mode Exit fullscreen mode

这样可读性更好，如果类型改变，我们肯定会得到警告。

现在进行测试

```
// Very simple test to check if App Renders
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

describe('<MyButton />', () => {
  it('renders without crashing', () => {
    const wrapper = shallow(<App />);
    expect(wrapper.length).toEqual(1);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

和...

```
import React from 'react';
import { shallow } from 'enzyme';
import MyButton from './MyButton';

describe('<MyButton />', () => {
  it('Should render without crashing', () => {
    const wrapper = shallow(<MyButton />);
    expect(wrapper.length).toEqual(1);
  });
  it('Should render without crashing', () => {
    const mockCountFunc = jest.fn();
    const wrapper = shallow(<MyButton countFunc={mockCountFunc} />);
    const btn = wrapper.find('button');
    btn.simulate('click');
    expect(mockCountFunc).toHaveBeenCalled();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面对 MyButton 的测试只是测试 MyButton 是否成功呈现，还测试当按钮被单击时，它是否会调用传递给它的 countFunc 属性。

你可以在这里找到完整的代码[代码示例](https://github.com/gate3/React-WorkFlow)

# 结论

如果你像我一样使用 Vscode，那么需要采取额外的步骤来确保一切顺利进行。您需要让 eslint 允许您定义流类型。如果你自己安装了这个项目，你可能会遇到一个错误。ts 文件可以定义类型(或者类似的东西)。要使此错误消失，请打开您的设置(在 Mac 上，将单击代码菜单，前往首选项设置并切换到工作区设置)。打开工作区设置并添加该设置

```
"javascript.validate.enable":false 
```

Enter fullscreen mode Exit fullscreen mode

你已经准备好了。

最后，整个过程可能很难理解，可能会让人不知所措，但你会习惯的。作为一般的经验法则，我遵循这种模式。我写我的测试失败，让他们失败，我写我的流程类型，然后我写我的组件。然后，我调整组件，以适应前两个。

编码愉快，留下你的评论。:-)