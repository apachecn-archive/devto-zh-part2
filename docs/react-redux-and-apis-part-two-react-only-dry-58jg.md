# React、Redux 和 API 的第二部分:仅反应(干)

> 原文：<https://dev.to/patrickgordon/react-redux-and-apis-part-two-react-only-dry-58jg>

在系列文章的第一篇[文章中，我展示了如何在 React 中与 API 交互。这种方法的一个主要问题是，如果您有多个容器需要与 API 对话，那么您将会重复许多相同的代码。](https://www.patrick-gordon.com/posts/react-redux-apis-part-one/)

在这篇文章中，我们将会看到你如何用 React 与 API 对话，但是是以一种[干巴巴的](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)方式。

# 抽象常用代码

让我们回头看看第一篇文章中的例子:

```
// Posts.js
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

现在，假设我们也想从同一个 API 获取注释。我们将不得不复制所有处理配置的代码，以及对注释容器的响应。无论您需要调用多少其他不同的端点，您都可以将这种情况进行到底。

另一种方法是抽象公共代码。例如，让我们创建一个新文件`apiHelper.js` :

```
// apiHelper.js
export const SUCCESSFUL_STATUS = "success";
export const FAILED_STATUS = "failed";

const apiHelper = async ({ method, endpoint }) => {
    const fetchConfig = {
        method,
        headers: new Headers({ "Content-Type": "application/json" }),
        mode: "cors"
    }

    const response = await fetch(`https://jsonplaceholder.typicode.com/${endpoint}/`, fetchConfig);

    if (response.ok) {

        try {
            const data = await response.json();

            return {
                status: SUCCESSFUL_STATUS,
                data
            }
        } catch (error) {
            return {
                status: FAILED_STATUS,
                error
            }
        }

    } else {
        return {
            status: FAILED_STATUS
        }
    }
}

export default apiHelper; 
```

在这里，我们将所有的处理从 PostList 转移到了 helper，并让它接受一些参数。

现在看看帖子和评论会是什么样子:

```
// Posts.js
import React, { Component } from "react";

import apiHelper, { SUCCESSFUL_STATUS } from "../utils/apiHelper";
import PostList from "./PostList";

class Posts extends Component {
    state = {
        posts: []
    }

    componentDidMount() {
        const { status, data } = apiHelper({ method: "GET", endpoint: "posts" });

        if (status === SUCCESSFUL_STATUS) {
            this.setState(() => ({ posts: data }));
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

```
// Comments.js
import React, { Component } from "react";

import apiHelper, { SUCCESSFUL_STATUS } from "../utils/apiHelper";
import CommentList from "./CommentList";

class Comments extends Component {
    state = {
        comments: []
    }

    componentDidMount() {
        const { status, data } = apiHelper({ method: "GET", endpoint: "comments" });

        if (status === SUCCESSFUL_STATUS) {
            this.setState(() => ({ comments: data }));
        }
    }

    render() {
        const { comments } = this.state;

        return (
            <CommentList comments={comments} />
        )
    }
} 
```

正如你所看到的，只需要很少的工作就可以让它变得更加灵活，而不需要重复我们自己。

# 奖金

如果您想与多个 API 接口，但保持最小的重复，该怎么办？这里有一个例子，告诉你如何重构`apiHelper.js`来做到这一点:

```
// apiHelper.js
export const SUCCESSFUL_STATUS = "success";
export const FAILED_STATUS = "failed";

const buildAPIHelper = (args) =>  async ({ method, endpoint }) => {
    const {
        baseURL,
        headers = new Headers({ "Content-Type": "application/json" }) // some sane defaults
    } = args;

    const fetchConfig = {
        method,
        headers,
        mode: "cors"
    }

    const response = await fetch(`${baseURL}${endpoint}`, fetchConfig);

    if (response.ok) {

        try {
            const data = await response.json();

            return {
                status: SUCCESSFUL_STATUS,
                data
            }
        } catch (error) {
            return {
                status: FAILED_STATUS,
                error
            }
        }

    } else {
        return {
            status: FAILED_STATUS
        }
    }
}

export const firstAPIHelper = buildAPIHelper({ 
    baseURL: "https://jsonplaceholder.typicode.com/",
});

export const secondAPIHelper = buildAPIHelper({
    baseURL: "https://api.patrick-gordon.com/" 
    headers: new Headers({ "Content-Type": "application/json", "Authorization": "bearer someKey" })
}); 
```

# 接下来

在本系列的下一部分中，我们将把 Redux 引入其中，并看看我们如何使用 Redux 与 API 对话。

在那之前，干杯，

帕特里克。