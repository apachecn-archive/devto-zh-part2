# 使用 react-testing-library 正确测试 react 组件

> 原文：<https://dev.to/bahdcoder_47/testing-react-components-the-right-way-with-react-testing-library-5h8d>

几天前 [Kent C. Dodds](https://twitter.com/kentcdodds) 发布了一个用于测试 React 组件/应用的测试包(React-Testing-Library)。该软件包是基于鼓励良好测试实践的原则而创建的。

> 你的测试越像你的软件被使用的方式，它们就越能给你信心。

编写 react 测试已经变得复杂和具有挑战性，因为测试实现细节的一般原则超过了可工作性和用户交互和界面。这个库是基于用户如何与应用程序交互来测试应用程序的，而不仅仅是基于功能是如何实现的。

看看这两个测试套件，了解它们的区别:

##### 测试实现细节的心态:

```
 test('todo should be set on state when input changes')
test('a list of todos should be set on state when component mounts')
test('the addTodo function should be called when user clicks createTodo button') 
```

##### 测试软件如何真正工作的心态:

```
 test('clicking on the add todo button adds a new todo to the list')
test('gets todos when component is mounted and displays them as a list')
test('should show todo successfully created notification for two seconds when todo is created') 
```

正如您在测试套件中注意到的，这个包鼓励编写更多的集成测试，这将极大地提高您部署应用程序时的信心。

例如，我们对如何呈现待办事项列表并不感兴趣，我们感兴趣的是用户能够看到待办事项列表，这是我们要测试的。我们也不想担心组件状态如何管理对输入文本字段所做的更改，但是我们关心用户的体验，这也是我们将要测试的。

### 背景:我们将测试的应用程序:

我们将在这里为托管在[的 todos CRUD 应用程序编写一些测试。](https://bahdcasts-react-crud.netlify.com)

以下是应用程序提供的功能列表:

*   安装组件时显示 api 中的待办事项列表
*   添加，编辑和更新待办事项。
*   显示执行的不同操作的通知。

我们将为以下内容编写测试:

*   装载组件时显示 api 中的待办事项列表
*   添加待办事项

应用程序是使用`create-react-app`搭建的。以下是主要文件:

##### `App.js`文件:

```
import PropTypes from 'prop-types';
import React, { Component } from 'react';

import './App.css';
import logo from './logo.svg';
import ListItem from './ListItem';
import loadingGif from './loading.gif';

class App extends Component {
  constructor() {
    super();
    this.state = {
      newTodo: '',
      editing: false,
      editingIndex: null,
      notification: null,
      todos: [],
      loading: true
    };

    this.addTodo = this.addTodo.bind(this);
    this.updateTodo = this.updateTodo.bind(this);
    this.deleteTodo = this.deleteTodo.bind(this);
    this.handleChange = this.handleChange.bind(this);
    this.hideNotification = this.hideNotification.bind(this);
  }

  async componentDidMount() {
    const todos = await this.props.todoService.getTodos();
    this.setState({
      todos,
      loading: false
    });
  }

  handleChange(event) {
    this.setState({
      newTodo: event.target.value
    });
  }

  async addTodo() {
    const todo = await this.props.todoService.addTodo(this.state.newTodo);

    this.setState({
      todos: [
        ...this.state.todos, todo
      ],
      newTodo: '',
      notification: 'Todo added successfully.'
    }, () => this.hideNotification());
  }

  editTodo(index) {
    const todo = this.state.todos[index];
    this.setState({
      editing: true,
      newTodo: todo.name,
      editingIndex: index
    });
  }

  async updateTodo() {
    const todo = this.state.todos[this.state.editingIndex];
    const updatedTodo = await this.props.todoService.updateTodo(todo.id, this.state.newTodo);
    const todos = [ ...this.state.todos ];
    todos[this.state.editingIndex] = updatedTodo;
    this.setState({
      todos,
      editing: false,
      editingIndex: null,
      newTodo: '',
      notification: 'Todo updated successfully.'
    }, () => this.hideNotification());
  }

  hideNotification(notification) {
    setTimeout(() => {
      this.setState({
        notification: null
      });
    }, 2000);
  }

  async deleteTodo(index) {
    const todo = this.state.todos[index];

    await this.props.todoService.deleteTodo(todo.id);

    this.setState({ 
      todos: [
        ...this.state.todos.slice(0, index),
        ...this.state.todos.slice(index + 1)
      ],
      notification: 'Todo deleted successfully.'
    }, () => this.hideNotification());
  }

  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">CRUD React</h1>
        </header>
        <div className="container">
          {
            this.state.notification &&
            <div className="alert mt-3 alert-success">
              <p className="text-center">{this.state.notification}</p>
            </div>
          }
          <input
            type="text"
            name="todo"
            className="my-4 form-control"
            placeholder="Add a new todo"
            onChange={this.handleChange}
            value={this.state.newTodo}
          />
          <button
            onClick={this.state.editing ? this.updateTodo : this.addTodo}
            className="btn-success mb-3 form-control"
            disabled={this.state.newTodo.length < 5}
          >
            {this.state.editing ? 'Update todo' : 'Add todo'}
          </button>
          {
            this.state.loading &&
            <img src={loadingGif} alt=""/>
          }
          {
            (!this.state.editing || this.state.loading) &&
            <ul className="list-group">
              {this.state.todos.map((item, index) => {
                return <ListItem
                  key={item.id}
                  item={item}
                  editTodo={() => { this.editTodo(index); }}
                  deleteTodo={() => { this.deleteTodo(index); }}
                />;
              })}
            </ul>
          }
        </div>
      </div>
    );
  }
}

App.propTypes = {
  todoService: PropTypes.shape({
    getTodos: PropTypes.func.isRequired,
    addTodo: PropTypes.func.isRequired,
    updateTodo: PropTypes.func.isRequired,
    deleteTodo: PropTypes.func.isRequired
  })
};

export default App; 
```

##### `ListItem.js`文件:

```
 import React from 'react';
import PropTypes from 'prop-types';

const ListItem = ({ editTodo, item, deleteTodo }) => {
  return <li
    className="list-group-item"
  >
    <button
      className="btn-sm mr-4 btn btn-info"
      onClick={editTodo}
    >U</button>
    {item.name}
    <button
      className="btn-sm ml-4 btn btn-danger"
      onClick={deleteTodo}
    >X</button>
  </li>; };

ListItem.propTypes = {
  editTodo: PropTypes.func.isRequired,
  item: PropTypes.shape({
    id: PropTypes.number.isRequired,
    name: PropTypes.string.isRequired
  }),
  deleteTodo: PropTypes.func.isRequired
};

export default ListItem; 
```

###### `index.js`文件:

```
 import React from 'react';
import axios from 'axios';
import ReactDOM from 'react-dom';

import App from './App';
import { apiUrl } from './config';

import TodoService from './service/Todo';

const client = axios.create({
  baseURL: apiUrl,
});

const todoService = new TodoService(client);

ReactDOM.render(<App todoService={todoService} />, document.getElementById('root')); 
```

##### `TodoService.js`文件:

```
 /**
 * A todo service that communicates with the api to perform CRUD on todos.
 */
export default class TodoService {
  constructor(client) {
    this.client = client;
  }

  async getTodos() {
    const { data } = await this.client.get('/todos');
    return data;
  }

  async addTodo(name) {
    const { data } = await this.client.post('/todos', { name });

    return data;
  }

  async updateTodo(id, name) {
    const { data } = await this.client.put(`/todos/${id}`, { name });

    return data;
  }

  async deleteTodo (id) {
    await this.client.delete(`/todos/${id}`);

    return true;
  }
} 
```

让我们从设置开始测试所需的一切开始。如果您正在使用`create-react-app`(像我一样)，那么测试环境已经为您设置好了。剩下的就是安装反应测试库了。

```
 npm i --save-dev react-testing-library 
```

### 测试:安装组件时显示待办事项列表。

让我们从编写一个测试开始，测试 out 组件挂载时发生的第一件事:从 api 获取 Todos 并显示为一个列表。

##### `App.spec.js`文件:

```
 import React from 'react'; 
import { render, Simulate, flushPromises } from 'react-testing-library';

import App from './App';
import FakeTodoService from './service/FakeTodoService';

describe('The App component', () => {
    test('gets todos when component is mounted and displays them', async () => {

    });
}); 
```

首先，我们从 react-testing-library 导入了`render`,这只是一个助手函数，它使用`ReactDOM.render`在幕后挂载我们的组件，并为我们的测试返回挂载的 DOM 组件和几个助手函数。

其次，我们导入了`Simulate`，它与`react-dom`中的模拟完全相同。这将帮助我们在测试中模拟用户事件。

最后，我们导入了`flushPromises`，这是一个简单的实用程序，当您的组件正在做一些异步工作时，它非常有用，您需要确保异步操作在您可以继续断言之前已经解决(或拒绝)。

在撰写本文时，这就是关于这个包的 api 的全部内容。很漂亮，对吧？

还要注意我导入了一个`FakeTodoService`，这是我在测试中模拟外部异步功能的版本。你可能更喜欢使用真正的`TodoService`，模仿`axios`库，这完全取决于你。下面是假 todo 服务的样子:

```
 /**
 * A fake todo service for mocking the real one.
 */
export default class FakeTodoService {
  constructor(todos) {
    this.todos = todos ? todos : [];
  }
  async getTodos() {
    return this.todos;
  }

  async addTodo(name) {
    return {
      id: 4,
      name
    };
  }

  async deleteTodo(id) {
    return true;
  }

  async updateTodo(id, name) {
    return {
      id, name
    };
  }
} 
```

我们希望确保一旦我们的组件被挂载，它就从 api 获取 todo，并显示这些 todo。我们需要做的就是挂载这个组件(使用我们的假 to do 服务)，并断言来自我们的假服务的 todo 被显示，对吗？看一看:

```
 describe('The App component', () => {
    const todos = [{
      id: 1,
      name: 'Make hair',
    }, {
      id: 2,
      name: 'Buy some KFC',
    }];

    const todoService = new FakeTodoService(todos);

    test('gets todos when component is mounted and displays them', async () => {
        const { container, getByTestId } = render(<App todoService={todoService} />); 
    });
}); 
```

当我们渲染这个组件时，我们从结果中分解出两个东西，即`container`和`getByTestId`。容器是挂载的 DOM 组件，`getByTestId`是一个简单的帮助函数，它使用*数据属性*在 DOM 中找到一个元素。看看 Kent C. Dodds 的这篇文章[就能理解为什么使用数据属性比传统的 css 选择器更好，比如**类**和**id**。安装完组件后，为了确保 todo 显示出来，我们将向包含 todo 元素的无序列表元素添加一个`testid`数据属性，并在其子元素上写入期望。](https://blog.kentcdodds.com/making-your-ui-tests-resilient-to-change-d37a6ee37269)

```
 // App.js

...

{
   (!this.state.editing || this.state.loading) &&
       <ul className="list-group" data-testid="todos-ul">

... 
```

```
 // App.test.js

test('gets todos when component is mounted and displays them', async () => {
  const { container, getByTestId } = render(<App todoService={todoService} />);
  const unorderedListOfTodos = getByTestId('todos-ul');
  expect(unorderedListOfTodos.children.length).toBe(2);  
}); 
```

如果我们现在运行这个测试，它会失败。这是为什么呢？这就是`flushPromises`函数的用武之地。我们只需要在 todos 服务的`getTodos`函数解析了 todos 列表后运行我们的断言。为了等待那个承诺的解决，我们简单地`await flushPromises()`。

```
 // App.test.js

test('gets todos when component is mounted and displays them', async () => {
   const { container, getByTestId } = render(<App todoService={todoService} />);
   await flushPromises();
   const unorderedListOfTodos = getByTestId('todos-ul');
   expect(unorderedListOfTodos.children.length).toBe(2);  
}); 
```

好吧。这确保了组件一挂载，我认为一个好的声明是确保组件挂载时调用了`todoService.getTodos`函数。这增加了我们对 todos 实际上来自外部 api 的信心。

```
 // App.test.js

test('gets todos when component is mounted and displays them', async () => {
   // Spy on getTodos function 
   const getTodosSpy = jest.spyOn(todoService, 'getTodos');

   // Mount the component
   const { container, getByTestId } = render(<App todoService={todoService} />); 
   // Wait for the promise that fetches todos to resolve so that the list of todos can be displayed
   await flushPromises();

   // Find the unordered list of todos
   const unorderedListOfTodos = getByTestId('todos-ul');

   // Expect that it has two children, since our service returns 2 todos.
   expect(unorderedListOfTodos.children.length).toBe(2);  

   // Expect that the spy was called
   expect(getTodosSpy).toHaveBeenCalled();
}); 
```

### 测试:添加待办事项

让我们为 todo 创建过程编写测试。同样，我们感兴趣的是当用户与应用程序交互时会发生什么。

首先，如果用户没有在输入框中输入足够的字符，我们将确保`Add Todo`按钮被禁用。

```
 // App.js
// Add a data-testid attribute to the input element, and the button element

...

<input
   type="text"
   name="todo"
   className="my-4 form-control"
   placeholder="Add a new todo"
   onChange={this.handleChange}
   value={this.state.newTodo}
   data-testid="todo-input"
/>

<button
   onClick={this.state.editing ? this.updateTodo : this.addTodo}
   className="btn-success mb-3 form-control"
   disabled={this.state.newTodo.length < 5}
   data-testid="todo-button"
>
 {this.state.editing ? 'Update todo' : 'Add todo'}
</button> 
... 
```

```
 // App.test.js

describe('creating todos', () => {
   test('the add todo button is disabled if user types in a todo with less than 5 characters', async () => {
     // Mount the component
     const { container, getByTestId } = render(<App todoService={todoService} />); 
     // Wait for the promise that fetches todos to resolve so that the list of todos can be displayed
     await flushPromises();

    // Find the add-todo button and the todo-input element using their data-testid attributes
     const addTodoButton = getByTestId('todo-button');
     const todoInputElement = getByTestId('todo-input');
  });
}); 
```

我们向`button`和`input`元素添加了`data-testid`属性，稍后在我们的测试中，我们使用我们的`getByTestId`辅助函数来找到它们。

```
 // App.test.js

describe('creating todos', () => {
   test('the add todo button is disabled if user types in a todo with less than 5 characters, and enabled otherwise', async () => {
     // Mount the component
     const { container, getByTestId } = render(<App todoService={todoService} />); 
     // Wait for the promise that fetches todos to resolve so that the list of todos can be displayed
     await flushPromises();

    // Find the add-todo button and the todo-input element using their data-testid attributes
     const addTodoButton = getByTestId('todo-button');
     const todoInputElement = getByTestId('todo-input');

    // Expect that at this point when the input value is empty, the button is disabled.
    expect(addTodoButton.disabled).toBe(true);

    // Change the value of the input to have four characters
    todoInputElement.value = 'ABCD';
    Simulate.change(todoInputElement);

    // Expect that at this point when the input value has less than 5 characters,     the button is still disabled.
    expect(addTodoButton.disabled).toBe(true);

    // Change the value of the input to have five characters
    todoInputElement.value = 'ABCDE';
    Simulate.change(todoInputElement);

    // Expect that at this point when the input value has 5 characters, the button is enabled.
    expect(addTodoButton.disabled).toBe(false);
  });
}); 
```

我们的测试向我们保证了我们的用户如何与我们的应用程序交互，而不需要保证功能是如何实现的。

让我们进一步讨论用户实际点击`Add todo`按钮的情况:

```
 // App.test.js

test('clicking the add todo button should save the new todo to the api, and display it on the list', async () => {
   const NEW_TODO_TEXT = 'OPEN_PULL_REQUEST';
   // Spy on getTodos function 
   const addTodoSpy = jest.spyOn(todoService, 'addTodo');

   // Mount the component
   const { container, getByTestId, queryByText } = render(<App todoService={todoService} />); 
   // Wait for the promise that fetches todos to resolve so that the list of todos can be displayed
   await flushPromises();

   // Find the add-todo button and the todo-input element using their data-testid attributes
   const addTodoButton = getByTestId('todo-button');
   const todoInputElement = getByTestId('todo-input');

   // Change the value of the input to have more than five characters
   todoInputElement.value = NEW_TODO_TEXT;
   Simulate.change(todoInputElement);

   // Simulate a click on the addTodo button
   Simulate.click(addTodoButton);

   // Since we know this makes a call to the api, and waits for a promise to resolve before proceeding, let's flush it.
   await flushPromises();     

   // Let's find an element with the text content of the newly created todo
   const newTodoItem = queryByText(NEW_TODO_TEXT);

   // Expect that the element was found, and is a list item
   expect(newTodoItem).not.toBeNull();
   expect(newTodoItem).toBeInstanceOf(HTMLLIElement);

   // Expect that the api call was made
   expect(addTodoSpy).toHaveBeenCalled();
}); 
```

我们引入了一个新的助手函数`queryByText`，如果没有找到传递给它的特定文本，它将返回 null。这个函数将帮助我们断言一个新的待办事项是否被添加到我们当前的待办事项列表中。

### 外卖食品

您现在已经看到了如何为您的 react 组件/应用程序编写大部分集成测试。以下是一些可以借鉴的关键技巧:

*   您的测试应该更倾向于用户如何与应用程序交互，而不一定是功能如何实现。例如，避免检查状态变化，用户不知道这一点。
*   最佳实践是，避免获取呈现容器的实例，用户不会与之交互，测试也不会。
*   总是执行完全渲染，它给你更多的信心，这些组件实际上在现实世界中正常工作。真实的故事，在现实世界中没有组件是浅安装的。
*   本教程的目的不是贬低单元测试的重要性，而是鼓励更多的集成测试。当你为你的应用程序编写测试时，[测试奖杯](https://twitter.com/kentcdodds/status/960723172591992832?lang=en)可能是你考虑的一个很好的指南。