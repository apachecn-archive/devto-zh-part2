# 带 React - One Repo 的 Rails 第 1 部分

> 原文：<https://dev.to/harrymlevine/rails-with-react---one-repo---part-1-3k7o>

## 第 1 部分，共 3 部分:设置 React 为视图层

本系列教程将带您创建一个使用 React 作为视图层的 Ruby on Rails web 应用程序，所有这些都在一个存储库中。

将包括的主要技术有:

*   导轨 5.2.1
    *   Ruby 2.5.3
    *   一种数据库系统
    *   网路套件
    *   RSpec
*   反应 16
    *   反应路由器
    *   阿克斯

这个系列的所有代码位于:[https://github.com/oddballio/rails-with-react](https://github.com/oddballio/rails-with-react)

## 设置

你首先需要安装当前版本的 Ruby 和 Rails。这些指令假设 MacOS 具有 rbenv。他们遵循这个极好的、维护良好的用于 Ruby 和 Rails 环境设置的[资源，其中也包括用于 Ubuntu 和 Windows 的说明。](https://gorails.com/setup/osx/10.14-mojave)

### 安装 Ruby 2.5.3

使用 [rbenv](https://github.com/rbenv/rbenv) 调用:

```
$ rbenv install 2.5.3 
```

### 安装导轨 5.2.1

```
$ gem install rails -v 5.2.1 
```

### 新建 Rails/React web app

我们的 web 应用程序将被称为`rails-with-react`。为了创建一个 Rails 应用程序，您将运行以下命令:

*   使用 Rails 版本 5.2.1
*   [集成 Webpacker 和 React](https://github.com/rails/webpacker#react)
*   跳过添加测试单元
*   将 Postgresql 用于其数据库

```
$ rails _5.2.1_ new rails-with-react -T -d postgresql --webpack=react 
```

### 设置 Ruby 版本

Ruby 版本需要在您的应用程序中进行更新。`cd`放入新目录，并将这两个文件中的 ruby 版本更改为`2.5.3`:

*   `Gemfile`
*   `.ruby-version`

### 添加 RSpec 用于规范支持

通过在`Gemfile` :
中添加以下内容来安装 [rspec-rails gem](https://github.com/rspec/rspec-rails)

```
group :development, :test do
  gem 'rspec-rails', '~> 3.8'
end 
```

运行以下命令完成安装:

```
$ gem install bundler
$ bundle install
$ rails generate rspec:install 
```

### 完成设置

最后，您将运行`bin/setup`脚本来完成安装:

```
$ bin/setup 
```

如果您收到此错误:

```
== Command ["bin/rails db:setup"] failed == 
```

试试:

```
$ rails db:migrate
$ bin/setup 
```

## 设置 React 为视图层

从设置中的`--webpack=react`标志，您会注意到一个新的`app/javascript/`目录的存在。这是我们所有与 React 相关的代码所在的地方。

默认情况下，Rails 包含了以下样板文件和结构:

```
app/
|
|-- javascript/
|   |-- packs/
        |-- application.js
        |-- hello_react.jsx 
```

我们将进行一些更改，将 React 应用程序设置为遵循更传统、可扩展的基于组件的结构。

### 组件

首先，我们将创建基本的`App.js`组件:

**1。**在`app/javascript/`文件夹下，创建一个`components`文件夹
**2。**在`components`文件夹内，创建第一个名为`App.js`
**3 的组件。**从一个基本的“Hello world”类组件结构开始

```
// app/javascript/components/App.js

import React from 'react'

class App extends React.Component {
  render() {
    return (
      <div>
        Hello world!
      </div>
    )
  }
}

export default App 
```

### Index.js

接下来，我们将创建一个`index.js`文件，它将被注入到一个 Rails 视图文件中，包含我们的 React 应用程序。为此，我们将:

**1。**将`hello_react.jsx`改名为`index.js`
T5】2。 **删除**本锅炉板块代码:

```
import PropTypes from 'prop-types'

const Hello = props => (
  <div>Hello {props.name}!</div>
)

Hello.defaultProps = {
  name: 'David'
}

Hello.propTypes = {
  name: PropTypes.string
} 
```

**3。**导入并渲染新的`App`组件

`index.js`文件应该是这样的:

```
// app/javascript/packs/index.js

// Run this example by adding <%= javascript_pack_tag 'hello_react' %> to the head of your layout file,
// like app/views/layouts/application.html.erb. All it does is render <div>Hello React</div> at the bottom
// of the page.

import React from 'react'
import ReactDOM from 'react-dom'
import App from '../components/App'

document.addEventListener('DOMContentLoaded', () => {
  ReactDOM.render(
    <App />,
    document.body.appendChild(document.createElement('div')),
  )
}) 
```

### React 应用程序的根轨道视图

我们现在将创建一个 Rails 视图，整个 React 应用程序将被注入其中。按照传统的 Rails 模式，这将涉及:

*   控制器动作
*   根路由
*   视角

**1。**用一个空的`index`动作
创建一个`app/controllers/pages_controller.rb`

```
# app/controllers/pages_controller.rb

class PagesController < ApplicationController
  def index
  end
end 
```

**2。**设置`root`路线到此`index`动作

```
# config/routes.rb

Rails.application.routes.draw do
  root 'pages#index'
end 
```

**3。**创建一个空的`app/views/pages/index.html.erb`视图文件来呈现`index`动作

### 注入 React 到 Rails

为了连接 React 和 Rails 这两个世界，您将使用 Rails [`javascript_pack_tag`](https://github.com/rails/webpacker#usage) 将整个 React 应用程序注入到 Rails 视图中。

**1。**将`javascript_pack_tag`添加到`app/views/pages/index.html.erb`文件中，注入`index.js`T5

```
# app/views/pages/index.html.erb

<%= javascript_pack_tag 'index' %> 
```

**2。**启动 rails 服务器

```
$ rails s 
```

**3。**参观`http://localhost:3000/`

如果你看到 **Hello world！**，您已经成功将 React 设置为您的 Rails app 的视图层！

### 下一步

本系列还有两个教程:

*   [第 2 部分，共 3 部分:集成 React 路由器](https://dev.to/harrymlevine/rails-with-react---one-repo---part-2-4a6o)
*   第 3 部分，共 3 部分:处理 React 和 Rails 之间的请求