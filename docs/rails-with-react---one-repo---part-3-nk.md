# 带 React - One Repo 的 Rails 第 3 部分

> 原文：<https://dev.to/harrymlevine/rails-with-react---one-repo---part-3-nk>

## 第 3 部分，共 3 部分:处理 React 和 Rails 之间的请求

### 重述

在本系列的第 1 和第 2 部分中，我们介绍了:

*   [设置 React 为视图层](https://dev.to/harrymlevine/rails-with-react---one-repo---part-1-3k7o)
*   [集成 React 路由器](https://dev.to/harrymlevine/rails-with-react---one-repo---part-2-4a6o)

这个系列的所有代码位于:[https://github.com/oddballio/rails-with-react](https://github.com/oddballio/rails-with-react)

### 简介

传统的 Rails 应用程序在呈现页面时有以下一般生命周期:

1.  用户访问一个 URL
2.  向该 URL 发出了 HTTP 请求
3.  路径在`routes.rb`中被识别，并调用相关的控制器动作
4.  控制器动作执行其逻辑
5.  控制器动作呈现一个视图，将任何相关的返回数据传递给视图

在本教程中，我们将介绍如何使用与 Rails 后端交互的 React 视图层来重新创建这种模式。

## 获取请求

我们将通过`Posts.js`组件来表示 HTTP `GET`请求流程。该组件将调用`posts_controller#index` Rails 动作来呈现帖子列表。

### 轨道控制器动作

这将是一个典型的 Rails 控制器动作，只需做一些调整，就可以像 API 一样工作。

**1。**在`app/controllers/`下创建一个`api`文件夹

我们的控制器和路由的命名空间减少了 React 路由和 Rails 路由之间的任何潜在冲突。

**2。`config/initializers/inflections.rb`中的**实现了一个变形规则，允许`api`名称空间在路由过程中作为大写的`API`模块被引用

[导轨](https://api.rubyonrails.org/classes/ActiveSupport/Inflector/Inflections.html#method-i-acronym)对此的解释是:

> 指定任何需要保持非标准大写的单词

```
# config/initializers/inflections.rb

ActiveSupport::Inflector.inflections(:en) do |inflect|
  inflect.acronym 'API'
end 
```

**3。**创建一个带有`index`动作的`app/controllers/api/posts_controller.rb`

为了简单起见，我们将模拟`index`动作通过一个存根化的 posts 数组从数据库返回一组`Post`记录。

```
# app/controllers/api/posts_controller.rb

module API
  class PostsController < ApplicationController
    def index
      posts = ['Post 1', 'Post 2']

      render json: { posts: posts }
    end
  end
end 
```

### 铁轨`posts#index`路线

为了与我们的控制器命名空间保持一致，我们将在一个`:api`命名空间中命名我们所有的路由。

```
# config/routes.rb

Rails.application.routes.draw do
  root 'pages#index'

  namespace :api, defaults: { format: 'json' } do
    resources :posts, only: :index
  end

  # IMPORTANT #
  # This `match` must be the *last* route in routes.rb
  match '*path', to: 'pages#index', via: :all
end 
```

### 呼唤`posts#index`从何反应

我们的`Posts.js`组件需要向我们新的`posts#index`端点发出一个 HTTP `GET`请求。为此，我们将使用 [Axios](https://www.npmjs.com/package/axios) HTTP 客户端。

**1。**安装 Axios

```
$ yarn add axios 
```

**2。**将 Axios 导入到`Posts.js`

```
// app/javascript/components/Posts.js

import axios from 'axios'
... 
```

**3。**用 Axios 调用`posts#index`

我们将在一个`componentDidMount()` React 生命周期方法中调用 Axios。你可以在这个[指南中阅读更多关于生命周期方法的内容，以反应组件生命周期方法](https://medium.com/@_ChrisBradshaw/blogdidmount-2018-guide-to-react-component-lifecycle-methods-1614e0bbe80a)。

请注意，`posts#index`端点的轨道路线是`/api/posts`。

```
// app/javascript/components/Posts.js

...
class Posts extends React.Component {
  state = {
    posts: []
  };

  componentDidMount() {
    axios
      .get('/api/posts')
      .then(response => {
        this.setState({ posts: response.data.posts });
      })
  }
... 
```

**4。**显示`posts#index`调用
返回的帖子

```
// app/javascript/components/Posts.js

...
  renderAllPosts = () => {
    return(
      <ul>
        {this.state.posts.map(post => (
          <li key={post}>{post}</li>
        ))}
      </ul>
    )
  }

  render() {
    return (
      <div>
        {this.renderAllPosts()}
      </div>
    )
  }
... 
```

**5。** `Posts.js`最后应该是这个样子:

```
// app/javascript/components/Posts.js

import React from 'react'
import axios from 'axios'

class Posts extends React.Component {
  state = {
    posts: []
  };

  componentDidMount() {
    axios
      .get('/api/posts')
      .then(response => {
        this.setState({ posts: response.data.posts });
      })
  }

  renderAllPosts = () => {
    return(
      <ul>
        {this.state.posts.map(post => (
          <li key={post}>{post}</li>
        ))}
      </ul>
    )
  }

  render() {
    return (
      <div>
        {this.renderAllPosts()}
      </div>
    )
  }
}

export default Posts 
```

**6。**在一个选项卡中启动`rails s`，在另一个选项卡中运行`bin/webpack-dev-server`

**7。**参观`http://localhost:3000/posts`

你应该看到:

```
• Post 1
• Post 2 
```

## 发布请求

我们将通过`NewPost.js`组件来表示 HTTP `POST`请求流程。这个组件将调用`posts_controller#create` Rails 动作来创建一个新的帖子。

### 轨道控制器动作和路线

**1。**给`posts_controller`增加一个`create`动作

我们将通过呈现调用端点时使用的`params`:
来模拟`post_controller#create`动作被成功点击

```
# app/controllers/api/posts_controller.rb

  def create
    render json: { params: params }
  end 
```

**2。**在`:posts`航线
上增加一条`:create`航线

```
# config/routes.rb

  namespace :api, defaults: { format: 'json' } do
    resources :posts, only: [:index, :create]
  end 
```

### 表单创建一个帖子

**1。**在`NewPost.js`创建表单提交新帖子

```
// app/javascript/components/NewPost.js

  render() {
    return (
      <div>
        <h1>New Post</h1>
        <form>
            <input
              name="title"
              placeholder="title"
              type="text"
            />
            <input
              name="body"
              placeholder="body"
              type="text"
            />
          <button>Create Post</button>
        </form>
      </div>
    )
  } 
```

**2。**捕获表单数据

我们将通过每个输入的`onChange` :
中的`setState`来做这件事

```
// app/javascript/components/NewPost.js

import React from 'react'

class NewPost extends React.Component {
  state = {
    title: '',
    body: ''
  }

  handleChange = event => {
    this.setState({ [event.target.name]: event.target.value });
  }

  render() {
    return (
      <div>
        <h1>New Post</h1>
        <form>
            <input
              name="title"
              onChange={this.handleChange}
              placeholder="title"
              type="text"
            />
            <input
              name="body"
              onChange={this.handleChange}
              placeholder="body"
              type="text"
            />
          <button>Create Post</button>
        </form>
      </div>
    )
  }
}

export default NewPost 
```

### 呼唤`posts#create`从何反应

我们的`NewPost.js`组件需要向我们新的`posts#create`端点发出一个 HTTP `POST`请求。为此，我们将使用我们在上一节中安装的 [Axios](https://www.npmjs.com/package/axios) HTTP 客户端。

**1。**将 Axios 导入到`NewPost.js`

```
// app/javascript/components/NewPost.js

import axios from 'axios'
... 
```

**2。**创建一个函数来处理表单的提交

```
// app/javascript/components/NewPost.js

...
  handleSubmit = event => {
    event.preventDefault();
  }

  render() {
    return (
      <div>
        <h1>New Post</h1>
        <form onSubmit={e => this.handleSubmit(e)}>
... 
```

**3。** `POST`表单数据到`posts#create`端点

`posts#create`终点的轨道路线为`/api/posts`。我们将`console.log`回应。

```
// app/javascript/components/NewPost.js

  handleSubmit = event => {
    event.preventDefault();

    const post = {
      title: this.state.title,
      body: this.state.body
    }

    axios
      .post('/api/posts', post)
      .then(response => {
        console.log(response);
        console.log(response.data);
      })
  } 
```

**4。**在一个选项卡中启动`rails s`，在另一个选项卡中运行`bin/webpack-dev-server`

**5。**访问`http://localhost:3000/new_post`，填写并提交表单

此时，表单应该不起作用。如果您查看 Rails 服务器日志，您应该会看到:

```
ActionController::InvalidAuthenticityToken (ActionController::InvalidAuthenticityToken) 
```

这是 Rails 的[跨站点请求伪造(CSRF)对策](https://guides.rubyonrails.org/security.html#csrf-countermeasures)的结果。

### 解决 CSRF 问题

为了解决这个问题，我们需要在 Axios 头中传递 Rails 的 CSRF 令牌，作为对 Rails 服务器端端点的 HTTP `POST`请求的一部分。

因为将来任何其他非`GET`请求都需要这个功能，我们将把它提取到一个`util/helpers.js`文件中。

**1。**创建一个`app/javascript/util/helpers.js`文件
2**。**在`helpers.js`中添加函数来传递 CSRF 令牌

```
// app/javascript/util/helpers.js

function csrfToken(document) {
  return document.querySelector('[name="csrf-token"]').content;
}

export function passCsrfToken(document, axios) {
  axios.defaults.headers.common['X-CSRF-TOKEN'] = csrfToken(document);
} 
```

**3。**将`passCsrfToken`函数导入到`NewPost.js`中，并调用

```
// app/javascript/components/NewPost.js

...
import { passCsrfToken } from '../util/helpers'

class NewPost extends React.Component {
  state = {
    title: '',
    body: ''
  }

  componentDidMount() {
    passCsrfToken(document, axios)
  }
... 
```

**4。**访问`http://localhost:3000/new_post`，填写并提交表单

在控制台中，您应该会看到:

```
params: {title: "some title", body: "some body", format: "json", controller: "api/posts", action: "create", …} 
```

🎉

*这个教程系列的灵感来自于 zayne . ioT3[“React+Rails】](https://www.youtube.com/channel/UCkoEStUK7wxmZef2DcPuCAQ/featured)*