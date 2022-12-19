# 带 React - One Repo 的 Rails 第 2 部分

> 原文：<https://dev.to/harrymlevine/rails-with-react---one-repo---part-2-4a6o>

## 第 2 部分，共 3 部分:集成 React 路由器

### 重述

在本系列的第一部分中，我们介绍了将 react 设置为视图层。

这个系列的所有代码位于:[https://github.com/oddballio/rails-with-react](https://github.com/oddballio/rails-with-react)

### 简介

现在我们已经有了视图层，接下来我们将希望能够访问应用程序中的许多不同页面，每个页面都有自己的目的。例如:

*   主页
*   显示文章列表的页面
*   带有创建新帖子的表单的页面

为了创建具有多个唯一 URL 的多个 React 组件，我们将集成 [React 路由器](https://www.npmjs.com/package/react-router)。

### 创建并导入新组件

让我们用一些样板内容创建一个类组件来表示这些页面中的每一个。

**1。**创建`app/javascript/components/Home.js`

```
// app/javascript/components/Home.js

import React from 'react'

class Home extends React.Component {
  render() {
    return (
      <div>
        Home page
      </div>
    )
  }
}

export default Home 
```

**2。**创建`app/javascript/components/Posts.js`

```
// app/javascript/components/Posts.js

import React from 'react'

class Posts extends React.Component {
  render() {
    return (
      <div>
        Posts page
      </div>
    )
  }
}

export default Posts 
```

**3。**创建`app/javascript/components/NewPost.js`

```
// app/javascript/components/NewPost.js

import React from 'react'

class NewPost extends React.Component {
  render() {
    return (
      <div>
        NewPost page
      </div>
    )
  }
}

export default NewPost 
```

**4。**将组件导入`App.js`

```
// app/javascript/components/App.js

import React from 'react'
import Home from './Home'
import Posts from './Posts'
import NewPost from './NewPost'

... 
```

### 安装并导入依赖项

**1。**安装 [React 路由器](https://www.npmjs.com/package/react-router)和 [react-router-dom](https://www.npmjs.com/package/react-router-dom)

```
$ yarn add react-router
$ yarn add react-router-dom 
```

**2。**在`index.js`导入相关的包组件

```
// app/javascript/packs/index.js

...
import { BrowserRouter as Router, Route } from 'react-router-dom' 
```

### 设置路由器和路由

让我们集成这些新包中的组件。

**1。**在`index.js`中，我们将传入包的`Router`组件
，而不是传入`App`组件

```
// app/javascript/packs/index.js

...
document.addEventListener('DOMContentLoaded', () => {
  ReactDOM.render(
    <Router>

    </Router>,
    document.body.appendChild(document.createElement('div')),
  )
}) 
```

**2。**在`Router`组件中，我们将添加一个建立根路径的`Route`组件，用`App.js`作为我们的根组件

```
// app/javascript/packs/index.js

...
    <Router>
      <Route path="/" component={App} />
    </Router>, 
```

### 在 React app 中创建路线

由于`App.js`被设置为路由器的根组件，它将包含每个组件的所有单独路由。

**1。**在`App.js`中，从`react-router-dom`
导入`Route`和`Switch`组件

```
// app/javascript/components/App.js

...
import { Route, Switch } from 'react-router-dom' 
```

**2。**在`App.js`中，在`Switch`组件
中建立所有唯一的路线

```
// app/javascript/components/App.js

...
class App extends React.Component {
  render() {
    return (
      <div>
        <Switch>
          <Route exact path="/" component={Home} />
          <Route exact path="/posts" component={Posts} />
          <Route exact path="/new_post" component={NewPost} />
        </Switch>
      </div>
    )
  }
}

export default App 
```

### 伞状轨道路线为所有反应路线

我们需要创建一个与来自 React 应用程序的任何潜在路线相匹配的总括路线，并将它们汇集到我们的`pages_controller#index`行动中。这是呈现我们的 React 应用程序的动作。

**重要:**这条`match`航线必须是`routes.rb`中最后一条航线**，以保证不会误吸收任何其他航线。**

**1。**在`routes.rb`创建通吃路线

```
# config/routes.rb

Rails.application.routes.draw do
  root 'pages#index'

  # IMPORTANT #
  # This `match` must be the *last* route in routes.rb
  match '*path', to: 'pages#index', via: :all
end 
```

**2。**用`rails s`
**3 启动 rails 服务器。**在单独的选项卡中运行`bin/webpack-dev-server`脚本

```
$ bin/webpack-dev-server 
```

这设置了一个基本的 React 应用程序的标准实时重载。

**4。**参观`http://localhost:3000/`

你应该看到“主页”

**5。**参观`http://localhost:3000/posts`

你应该看到“文章页面”

**6。**参观`http://localhost:3000/new_post`

你应该看到“新帖子页”

### 下一步

本系列还有一个教程:

第 3 部分，共 3 部分:处理 React 和 Rails 之间的请求