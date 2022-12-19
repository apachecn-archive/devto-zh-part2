# React 搜索栏让我们在 React 中建立一个搜索栏！

> 原文：<https://dev.to/iam_timsmith/lets-build-a-search-bar-in-react-120j>

本文原文可在[这里](https://www.iamtimsmith.com/blog/lets-build-a-search-bar-in-react)找到。

我知道，我知道...另一个任务应用...

请听我说完！我们将建立一个任务应用程序，它也可以根据搜索查询实时过滤列表。听起来很复杂？这并不像你想象的那么复杂，所以让我们深入研究一下吧！

> **在我们开始之前有一个小提示:**
> 我将使用 Parcel 作为捆绑器。它非常棒，超级
> 容易设置。我有另一篇关于用包裹
> 建立一个项目的文章，它会给你更多关于建立的信息，所以如果我在这里快速浏览了一下
> ，我会推荐在这里查看一下。

### 建立我们的档案

首先，我们将创建我们的目录并使用命令行输入它。为此，打开您的终端并导航到您想要放置项目的目录。在那里，使用下面的代码行为我们的项目创建目录并输入它。

```
mkdir search-tasks && cd $_ 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在我们的项目文件夹中，我们需要用 yarn 或 npm 初始化我们的项目。我将在这个项目中使用 yarn，但是 npm 命令几乎是一样的。

```
yarn init -y 
```

Enter fullscreen mode Exit fullscreen mode

我们将只使用`-y`标志，这样它会自动为我们配置。我们很快就会进去修改`package.json`文件。

现在我们有了一个`package.json`文件，我们应该创建我们的`index.html`和`app.js`文件。您可以在终端中使用下面的代码行同时创建这两个文件。

```
touch index.html app.js 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们需要打开我们的`index.html`文件进行编辑，并将下面的代码放入其中:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Search To-Do App
</head>
<body>
  <div id="app"></div>
  <script src="./app.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

[![Add packages to our project](img/27279fbf86e4df1936bbe887672dad56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gva6JXca--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d7j7e1fshxeaaxe1j9qo.jpeg)

### 向我们的项目添加包

接下来，我们需要将必要的包安装到我们的项目中。在这种情况下，它将是反应，反应 DOM，包裹，巴别预设环境，巴别预设反应，和布尔玛。要将这些添加到我们的项目中，您可以使用 NPM 或纱线。我将提供两者的代码，所以你可以选择你更舒服的。

```
npm install react react-dom parcel babel-preset-env babel-preset-react bulma --save-dev

or

yarn add react react-dom parcel babel-preset-env babel-preset-react bulma 
```

Enter fullscreen mode Exit fullscreen mode

#### 这些是做什么的？

NPM 和纱是包管理器，允许您添加预先编写的代码到您的项目中。这可以极大地加快开发时间。下面你会发现每个包的功能的简要描述。

*   React:一个加速开发的库(对于 React 教程来说似乎是显而易见的，对吗？)[链接](https://reactjs.org/)
*   React-DOM:一个允许 React 在浏览器中与 DOM 交互的库。[链接](https://reactjs.org/docs/react-dom.html)
*   包:一个不需要配置的捆绑库。[链接](https://parceljs.org/)
*   Babel-preset-env:这个库告诉 Parcel 如何将 ES6 转换成与许多不同的浏览器兼容。[链接](https://babeljs.io/docs/en/babel-preset-env.html)
*   一个告诉包如何处理 JSX 的库。[链接](https://babeljs.io/docs/en/babel-preset-react)
*   布尔玛:一个使用 flexbox 的 CSS 框架，简单易用。[链接](https://bulma.io/)

### 设置 package.json 和。巴伯尔克

在我们真正开始构建 React 项目之前，我们需要添加一个`.babelrc`文件来包含我们安装的 babel-presets。首先，使用代码
创建文件

```
touch .babelrc && open $_ 
```

Enter fullscreen mode Exit fullscreen mode

进入文件后，我们将添加以下代码来包含已安装的预设。

```
{
  "presets": ["env", "react"]
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了。babelrc 文件设置完成后，我们需要将启动脚本添加到 package.json 文件中，所以请打开它。在该文件中，添加以下代码:

```
"scripts": {
    "start": "parcel index.html"
}, 
```

Enter fullscreen mode Exit fullscreen mode

### 设置 app.js 文件

还和我在一起吗？太好了！下一步是在我们的`app.js`文件中建立一个组件。我们将使用状态来管理我们的列表，所以我们需要为此使用一个类组件。首先，让我们导入构建应用程序所需的库。

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';
import 'bulma/bulma'; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们就可以创建一个 App 组件:

```
class App extends Component {
  render() {
    return(
      ...
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们需要确保我们的组件呈现到 DOM。为此，我们将使用 React DOM。

```
ReactDOM.render(<App />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以添加我们的构造函数和状态。我们将在状态中创建一个“列表”数组。首先，我们将用几个条目填充它，这样我们就可以看到我们的列表:

```
class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      list: [
        "Go to the store",
        "Wash the dishes",
        "Learn some code"
      ]
    }
  }
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

厉害！现在我们已经将列表置于 App 组件的状态，让我们来显示这个列表。我用布尔玛来设计我的风格，但是你可能会用一些不同的东西。这很酷，你只需要相应地调整你的课程。

```
class App extends Component {
  ...
  render() {
    return (
      <div className="content">
        <div className="container">
          <section className="section">
            <ul>
              {this.state.list.map(item => (
                <li key={item}>{item}</li>
              ))}
            </ul>
          </section>
        </div>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 上面的代码在做什么？

我们需要呈现我们的列表。为了做到这一点，我们使用了一些布尔玛类来帮助给事情一点空间。重要的部分发生在`<ul>`上。首先，我们创建了用于显示列表的`<ul>`。然后我们将使用花括号来避免 JSX，并使用一个叫做`.map()`的 javascript 函数。我们用`this.state.list`得到我们在 state 中制作的列表，并在它的末尾添加`.map()`。然后，我们传递一个回调函数(在本例中，我们使用了一个箭头函数)来返回我们想要显示的 JSX。

`.map()`函数的工作方式与`foreach`相似，因为它遍历数组中的每一项。我们传递给回调函数的参数(在本例中是`item`)将代表循环的每次迭代中的项目。在返回内容中，我们将创建一个`<li>`，它将显示的文本将是`item`，或者是列表数组的当前索引中的文本。

#### 我们得到了什么？

如果我们回到我们的终端，输入`yarn start`或`npm run start`，我们可以在浏览器中进入`localhost:1234`，看到我们制作的待办事项列表，显示为无序列表。现在让我们允许用户向列表中添加待办事项。

### 向列表中添加项目

这将非常简单。首先，我们需要添加代码来呈现一个输入框和一个提交按钮。我们渲染组件的完整代码现在应该是这样的:

```
<div className="content">
  <div className="container">
    <section className="section">
      <ul>
        {this.state.list.map(item => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </section>
    <hr />
    <section className="section">
      <form className="form" id="addItemForm">
        <input
          type="text"
          className="input"
          id="addInput"
          placeholder="Something that needs ot be done..."
        />
        <button className="button is-info" onClick={this.addItem}>
          Add Item
        </button>
      </form>
    </section>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加功能添加物品

现在我们有了一个输入和按钮，我们需要让它做一些事情。否则我们的用户根本无法更改列表。为此，我们需要向组件添加一个名为`addItem()`的函数，该函数位于构造函数之下，render 方法之前。我们需要它在我们点击按钮时运行。单击时，它应该接受输入中的文本，并查看它是否为空。如果它有文本，我们将把它添加到我们状态中的数组，然后这个数组将更新我们呈现的页面。下面的函数将为我们的输入添加必要的功能:

```
addItem(e) {
    // Prevent button click from submitting form
    e.preventDefault();

    // Create variables for our list, the item to add, and our form
    let list = this.state.list;
    const newItem = document.getElementById("addInput");
    const form = document.getElementById("addItemForm");

    // If our input has a value
    if (newItem.value != "") {
      // Add the new item to the end of our list array
      list.push(newItem.value);
      // Then we use that to set the state for list
      this.setState({
        list: list
      });
      // Finally, we need to reset the form
      newItem.classList.remove("is-danger");
      form.reset();
    } else {
      // If the input doesn't have a value, make the border red since it's required
      newItem.classList.add("is-danger");
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经构建了我们的函数，但是它不知道何时运行或者如何解释`this`关键字。我们可以在构造函数中用下面的代码告诉 react 如何处理这个问题:

```
this.addItem = this.addItem.bind(this); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以给我们的按钮添加一个 onClick 触发器，这样我们的按钮看起来应该是这样的:

```
<button className="button is-info" onClick={this.addItem}>
  Add Item
</button> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在浏览器中使用`yarn start`或`npm run start`并转到`localhost:1234`来测试我们的应用程序。我们的应用程序现在允许我们在列表中添加一个项目！相当酷！

[![Add a delete button](img/f60760d7b122a164050ff9e3fff90656.png)T2】](https://i.giphy.com/media/vohOR29F78sGk/giphy.gif)

### 添加删除按钮

好了，现在我们的用户可以添加项目了，但是如果他们一旦完成就不能删除它们，这又有什么用呢？他们只会有一个接一个的项目，直到熵值达到他们焦虑水平的顶峰，让他们早早进入坟墓。让我们通过添加一个删除按钮来拯救一些生命，好吗？

和以前一样，我们将添加一个函数来处理这个问题。下面的代码将允许我们的用户在完成后删除他们的列表项:

```
removeItem(item) {
    // Put our list into an array
    const list = this.state.list.slice();
    // Check to see if item passed in matches item in array
    list.some((el, i) => {
      if (el === item) {
        // If item matches, remove it from array
        list.splice(i, 1);
        return true;
      }
    });
    // Set state to list
    this.setState({
      list: list
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加到构造函数中

我们还需要将这个函数添加到构造函数中。就像之前一样，我们可以这样做:

```
this.removeItem = this.removeItem.bind(this); 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加按钮删除项目

为了方便用户删除条目，我们应该在`<li>`中添加一个删除按钮。下面的代码会做到这一点。

```
...
<ul>
  {this.state.list.map(item => (
    <li key={item}>
      {item} &nbsp;
      <span
        className="delete"
        onClick={() => this.removeItem(item)}
      />
    </li>
  ))}
</ul>
... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在终端中运行`yarn start`或`npm run start`来查看我们的更改。现在，我们可以单击 x 从列表中删除该项目。成功了吗？

### 将列表变成组件

咻！到目前为止，一切顺利。

接下来，我们要把我们的列表变成一个有自己的状态和方法的组件。为了简单起见，我将在我们的 app.js 文件中创建这个组件，但是您也可以在一个单独的文件中创建这个组件并导入它。在 App 组件下面，用下面的代码创建一个名为 List 的类组件:

```
class List extends React.Component {
    render() {
        return (
            <div>
            ...
            </div>
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们想要呈现的代码只是我们的列表，所以回到我们的应用程序组件，抓取下面的代码粘贴到我们的列表组件的呈现函数中:

```
<ul>
  {this.state.list.map(item => (
    <li key={item}>
      {item} &nbsp;
      <span
        className="delete"
        onClick={() => this.removeItem(item)}
      />
    </li>
  ))}
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

将 App 组件中代码替换为对 List 组件的调用，如下所示:

```
<List items={this.state.list} delete={this.removeItem} /> 
```

Enter fullscreen mode Exit fullscreen mode

#### 上面的代码在做什么？

在这里，我们调用列表组件并传入一些属性。道具正在发送我们存储在州中的列表。`delete`属性传入我们创建的用于删除项目的`removeItem`方法。

在它按预期工作之前，我们需要稍微修改一下我们的列表组件。首先，我们需要添加构造函数，这样我们就可以接收道具。

```
class List extends React.Component {
    constructor(props) {
        super(props);
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们用`npm run start`或`yarn start`运行应用程序，应用程序看起来应该和以前一样。我们仍然可以毫无问题地将项目添加到列表中。如果我们点击删除按钮...哦哦...它不起作用。这是为什么呢？

我们在这个组件中没有名为`removeItem`的方法，所以单击按钮不会调用任何东西。幸运的是，我们有先见之明，将该方法作为道具传递到这个组件中。要重新获得删除功能，我们只需将该按钮的代码修改如下:

```
<span className="delete" onClick={() => this.props.delete(item)} /> 
```

Enter fullscreen mode Exit fullscreen mode

因此，经过一些调整，我们现在在一个单独的组件中有一个功能齐全的列表。现在，继续添加搜索功能。

### 在列表中创建过滤项

添加搜索栏的第一部分是创建一个过滤列表的数组。如果输入栏是空的，它应该显示列表中的所有项目。如果搜索栏中有文本，它应该只显示包含该文本的项目。

首先，我们将状态添加到列表组件中，并给它一个名为 filtered 的数组。下面的代码说明了这一点。

```
class List extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            filtered: []
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了放置过滤列表的地方，我们需要确保数据被放在那里。

> 公平的警告:这部分可能有点抽象，所以尽你所能坚持我的观点。如果我解释得不够好或者你正在纠结，请留下评论，我会尽力帮你理解。

我们最初的任务列表位于 App 组件中，在本例中是父组件。这个状态被传递到列表组件中，在本例中是子组件，每次任务列表更新时都会被重新呈现。你问，告诉你这些有什么意义？每当列表组件被重新呈现时，我们需要将数据传递到我们的`filtered`状态中。为此，我们将使用一些生命周期方法。

生命周期方法允许我们在渲染过程的不同点“挂钩”到一个组件。在这种情况下，我们将使用`componentDidMount`和`componentDidReceiveProps`。`componentDidMount`将允许我们在最初渲染组件时将数据放入我们的`filtered`数组。另一方面，`componentDidReceiveProps`将在传递给组件的属性发生变化时触发。

要将这些生命周期方法添加到我们的列表组件中，请在构造函数下方、呈现函数之前添加以下代码:

```
componentDidMount() {
  this.setState({
    filtered: this.props.items
  });
}

componentWillReceiveProps(nextProps) {
  this.setState({
    filtered: nextProps.items
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们改变我们的列表使用的`.map()`函数来映射到`filtered`列表，而不是通过 props 传递的`items`列表，我们应该在前端看到同样的事情。

[![Whoop-dee-doo](img/cf73deb45ae16fc512a25b4108feda0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nw4lAD6a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/0abd7b24a1f28196cbde3c081aae20dd/tenor.gif)

有什么大不了的？重要的是，现在我们有了一个列表，我们可以在不改变原始列表的情况下操作它。我们所要做的就是修改我们的`filter`状态，显示的项目也会反映出来，但是我们这样做并没有丢失原来的列表。

### 创建搜索栏本身

在我看来，从搜索栏开始是一个好地方...良好的...搜索栏。让我们继续创建它。在列表组件的 div 包装器内部，我们添加一个输入。

```
<div>
    <input type="text" className="input" placeholder="Search..." />
    <ul>
    ...
    </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

酷！现在我们有一个搜索栏。要是它真的有用就好了...

### 使搜索栏搜索

我们有一个好看的搜索栏，但是它除了看起来很漂亮之外，并没有真正做什么。也许这已经足够好了，但是我认为生活不仅仅是长得非常非常非常好看。让我们加上“大脑”。

[![Really Really Ridiculously Good Looking](img/45fed45085472a4809b9e4cb5d4bcc1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P5z9rUFq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/acfdeea24b2907dc5aab376551b69b22/tenor.gif%3Fitemid%3D3547407)

首先，我们将在生命周期方法之后添加一个名为`handleChange`的方法。我们将把`e`作为代表事件的参数传入。在方法内部，我们将创建两个变量，它们将保存作为 props 传入的原始任务列表，以及传递到 state 之前的过滤列表。

我们还需要添加一个 if 语句，以便`.filter()`函数只在输入不为空时运行。否则空的搜索栏不会显示任何任务。因此，如果搜索栏不为空，我们希望运行`.filter()`函数，看看当前条目是否包含搜索词。如果是，那么我们将把该项返回给新列表数组。

```
handleChange(e) {
        // Variable to hold the original version of the list
    let currentList = [];
        // Variable to hold the filtered list before putting into state
    let newList = [];

        // If the search bar isn't empty
    if (e.target.value !== "") {
            // Assign the original list to currentList
      currentList = this.props.items;

            // Use .filter() to determine which items should be displayed
            // based on the search terms
      newList = currentList.filter(item => {
                // change current item to lowercase
        const lc = item.toLowerCase();
                // change search term to lowercase
        const filter = e.target.value.toLowerCase();
                // check to see if the current list item includes the search term
                // If it does, it will be added to newList. Using lowercase eliminates
                // issues with capitalization in search terms and search content
        return lc.includes(filter);
      });
    } else {
            // If the search bar is empty, set newList to original task list
      newList = this.props.items;
    }
        // Set the filtered state based on what our rules added to newList
    this.setState({
      filtered: newList
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

> 使用`.contains()`时，区分大小写。这可能会使用户在我们的任务中搜索变得更加困难，因为他们必须确切地知道项目使用的大小写。为了解决这个问题，我们可以将函数中的所有内容都改为小写，这样我们就知道在进行搜索时所有内容将使用什么大小写。上面的评论也说明了这一点。

### 向输入中添加方法

我们很接近了！在使用`handleChange()`方法之前，我们需要将`this`关键字绑定到它。在我们的构造函数中，在 state 之后，添加下面的代码来绑定我们的`this`关键字。

```
this.handleChange = this.handleChange.bind(this); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以向输入项添加一个事件处理程序，以便在内容发生变化时调用该方法。这最后一块将是真正使搜索功能的工作。将`onChange={this.handleChange}`添加到输入元素，使其看起来像这样:

```
<input type="text" className="input" onChange={this.handleChange} placeholder="Search..." /> 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

运行应用程序现在应该允许您创建、删除和搜索任务。这里有很多文本，但实际上并没有那么复杂。

这对你有帮助吗？如果你遇到任何问题，让我知道，我会得到这个教程更新。我还添加了 codepen，下面是完整的代码，所以你可以用它来玩或者比较代码。

本文原文可在[这里](https://www.iamtimsmith.com/blog/lets-build-a-search-bar-in-react)找到。

[https://codepen.io/twhite96/embed/zJPzwN?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/zJPzwN?height=600&default-tab=result&embed-version=2)