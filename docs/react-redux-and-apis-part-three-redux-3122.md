# React、Redux 和 API 的第三部分:Redux

> 原文：<https://dev.to/patrickgordon/react-redux-and-apis-part-three-redux-3122>

*原帖可以在我的博客上找到*

在[的上一篇文章](https://www.patrick-gordon.com/posts/react-redux-apis-part-two/)中，我们深入探讨了如何使用 React 以一种干巴巴的方式与 API 对话。在这篇文章中，我们将引入 [Redux](https://github.com/reduxjs/redux) 来管理我们的应用程序状态并与我们的 API 对话。

我们不会看你为什么想要 redux，相反，我们会看你如何使用 thunks 作为接口与 API 对话，并把你所有的逻辑从你的组件中移出，我们在第一部分和第二部分已经有了。

这篇文章中有一些关于 redux 的假设知识。至少你应该明白:

1.  为什么你想在你的应用中使用 redux
2.  “行动”是什么，做什么
3.  “减压器”是什么，有什么作用
4.  什么是“中间件”,它做什么

如果你对 thunks 有所了解，也会有所帮助。

[Redux docs](https://redux.js.org/) 非常棒，如果你对以上内容不确定，你绝对应该通读一下。

**公平警告:这篇文章有点长！**

# Thunks

Thunks 在 redux 应用程序中非常有用，因为它们通过一个名为 getState 和 dispatch 的函数让您可以访问状态。这意味着你可以把你的动作创建器从返回一个对象的简单函数变成返回一个内部函数的函数，这个内部函数允许你检查你的状态或者调度多个动作。

Redux-thunk 将上述内容总结为:

> Redux Thunk 中间件允许您编写返回函数而不是动作的动作创建器。thunk 可用于延迟动作的调度，或者仅在满足特定条件时才进行调度。内部函数接收存储方法`dispatch`和`getState`作为参数。

太好了，这是很多信息，让我们看看从代码的角度来看这些信息意味着什么。

# 传统 Redux 动作对比 thunk 的例子

让我们以更新客户 ID 的操作为例。

```
// customerActions.js

const updateCustomerID = customerID => {
    type: "Customer.UPDATE_CUSTOMER_ID",
    payload: {
        customerID
    }
} 
```

在这个动作创建器中，它接收一个 customerID，然后返回一个动作来更新它。但是，如果神奇的业务规则说，如果商店中还没有设置 customerID，我们只想更新它，那该怎么办呢？

一种方法是连接正在更新 customerID 的组件，并在启动操作之前检查那里。但是，如果有另一个组件需要做同样的事情呢？还是两个？还是三个？到处都是大量的重复。

Thunks 允许我们避免这种情况

```
// customerActions.js

const updateCustomerID = customerID => (dispatch, getState) => {
    const state = getState();
    const { customerID } = state.customer;

    if (customerID === null) {
        dispatch({
            type: "Customer.UPDATE_CUSTOMER_ID",
            payload: {
                customerID
            }
        });
    }
} 
```

在这里，我们可以使用 thunk 来检查 customerID 是否已经设置，如果没有，我们可以更新它。这样，我们可以避免在整个应用程序中重复大量的代码。

# 用 thunks 与 API 对话

好了，这是很多关于使用 thunks 的背景知识，但是在我们讨论如何使用它与 API 对话之前，这是很有必要的。

在 redux 中处理 API 时，我喜欢的一个特殊模式是在请求开始、请求成功完成和请求失败时触发动作。这允许你设置你的 reducers 来处理各种不同的结果，反过来你可以更新你的 UI。例如，当从 API 获取帖子时，您可以显示一个加载微调器。

下面是我们如何使用 thunk:
与 posts API 对话

```
// postsActions.js

const loadPosts = () => async (dispatch, getState) => {
    dispatch({
        type: "Posts.LOAD_POSTS_REQUEST"
    });

    const fetchConfig = {
        method,
        headers: new Headers({ "Content-Type": "application/json" }),
        mode: "cors"
    }

    const response = await fetch(`https://jsonplaceholder.typicode.com/${endpoint}/`, fetchConfig);

    if (response.ok) {
        try { 
            const data = await response.json();
            dispatch({
                type: "Posts.LOAD_POSTS_SUCCESS",
                payload: {
                    data
                }
            });
            return;
        } catch (error) {
            dispatch({
                type: "Posts.LOAD_POSTS_FAILURE"
            });
        }
    }

    dispatch({
        type: "Posts.LOAD_POSTS_FAILURE"
    });
} 
```

在这个示例中，我们触发一个动作来记录我们正在获取帖子，然后当它成功完成时我们触发一个动作，如果它失败了我们也可以触发一个动作。

下面是这如何转化为我们的组件:

```
// Posts.js
import React, { Component } from "react";
import { bindActionCreators } from "redux";
import { connect } from "react-redux";

import PostList from "./PostList";
import { loadPosts as loadPostsAction } from "./postsActions";

class Posts extends Component {
    componentDidMount() {
        const { actions: { loadPosts } } = this.props;
                loadPosts();
    }

    render() {
        const { posts } = this.props;

        return (
            <PostList posts={posts} />
        )
    }
}

const mapStateToProps = state => ({
    posts: state.posts
});

const mapDispatchToProps = dispatch => ({
    actions: bindActionCreators({
        loadPosts: loadPostsActions
    })
});

export default connect(mapStateToProps, mapDispatchToProps)(Posts); 
```

您可以看到，我们不再将容器组件耦合到获取数据，或者必须拥有组件的内部状态。随着应用程序的规模和复杂性的增长，这将给我们带来更大的灵活性。

我故意省略了 reducers，但本质上它只需要处理这三个动作并相应地更新存储。

# 接下来:

在下一篇文章中，当我们扩展我们的应用程序以包含评论动作时，我们将看看如何使 Redux 代码更加简洁。