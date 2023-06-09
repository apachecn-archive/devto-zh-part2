# 为什么不是秘银？

> 原文：<https://dev.to/evanrblack/why-not-mithril-a4c>

我们都经历过这种情况:传统 web 应用程序的一小部分确实可以使用一些类似 SPA 的交互性。您可以用 jQuery 将它拼凑起来，但是可靠地更新所有元素可能会变得混乱。在这种情况下，反应式框架可能会更好，但是像 React 或 Vue 这样的东西可能会有很多负担；你只是想这件事毕竟有点花哨。

如果你发现自己在这个地方，[秘银](https://mithril.js.org/)值得一去。

Mithril 将自己标榜为“构建单页面应用程序的现代客户端 Javascript 框架”，并为其提供了开箱即用的路由器和请求处理器等支持。尽管它功能齐全，但对于单个组件来说也一样有效，并且最小化仅为 22k，不到 React 大小的四分之一。

为了更好地描述 Mithril，我认为将它与 React 和 Vue 进行比较和对比可能会有所帮助。

### 类似的反应

*   必须安装在特定的元件上，只能在元件内操作
*   使用 Javascript 逻辑控制呈现

### 与反应不同

*   对全局状态的操作很简单
*   无需编译器/传输器即可轻松完成

### 类似于 Vue

*   更适合传统网络应用的增强
*   设置很简单

### 不同于 Vue

*   通过 Javascript 表达的所有逻辑
*   不能在服务器模板化的 HTML 上操作

* * *

为了给出一个简单组件的样子，这里有一个简单的例子:

```
// todo.js

var root = document.getElementById('root');
var newTodo = '';
var todos = [];

function setNewTodo(value) {
  newTodo = value;
}

function removeTodo(i) {
  todos.splice(i, 1);
}

function addTodo(value) {
  todos.push(value);
  newTodo = '';
}

var TodoComponent = {
  view: function() {
    return m('div', [
      m('ul', todos.map(function(todo, i) {
        return m('li', [
          m('span', todo),
          m('strong', { href: '#', onclick: function() { removeTodo(i) } }, ' X'),
        ]);
      })),
      m('input', { value: newTodo, oninput: m.withAttr('value', setNewTodo) }),
      m('button', { onclick: function() { addTodo(newTodo) } }, 'Add Todo'),
    ]);
  }
};

m.mount(root, TodoComponent); 
```

这是一个实际的例子:

[https://codepen.io/evanrblack/embed/EOQeoy?height=600&default-tab=result&embed-version=2](https://codepen.io/evanrblack/embed/EOQeoy?height=600&default-tab=result&embed-version=2)

最突出的一点是使用了`m`函数。`m`根据它接收的参数有许多不同的形式，但一个通用的形式是:`m(<tag or component>, <properties>, <children or textcontent>)`。可以省略属性部分来代替子项。子元素甚至不必是一个数组，也可以写成附加参数。

[文档](https://mithril.js.org/)涵盖了[功能的灵活性](https://mithril.js.org/hyperscript.html#flexibility)和几乎所有其他内容，包括常见场景和一些疑难杂症。

* * *

对我来说，秘银的主要好处是:

*   比 jQuery 更容易管理显示逻辑
*   设置比反应容易
*   比 Vue 更适合隔离的专用元件

每个框架都有自己的位置，但是秘银找到了一个最佳点。希望我已经说服你让它成为你工具带的一部分。