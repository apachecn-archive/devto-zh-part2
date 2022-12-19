# 在 React 中创建待办事项应用程序

> 原文：<https://dev.to/kris/create-a-todo-app-in-react-5a0h>

在本教程中，我们将使用 React 创建一个 ToDo 应用程序。它对初学者很友好，所以如果你所做的所有反应都是“你好，世界”，那也没关系。你可以[在这里](https://react-todo-rj.herokuapp.com)自己体验 app。它也嵌入在这篇文章的结尾。

[![](../Images/e4677f4dd9dfdd053248d8bec71b7f28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qoBGnZOk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i1.wp.com/i.gyazo.com/8b411b3e0e0a1ca7ac28858f194b06dc.gif%3Fssl%3D1)

### 跟随前进的工具

*   NodeJS 和 npm:从这里安装，如果你还没有
*   用`npm install create-react-app --global`安装 create-react 应用程序

现在，我们可以走了。

### 新建一个 react app

新的 react 应用程序是用

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

它将创建一个包含一些文件和文件夹的新文件夹`react-todo`。

#### react-todo 中的文件和文件夹

`package.json`包含应用程序使用的模块列表。每个模块执行一个功能或一组功能。

`node_modules`存储 package.json 中列出的所有包。

`src`包含所有 React 源代码文件。

`public`包含样式表和图片等静态文件。

使用`npm start`运行此应用程序

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

**你会看到一个欢迎页面……**

[![](../Images/6dc57b0317d9664f4dc59dc6738c8a98.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2u2gK0kX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/reactninja.io/wp-content/uploads/2018/06/react-welcome-screen.png%3Fresize%3D552%252C248%26ssl%3D1)

如说明所说，修改 App.js 进行修改。`App.js`从`index.js`文件渲染而来。将`App.js`视为所有其他组件的容器。

体验下面的 ToDo 应用程序。在输入框中输入任务，点击添加按钮添加到列表中。要从列表中删除，请单击要删除的待办事项。

转到`src`中的`App.js`文件。从返回中删除除父 div 之外的所有内容。它看起来像下面

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

在这个 return 语句中，所有组件都将放在 div 中。

### 创建用户界面

待办事项列表是你在上面经历过的表格和下面的显示。

我们在 src 目录下的 TodoList.js 文件中创建组件`TodoList`。

我们导入这是 App.js，这样我们就可以把它放在我们所说的 div 中。

[![](../Images/e70734ba6b7d9a709af17d5fc82165cb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nc5iwst1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/reactninja.io/wp-content/uploads/2018/06/import-todolist.png%3Fresize%3D964%252C216%26ssl%3D1)

此外，将 TodoList 放在 return 语句的 div 中。

[![](../Images/5f468eb8fbd677c5f22e89949380f680.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fhPYT7wL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/reactninja.io/wp-content/uploads/2018/06/todolist-app.png%3Fresize%3D964%252C408%26ssl%3D1)

TodoList 里有什么？

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

您可能会发现一些多余的 div，不要担心，我们一会儿就会填充它们。

该组件创建表单。

看起来是这样的…

[![](../Images/fadbdf1d5395b6a0f3e84d90ea8a5e93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VSWg4kZz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/reactninja.io/wp-content/uploads/2018/06/todo-starter-output.png%3Fresize%3D416%252C642%26ssl%3D1)

因为 CSS，你的输出不会和我的一样。我偷偷把 CSS 加到了`index.css`文件里。这是基本的东西，我们不会在这之后讨论样式表。如果你想让你的应用程序的风格像例子中一样，[从这里获取 index . CSS](https://github.com/therj/react-todo/blob/master/src/index.css)…

如果您尝试在此应用程序中添加待办事项，它只会重新加载页面。这是因为表单的默认行为是提交到同一个页面。

### 赋予 React Todo App 生命

我们已经有了一个毫无生气的 todo 应用程序，它除了显示自己什么也不做。

我们要做的是:

*   添加项目
*   显示项目
*   移除项目

### 添加物品

当提交表单时，输入项被提交。要处理此操作，请在 TodoList 中添加 onSubmit to form 标记。

[![](../Images/881114aa86e50b4894e93f95d6db7121.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NvGKJwMb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/reactninja.io/wp-content/uploads/2018/06/form-submit-add-item.png%3Fresize%3D964%252C216%26ssl%3D1)

这个`addItem`应该在 App 组件上处理。它作为道具传递给其他组件。

[![](../Images/d5bd777dc019e4bc80eb6461c41b3772.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ofJR9PPx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/reactninja.io/wp-content/uploads/2018/06/add-item-prop.png%3Fresize%3D1130%252C312%26ssl%3D1)

这必须存在于应用程序中才能通过。在`App`中创建一个`addItem`属性。

我们可以将它声明为一个旧的类似 JavaScript (ES5)的函数，但是它不会将表单与它绑定在一起。我们必须通过构造函数手动绑定它。我会用 ES6 之类的语法把它去掉。

我们仍然需要状态来保存项目数组。状态使得在页面上呈现和元素变得容易。当数据状态改变时，所有使用数据的组件将自动改变。

我们还需要另一个名为`currentItem`的状态来保存内存中的当前值。它是一个对象，除了文本，它还有一个键。React 在内部使用这个键，只在有多个相似组件时才呈现组件。没有关键字无法呈现待办事项列表，因为将会有所有的`li`。

将构造函数添加到`App`中。同样加上`addItem`和`handleInput`。

addItem 管理列表的添加，handleInput 管理输入字段的更改。

这是我的 App.js 的样子…

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

要获得输入元素，我们必须有一种方法来引用它。您可能会对使用 querySelector 感到兴奋，但是 React 不喜欢这样。虽然它完全有效，但虚拟 DOM 的思想不是直接与 DOM 交互，而是与 DOM 中的组件交互。

为了引用输入，我们用`inputElement = React.createRef()`创建了一个`ref`。把这个传给`TodoList`，就像传给`addItem`一样

`inputElement = {this.inputElement}`

将其作为`TodoList`中的`ref = {this.props.inputElement}`。

[![](../Images/528367f3bd01acbbc48039691a5419a6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4d5WhZ-l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i1.wp.com/i.gyazo.com/9908c98a865c6557a5436774adec78ef.gif%3Fssl%3D1)

如果你现在尝试这个应用程序，你可以看到它记录了来自 addItem 的消息，然后重新加载。重新加载是表单提交的默认行为。

若要停止此行为，请将 addItem 修改为以下内容。

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

`preventDefault`将防止提交表单时重新加载。

这是我们传递给 TodoList 的所有数据…

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

`addItem`要处理点击添加。

`inputElement`指代此元素。

`handleInput`处理输入字段上的数据变化

`currentItem`显示设置为 currentItem 的状态值。

下面是我的 TodoList.js 的样子…

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

一会儿我们会谈到 eh**component did update**…

`form`提交呼叫时`addItem`

`ref`指当前元素。

`value`作为文本存储在`currentElement`对象中。

如果组件中没有`onChange`，该字段将是只读的。我们不想这样。

`onChange`调用 handleInput，这是接下来要讨论的。

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

handleInput 获取事件，它从输入框中获取值，并将状态设置为和对象`currentItem`。它将 key 作为当前数据，将 text 作为输入数据。关键字是 Date.now()，它是从 1970 年到现在的毫秒数。每毫秒最多只能接受 1 次输入。这对我们的案子来说足够了。

我们需要这个对象，因为当用户提交表单时，我们需要将这个值存储到数组`items`中。

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

`addItem`防止默认重新加载。它从状态`currentItem`中获取输入框中的值。

因为我们不想在 todo 中添加空值，所以我们检查。如果不为空，则析构 items 数组并添加`newItem`。

我们必须将这个项设置为状态[]，我们称之为`this.setSate`。重置`currentItem`清除输入框也是有意义的。

**ComponentDidUpdate** 是 React 中的生命周期方法之一。[我们已经在这里讨论了所有这些问题](https://dev.to/kris/how-to-use-react-lifecycle-methods-41a8-temp-slug-3022603)。ComponentDidUpdate 被调用来关注由`inputElement`引用所引用的输入框。提交表单时会更新组件。`this.props.inputElement.current.focus()`在输入区域设置焦点，这样我们可以继续输入待办事项列表中的下一项。

### 显示待办事项

我们已经有了所有的 todos 状态，我们需要的是另一个组件，可以在屏幕上呈现这些。

我们将调用这个组件`TodoItems`并将所有项目作为道具传递。 [![](../Images/8bb70bde2721dc0abc2e581e6bc5c0c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PSsGdsgj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/reactninja.io/wp-content/uploads/2018/06/todo-items-prop.png%3Fresize%3D1234%252C216%26ssl%3D1)

下面是`TodoItems`的样子…

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

函数 createTasks 为每个传递的项目返回 li。它使用我们之前提供的密钥。在这个阶段，它不能与 key 一起工作，因为 React 必须能够区分多个项目，以重新渲染适当的项目。

使用映射功能将所有这些列表项保存到`listItems`。这在 return 语句中的`ul`中使用。

### 删除待办事宜

我们已经添加了待办事项，我们可能想要删除一些。

我们已经在`TodoItems.js`中显示了 todos，我们做了一个小的修改。只需添加一个 onClick 侦听器来删除带有键的 Item。

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

这将执行 deleteItem，并将键作为参数。道具要从`App`传过来。

`<TodoItems entries={this.state.items}deleteItem={this.deleteItem}/>`

在 App.js 中新建一个属性为`deleteItem`。

查看[要点](https://gist.github.com/therj/07c0c6a458b38ecb9e571f950335187c)上的代码。

它从`items`状态过滤接收到的密钥。然后将项目设置为已过滤的项目。

下面自己体验一下 app。

在 React 中创建待办事宜应用的帖子[最早出现在](https://reactninja.io/2018/06/26/create-todo-app-in-react/)[React inja](https://reactninja.io)上。