# 学习冗余和笔记

> 原文：<https://dev.to/oathkeeper/learning-redux-and-notes--43ae>

这是我在 Stephen Grider 的课程中学习 React Redux 时得到的笔记和知识，我将写下结论并展示将与一起创建的应用程序。

代码回购在这里[https://github.com/Tapudp/BookList](https://github.com/Tapudp/BookList)

这个应用程序有两个主要容器(组件)

*   书单
*   activeBook(活动书籍)

bookList 将呈现已经保存在 reducers 中的图书列表，然后 container 将在页面上呈现该列表，可以单击并选择其中的一个

当动作生成器`onClick`事件发生时，activeBook 容器进入画面，它将显示来自 activeBook reducer 的特定图书的所有细节。

以下是在创建整个应用程序时已经完成的一步一步的过程

# 书库 App

### 减速器

*   有没有像 reducers 这样的 fanct 单词，所以要创建一个 reducer 文件，该文件具有返回书籍数组的功能

### 容器连接 Redux 进行反应

*   它已经为组件生成了一个状态
*   创建了与状态没有任何联系的组件`book-list.js`。
*   `containers`与组件类似，只是有一个哑组件，可以添加冗余状态

### 容器实现

*   在主 app 文件中添加`booklist`组件
*   在`book-list`中添加`react-redux`库，因为它将成为我们应用程序的容器
*   `connect`接受一个函数和一个组件并生成一个容器
*   供应商会给你的商店，我们可以调用我们的减速器
*   在到目前为止还是哑组件的`book-list`中，将会有一个函数`mapStateToProps`,它直接在商店中查找`books`或任何在那里定义的状态属性。

### 动作和动作创建者

*   用户单击并创建操作操作
*   动作创建者返回一个动作
*   动作自动发送给所有减速器
*   `activeBook`状态属性设置为从`activeBook`减速器返回的值
*   所有减速器都处理了该操作，并返回新状态。新的国家已经组成。通知容器状态的变化
*   在通知容器将呈现新的道具

### 绑定动作创建者

*   创建一个动作功能`selectbook`
*   用图书列表容器附加`selectBook`动作创建器
*   `bindActionCreators`redux 库中的函数
*   `mapDispatchToProps`使用带有`dispatch`功能对象的`bindActionCreators`作为第二个参数，它将采取所有的动作，并将传递给应用程序中所有不同的减速器

### 创建一个动作

*   `onClick`事件和动作现在刚刚登录
*   所以现在动作创建者应该返回一个动作
*   现在改变动作创建者，它有两个关键字`type`可以是一个变量和一个`payload`书本身

### 消耗减速器中的动作

*   创建了一个新的减速器`activeBook reducer`
*   2 减速器的参数`currentState`和`action`
*   状态参数不是应用程序状态，而是特定缩减器负责的状态
*   redux 不接受`undefined`状态，所以我们将使用 ES6 语法，并说`state = null`函数参数本身，所以它说`if the state is undefined then set it to null`
*   永远不要改变当前来呈现新的状态，总是返回一个新的对象
*   并且`active_reducer`需要处于组合减速器状态，因为`index.js`
*   任何提供我们`combineReducer`的密钥最终都会成为我们全局状态的密钥

随着我的进步，我会不断更新