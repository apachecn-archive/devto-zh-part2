# 测试 vue 应用程序的简单介绍。

> 原文：<https://dev.to/bahdcoder/a-gentle-introduction-to-testing-vue-applications-52jk>

### 简介

在本教程中，我们将介绍测试 vue-js 应用程序和组件。我们将测试这个简单的 todo 应用程序。

这个应用程序的源代码保存在[这里](https://github.com/bahdcoder/testing-vue-apps)。

为了简单起见，这个应用程序是用一个组件`App.vue`构建的。下面是它的样子:

```
 // src/App.vue

<template>
<div class="container text-center">
  <div class="row">
    <div class="col-md-8 col-lg-8 offset-lg-2 offset-md-2">
      <div class="card mt-5">
      <div class="card-body">
        <input data-testid="todo-input" @keyup.enter="e => editing ? updateTodo() : saveTodo()" v-model="newTodo" type="text" class="form-control p-3" placeholder="Add new todo ...">
        <ul class="list-group" v-if="!editing" data-testid="todos">
          <li :data-testid="`todo-${todo.id}`" class="list-group-item" v-for="todo in todos" :key="todo.id">
            {{ todo.name }}
            <div class="float-right">
              <button :data-testid="`edit-button-${todo.id}`" class="btn btn-sm btn-primary mr-2" @click="editTodo(todo)">Edit</button>
              <button :data-testid="`delete-button-${todo.id}`" class="btn btn-sm btn-danger" @click="deleteTodo(todo)">Delete</button>
            </div>
          </li>
        </ul>
      </div>
    </div>
    </div>
  </div> </div> </template> 
<script>
import axios from 'axios'

export default {
  name: 'app',
  mounted () {
    this.fetchTodos()
  },
  data () {
    return {
      todos: [],
      newTodo: '',
      editing: false,
      editingIndex: null,
      apiUrl: 'https://5aa775d97f6fcb0014ee249e.mockapi.io'
    }
  },
  methods: {
    async saveTodo () {
      const { data } = await axios.post(`${this.apiUrl}/todos`, {
        name: this.newTodo
      })

      this.todos.push(data)

      this.newTodo = ''
    },
    async deleteTodo (todo) {
      await axios.delete(`${this.apiUrl}/todos/${todo.id}`)
      this.todos.splice(this.todos.indexOf(todo), 1)
    },
    editTodo (todo) {
      this.editing = true
      this.newTodo = todo.name

      this.editingIndex = this.todos.indexOf(todo)
    },
    async updateTodo () {
      const todo = this.todos[this.editingIndex]

      const { data } = await axios.put(`${this.apiUrl}/todos/${todo.id}`, {
        name: this.newTodo
      })

      this.newTodo = ''
      this.editing = false

      this.todos.splice(this.todos.indexOf(todo), 1, data)
    },
    async fetchTodos () {
      const { data } = await axios.get(`${this.apiUrl}/todos`)

      this.todos = data
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### 简要应用概述。

我们正在测试的应用程序是一个 CRUD to-dos 应用程序。

*   当安装组件时，调用一个`fetchTodos`函数。这个函数调用一个外部 API 并获得一个 todos 列表。
*   待办事项列表显示在一个无序列表中。
*   每个列表项都有一个使用待办事项的唯一 id 生成的动态属性`data-testid`。这将用于我们以后的测试。如果你想理解为什么我们要使用数据属性而不是传统的类和 id，看看[这个](https://blog.kentcdodds.com/making-your-ui-tests-resilient-to-change-d37a6ee37269)。
*   无序列表、输入字段、编辑和删除按钮也有`data-testid`属性。

### 设置

*   在本地克隆 GitHub 存储库并安装所有 npm 依赖项:

```
 git clone https://github.com/bahdcoder/testing-vue-apps

cd testing-vue-apps && npm install 
```

Enter fullscreen mode Exit fullscreen mode

*   安装测试所需的包:
    *   包，这是 vuejs 的官方测试库。
    *   package，这是一个简单的包，它刷新所有待定的已解决承诺处理程序(我们将在后面详细讨论)。

```
 npm i --save-dev @vue/test-utils flush-promises 
```

Enter fullscreen mode Exit fullscreen mode

*   我们将为`axios`库创建一个 mock，我们将在测试中使用它，因为我们不想在测试中发出真正的 API 请求。创建一个`test/__mocks__/axios.js`文件，并在其中粘贴以下模拟:

```
// __mocks__/axios.js

export default {
  async get () {
    return {
      data: [{
        id: 1,
        name: 'first todo'
      }, {
        id: 2,
        name: 'second todo'
      }]
    }
  },
  async post (path, data) {
    return {
      data: {
        id: 3,
        name: data.name
      }
    }
  },
  async delete (path) {},
  async put (path, data) {
    return {
      data: {
        id: path[path.length - 1],
        name: data.name
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Jest 会自动获取这个文件，并在我们运行测试时用安装的`axios`库替换它。例如，`get`函数返回一个用两个 todos 解析的承诺，每次在我们的应用程序中调用`axios.get`时，jest 都会用我们的 mock 中的函数替换这个功能。

### 编写我们的第一个测试

在`tests/unit`目录中，创建一个名为`app.spec.js`的新文件，并将其添加到:

```
 // tests/unit/app.spec.js

import App from '@/App.vue'
import { mount } from '@vue/test-utils'

describe('App.vue', () => {
  it('displays a list of todos when component is mounted', () => {
    const wrapper = mount(App)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们做的第一件事是从`@vue/test-utils`库中导入`App.vue`组件和`mount`函数。

接下来，我们调用`mount`函数，将`App`组件作为参数传入。

mount 函数呈现 App 组件，就像在真实浏览器中呈现组件一样，并返回一个包装器。这个包装器包含了大量的测试助手函数，我们将在下面看到。

正如您所看到的，我们想要测试从 API 中获取一个 todos 列表，并在组件挂载时显示为一个无序列表。

由于我们已经通过调用组件上的`mount`函数呈现了组件，我们将搜索列表项，并确保它们被显示。

```
// app.spec.js
  it('displays a list of todos when component is mounted', () => {
    const wrapper = mount(App)

    const todosList = wrapper.find('[data-testid="todos"]')
    expect(todosList.element.children.length).toBe(2)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

*   包装器上的`find`函数接收一个`CSS selector`并使用该选择器在组件中找到一个元素。

不幸的是，此时运行这个测试会失败，因为断言在用 todos 解析`fetchTodos`函数之前运行。为了确保我们的 axios mock 在断言运行之前用待办事项列表进行解析，我们将使用我们的`flush-promises`库:

```
 // app.spec.js

import App from '@/App.vue'
import { mount } from '@vue/test-utils'
import flushPromises from 'flush-promises'

describe('App.vue', () => {
  it('displays a list of todos when component is mounted', async () => {
    // Mount the component
    const wrapper = mount(App)
    // Wait for fetchTodos function to resolve with list of todos
    await flushPromises()

    // Find the unordered list
    const todosList = wrapper.find('[data-testid="todos"]')

    // Expect that the unordered list should have two children
    expect(todosList.element.children.length).toBe(2)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

`find`函数返回一个包装器，在那里我们可以得到真正的`DOM-element`，它保存在`element`属性中。因此，我们断言孩子的数量应该等于 2(因为我们的`axios.get` mock 返回一个包含两个待办事项的数组)。

运行我们的测试现在通过了。太好了！

### 测试用户可以删除待办事宜

每个待办事项都有一个删除按钮，当用户点击这个按钮时，它应该会删除待办事项，并将其从列表中删除。

```
 // app.spec.js

  it('deletes a todo and removes it from the list', async () => {
    // Mount the component
    const wrapper = mount(App)

    // wait for the fetchTodos function to resolve with the list of todos.
    await flushPromises()

    // Find the unordered list and expect that there are two children
    expect(wrapper.find('[data-testid="todos"]').element.children.length).toBe(2)

    // Find the delete button for the first to-do item and trigger a click event on it.
    wrapper.find('[data-testid="delete-button-1"]').trigger('click')

    // Wait for the deleteTodo function to resolve.
    await flushPromises()

    // Find the unordered list and expect that there is only one child
    expect(wrapper.find('[data-testid="todos"]').element.children.length).toBe(1)

    // expect that the deleted todo does not exist anymore on the list
    expect(wrapper.contains(`[data-testid="todo-1"]`)).toBe(false)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我们引入了一些新东西，即`trigger`函数。当我们使用`find`函数找到一个元素时，我们可以使用这个函数在这个元素上触发 DOM 事件，例如，我们通过在找到的 todo 元素上调用`trigger('click')`来模拟对删除按钮的点击。

当这个按钮被点击时，我们调用`await flushPromises()`函数，以便`deleteTodo`函数解析，之后，我们可以运行我们的断言。

我们还引入了一个新函数`contains`，它接受一个`CSS selector`，并根据该元素是否存在于`DOM`中返回一个布尔值。

因此，对于我们的断言，我们断言`todos`无序列表中的列表项数量是 1，最后还断言 DOM 不包含我们刚刚删除的待办事项的列表项。

### 测试用户可以创建待办事宜

当用户键入一个新的待办事项并点击 enter 按钮时，一个新的待办事项会被保存到 API 并添加到待办事项的无序列表中。

```
// app.spec.js

  it('creates a new todo item', async () => {
    const NEW_TODO_TEXT = 'BUY A PAIR OF SHOES FROM THE SHOP'

    // mount the App component
    const wrapper = mount(App)

    // wait for fetchTodos function to resolve
    await flushPromises()

    // find the input element for creating new todos
    const todoInput = wrapper.find('[data-testid="todo-input"]')

    // get the element, and set its value to be the new todo text
    todoInput.element.value = NEW_TODO_TEXT

    // trigger an input event, which will simulate a user typing into the input field.
    todoInput.trigger('input')

    // hit the enter button to trigger saving a todo
    todoInput.trigger('keyup.enter')

    // wait for the saveTodo function to resolve
    await flushPromises()

    // expect the the number of elements in the todos unordered list has increased to 3
    expect(wrapper.find('[data-testid="todos"]').element.children.length).toBe(3)

    // since our axios.post mock returns a todo with id of 3, we expect to find this element in the DOM, and its text to match the text we put into the input field.
    expect(wrapper.find('[data-testid="todo-3"]').text())
      .toMatch(NEW_TODO_TEXT)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我们是这样做的:

*   我们使用其`data-testid attribute selector`找到输入字段，然后将其值设置为字符串常量`NEW_TODO_TEXT`。使用我们的触发函数，我们触发了`input`事件，这相当于用户在输入字段中输入内容。

*   为了保存待办事项，我们通过触发`keyup.enter`事件来点击回车键。接下来，我们调用`flushPromises`函数来等待`saveTodo`函数进行解析。

*   此时，我们运行我们的断言:

    *   首先，我们找到无序列表，并期望它现在有三个待办事项:两个来自组件安装时调用的`fetchTodos`函数，一个来自创建一个新的。
    *   接下来，使用`data-testid`，我们找到刚刚创建的特定待办事项(我们使用`todo-3`,因为我们对`axios.post`函数的模仿返回一个新的 todo 项，其`id`为 3)。
    *   我们断言这个列表项中的文本等于我们在文本开头的输入框中键入的文本。
    *   注意，我们使用了`.toMatch()`函数，因为这个文本也包含了`Edit`和`Delete`文本。

### 测试用户可以更新待办事宜

更新过程的测试与我们已经完成的类似。就是这里:

```
 // app.spec.js

  it('updates a todo item', async () => {
    const UPDATED_TODO_TEXT = 'UPDATED TODO TEXT'

    // Mount the component
    const wrapper = mount(App)

    // Wait for the fetchTodos function to resolve
    await flushPromises()

    // Simulate a click on the edit button of the first to-do item
    wrapper.find('[data-testid="edit-button-1"]').trigger('click')

    // make sure the list of todos is hidden after clicking the edit button
    expect(wrapper.contains('[data-testid="todos"]')).toBe(false)

    // find the todo input
    const todoInput = wrapper.find('[data-testid="todo-input"]')

    // set its value to be the updated texr
    todoInput.element.value = UPDATED_TODO_TEXT

    // trigger the input event, similar to typing into the input field
    todoInput.trigger('input')

    // Trigger the keyup event on the enter button, which will call the updateTodo function
    todoInput.trigger('keyup.enter')

    // Wait for the updateTodo function to resolve.
    await flushPromises()

    // Expect that the list of todos is displayed again
    expect(wrapper.contains('[data-testid="todos"]')).toBe(true)

    // Find the todo with the id of 1 and expect that its text matches the new text we typed in.
    expect(wrapper.find('[data-testid="todo-1"]').text()).toMatch(UPDATED_TODO_TEXT)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

现在运行我们的测试应该会成功。厉害！