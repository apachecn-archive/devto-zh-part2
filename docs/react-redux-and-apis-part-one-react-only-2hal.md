# React、Redux 和 API 的第一部分:仅反应

> 原文：<https://dev.to/patrickgordon/react-redux-and-apis-part-one-react-only-2hal>

当使用 React 构建 web 应用程序时，很有可能需要与 API 进行交互。它可能是您或您的公司构建的，也可能是第三方提供的。无论如何，您都希望从中检索数据，并向您的用户显示这些数据。

本系列将涵盖 React 和 React/Redux 应用程序与 API 对话的不同方式，以及每种方式的优缺点。

第一篇文章非常简单，只用 React 来覆盖。

# 取入容器

您可以尝试检索数据的第一种方法是直接在您的[容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.1k4schs31)中进行 fetch 调用。假设您想要显示一个帖子列表，并且您的容器呈现了一个带有属性`posts`的`PostList`组件。

这可能看起来像这样:

```
import React, { Component } from "react";

import PostList from "./PostList";

class Posts extends Component {
    state = {
        posts: []
    }

    async componentDidMount() {
        const fetchConfig = {
            method: "GET",
            headers: new Headers({ "Content-Type": "application/json" }),
            mode: "cors"
        }

        const response = await fetch("https://jsonplaceholder.typicode.com/posts/", fetchConfig);

        if (response.ok) {
            const posts = await response.json();
            this.setState({ posts });
        } else {
            console.log("error!", error);
        }
    }

    render() {
        const { posts } = this.state;

        return (
            <PostList posts={posts} />
        )
    }
} 
```

#### 下面是这个例子的一个工作代码:【https://jsfiddle.net/patrickgordon/69z2wepo/142592/】T2

在上面的例子中，我们首先用一个空数组为`posts`定义组件的本地状态。这是我们将用来传递给我们的`PostList`组件的内容。然后，我们使用生命周期方法`componentDidMount`通过一些标准配置启动获取。如果你不熟悉 promises 和 async/await，这部分可能看起来很混乱，但本质上我们在等待从 API 得到响应，然后使用`this.setState`用来自 API 的 JSON 响应更新组件状态。这将触发重新呈现，然后数据将在列表中显示给用户。

## 利弊

如果您只需要一种快速简单的方法来访问小型 React 应用程序中的数据，这种方法非常好。然而，随着应用程序的增长，这将开始变得非常令人沮丧。例如，假设您想使用类似于 [react-router](https://github.com/ReactTraining/react-router) 的东西向您的应用程序添加多条路由。当你导航离开这个页面，然后再导航回来，每次你点击这个页面，它就会在`componentDidMount`里面触发 fetch。这是不希望的，原因有很多，但不限于:

*   不必要的网络请求
*   导致您的应用程序多次重新呈现
*   大量重复的代码

此外，从这个 API 检索的数据只对这个组件层次结构有用。如果你想有一个导航栏，上面有一个数字，可以计算你有多少帖子，那该怎么办？你还需要让它获取和检索你所有的文章。这意味着大量浪费的网络请求和大量重复的代码。

# 接下来

在本系列的下一部分中，我将解释在 React only 中处理 API 时如何使用实用函数来确保代码重用。