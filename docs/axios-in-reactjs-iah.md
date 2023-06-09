# Axios in React。射流研究…

> 原文：<https://dev.to/moz5691/axios-in-reactjs-iah>

Axios 是下载量最大的 NPM 软件包之一，即使 React 也是如此。JS 自带了原生的 FETCH API，它应该和 Axios 做同样的工作。我不会解释为什么 Axios 在 React.jS 和 Angular 中比 FETCH 或其他 API 更受欢迎。这篇文章主要是关于在 React 中使用 Axios。这是我从摩西·哈迈达尼([https://programmingwithmosh.com](https://programmingwithmosh.com))的训练中学到的。

下面是帮助在 React.js 中编写 CRUD 方法的代码。作为如何使它与后端 Node.JS 一起工作的快速参考，它可能是有用的。

以下是代码结构的完整快照，只是省略了“渲染”部分。本文的范围是 Axios 和 Node。JS CRUD 实现。每个处理程序的“onClick”事件应该很简单。

```
import React, { Component } from 'react';
import axios from 'axios';
import './App.css';
const apiEndpoint = 'URL here';
class App extends Component {
    state = {
    posts: []
};

async componentDidMount() {
    const { data: posts } = await axios.get(apiEndpoint);
    this.setState({ posts });
}

handleAdd = async () => {
    const obj = { title: 'a', body: 'b' };
    const { data: post } = await axios.post(apiEndpoint, obj);
    const posts = [post, ...this.state.posts];
    this.setState({ posts });
};

handleUpdate = async post => {
    post.title = 'updated title';
    const { data } = await axios.put(apiEndpoint + '/' + post.id, post);
    // axios.patch(apiEndpoint + '/' + post.id, {title: post.title});
    const posts = [...this.state.posts];
    const index = posts.indexOf(post);
    posts[index] = post;
    this.setState({ posts });
};

handleDelete = async post => {
    await axios.delete(apiEndpoint + '/' + post.id);
    const posts = this.state.posts.filter(p => p.id !== post.id);
    this.setState({ posts });
}; 
```

让我们一个一个地检查 CRUD 方法。

GET 方法:ComponentDidMount 生命周期钩子是实现 GET 方法最合理的地方。当浏览器准备好了，所有组件都准备好了，我们就可以从后端获取数据了。

Axios 在承诺返回中返回“数据”数组。我们可以简单地从 Axios 返回的“承诺”中提取“数据”。然后，“数据”被重命名为“文章”，以进一步简化代码。如果{posts: posts}与' posts '和' post '同值，我们可以简化为 ES6 JS 中的{posts}。

```
async componentDidMount() {
    const { data: posts } = await axios.get(apiEndpoint);
    this.setState({ posts });
} 
```

POST 方法是将单个对象添加到后端数据库。假设数据模式中有“title”和“body”属性，那么我们要添加带有{title:'a '，body:'b'}的新对象。

在 POST 方法中，Axios 通过 Promise 返回相同的返回属性“data ”,但它是一个单独的对象，只是通过 POST 方法添加的。我们获取这个对象，并在“state”中的“posts”中添加和更新。

```
handleAdd = async () => {
    const obj = { title: 'a', body: 'b' };
    const { data: post } = await axios.post(apiEndpoint, obj);
    const posts = [post, ...this.state.posts];
    this.setState({ posts });
}; 
```

PUT 或 PATCH 方法是更新单个项目。PUT 和 PATCH 的主要区别是 PUT 更通用，它允许我们更新多个属性。修补更具体地说是更新对象中的单个属性。下面的代码技巧是我从 Mosh 那里学到的，非常有用和简单。React 中的 setState 以我的经验来看有时候是 JS 数组挑战。我们总能找到方法，但要找到正确有效的方法需要付出一些努力。

```
handleUpdate = async post => {
    post.title = 'updated title';
    const { data } = await axios.put(apiEndpoint + '/' + post.id, post);
    /* the following commented is for PATCH API as reference */
    // axios.patch(apiEndpoint + '/' + post.id, {title: post.title});
    const posts = [...this.state.posts];
    const index = posts.indexOf(post);
    posts[index] = post;
    this.setState({ posts });
}; 
```

删除方法非常简单。在 setState 中，使用了 React 中一个流行的过滤函数。

```
handleDelete = async post => {
    await axios.delete(apiEndpoint + '/' + post.id);
    const posts = this.state.posts.filter(p => p.id !== post.id);
    this.setState({ posts });
}; 
```

所有上述方法都基于“悲观更新”，这意味着我们希望确保在更新前端用户视图之前更新后端更新。这种实现的缺点是用户可能会经历 POST、PUT、DELETE 方法的缓慢响应。

另一种实现方式是“乐观更新”。乐观更新是先更新前端，然后在后台运行服务器端作业。这种方式可以在很大程度上提高前端用户体验的响应性，然而，我们希望假设后端一切正常。

我们仍然可以添加一个简单的代码更改来回滚前端所做的任何更改，以防后端出现故障。下面是一个使用 DELETE 方法的简单代码更改示例。

```
handleDelete = async post => {
   /* store the current state in previousPosts, just in case of server side fail */
    const previousPosts = this.state.posts;
 /* optimistically, update on browser side, */
    const posts = this.state.posts.filter(p => p.id !== post.id);
 this.setState({ posts });

 /* server side update.  If any fail, rollback the state */
    try {
     await axios.delete(apiEndpoint + '/' + post.id);
     } catch (e) {
     this.setState({posts: previousPost});
   }

}; 
```

就个人而言，上述带有 try-catch 乐观更新的代码最适合用户体验和从任何服务器端故障中恢复。

参考:[https://codewithmosh.com/](https://codewithmosh.com/)