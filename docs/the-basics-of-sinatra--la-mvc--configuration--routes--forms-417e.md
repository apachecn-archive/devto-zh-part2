# Sinatra à la MVC 的基础——配置、路线和形式

> 原文：<https://dev.to/morinoko/the-basics-of-sinatra--la-mvc--configuration--routes--forms-417e>

Sinatra 是一种 DSL，可以让您轻松地在自己的 web 服务器上启动和运行您的应用程序，该服务器可以响应 HTTP 请求并处理 URI 路由。它实际上是建立在 [Rack](https://rack.github.io/) 之上的，这是一个 Ruby 应用程序的 web 服务器接口。相比 Rack，Sinatra 好用多了。

这篇博客文章使用 MVC 模型回顾了 Sinatra 的基本设置，并使用一个简单的食谱应用程序的例子开始了基本路线。

## 文件结构和初始设置/配置

以下是在模型-视图-控制器设置中组织应用程序的基本方法之一。值得注意的是，所有主要的应用程序文件都组织在一个应用程序文件夹中，子文件夹对应于 MVC 的每个组件:`/models`用于对象模型文件，`/views`用于 html 模板文件，`/controllers`用于控制器文件。

CSS 样式表和 Javascript 通常放在各自子文件夹下的 public 文件夹中。规范文件夹是用于规范测试的，如果您有它们的话。

```
├── Gemfile
├── README.md
├── app
│   ├── controllers
│   │   └── application_controller.rb
│   ├── models
│   │   └── model.rb
│   └── views
│       └── index.erb
├── config
│   └── environment.rb
├── config.ru
├── public
│   └── stylesheets
│   └── javascript
└── spec
    ├── controllers
    ├── features
    ├── models
    └── spec_helper.rb 
```

Enter fullscreen mode Exit fullscreen mode

*注意，对于较小的应用程序，有时只需要一个应用程序控制器，可以直接放在根文件夹中。模型和视图文件夹也可以直接放在根目录中，而不是放在应用程序文件夹中。

## Gemfile

首先，你需要 Sinatra gem，我也推荐 Shotgun gem，这样你就不需要在测试/开发过程中每次做改变时重启 Sinatra 服务器。确保您的 gem 文件包含以下内容:

```
# Gemfile

source "https://rubygems.org"
gem 'sinatra'
gem 'shotgun' 
```

Enter fullscreen mode Exit fullscreen mode

在 gem 文件准备好之后，确保在命令行中运行`bundle install`。

## Config.ru

Sinatra 和基于机架的应用程序需要一个`config.ru`来加载应用程序的环境和其他要求，通过`use`和`run`关键字指定应该使用哪些控制器，并在调用`run`时启动应用服务器。

在下面的简单例子中，`application_controller.rb`文件是我们唯一的控制器，环境是通过`config/environment.rb`文件加载的。

```
# config.ru

require_relative './config/environment'
run ApplicationController 
```

Enter fullscreen mode Exit fullscreen mode

## config/environment.rb

这个文件将我们的应用程序代码连接到适当的 gems。它加载 Bundler(从而加载 gem 文件中的所有 gem)和 app 目录中的所有文件(模型、视图和控制器)。

```
ENV['SINATRA_ENV'] ||= "development"
ENV['RACK_ENV'] ||= "development"

require 'bundler/setup'
Bundler.require(:default, ENV['SINATRA_ENV'])

require_all 'app' 
```

Enter fullscreen mode Exit fullscreen mode

(要使用方便的 require_all 关键字，请将 gem `'require_all'`添加到 gem 文件中。点击查看 [Github。)](https://github.com/jarmo/require_all/)

## 应用控制器

好了，我们最后来看一个 Sinatra 的基本控制器。控制器的工作是处理所有传入的请求、响应和路由。

在`application_controller.rb`文件中，让我们为从`Sinatra::Base`继承的应用程序创建一个类。`Sinatra::Base`为我们的应用程序提供了一个可以通过 Sinatra 的框架使用的框架兼容界面。

```
# application_controller.rb

class ApplicationController < Sinatra::Base
  # code for the controller here...
end 
```

Enter fullscreen mode Exit fullscreen mode

要为上面的文件结构设置控制器，添加一些配置代码，告诉 Sinatra 在哪里可以找到`/views`文件夹(Sinatra 默认在根目录中查找)和使用`configure`块的`/public`文件夹。在配置代码之后，我们可以编写我们的路线(我将在下面介绍一些基础知识)。

```
# application_controller.rb

class ApplicationController < Sinatra::Base
  configure do
    set :views, "app/views"
    set :public_dir, "public"
  end

  get '/' do
    "Hello World!"
  end

  # and more routes...
end 
```

Enter fullscreen mode Exit fullscreen mode

对于一个小的应用程序来说，一个控制器就足够了，在这里我们可以定义所有的 URL，以及它们如何响应像“get”和“post”这样的请求

请注意，应用程序控制器类可以命名为您喜欢的任何名称；只要确保通过`config.ru`文件中的`run`用适当的名称挂载它。“安装”只是告诉 Rack 应用程序的哪个部分定义了处理 web 请求的控制器。

## 路线

### 基本路线

路由将来自浏览器的请求连接到您的应用程序(在控制器中)中的特定方法，该方法可以处理请求并发送响应。例如，简单来说，一个路由可能只是显示或呈现一个基本的 HTML 视图。或者，另一个途径可能接收通过表单提交的数据，比如食谱标题及其成分和步骤，处理这些数据，然后显示完成的 post——一个新的食谱，这只是另一个 HTML 视图。

一些基本的 GET 请求可能是这样的:

```
# application_controller.rb

get '/' do
  erb :index
end

get '/about' do
  erb :about
end 
```

Enter fullscreen mode Exit fullscreen mode

`get '/' do`和`get '/about' do`行对应浏览器中的 URL。所以，如果域是`tastybites.com`，`get '/' do`指的是那个根域。`get '/about' do`指`tastybites.com/about`。

`erb :index`和`erb :about`行告诉控制器获取和显示视图文件夹中的哪个视图文件，在本例中是一个嵌入的 ruby 文件。因此，我们需要在视图文件夹中有一个`index.erb`和一个`about.erb`来实现这个功能。

如您所见，视图文件由路径中的同名符号表示。Sinatra 假设视图模板都直接位于`/views`文件夹下，所以如果视图恰好嵌套在`/views`的一个文件夹中，比如说`/views/recipes/index`，我们就需要这样称呼它:`erb :'recipes/index'`或`erb 'recipes/index'.to_sym`。例如:

```
# application_controller.rb

get '/recipes/index' do
  erb :'recipes/show'
end

# Or the other way:
get '/recipes/index' do
  erb 'recipes/show'.to_sym
end 
```

Enter fullscreen mode Exit fullscreen mode

### 动态路线

动态路由可以根据 URL 中的属性处理 HTTP 请求。这些属性由直接在路径中编码的符号表示，并且它们的值可以通过自动生成的`params`哈希轻松访问，因此它们可以用于查找或处理数据。

让我们看一个例子。假设在我们位于`tastybites.com`的食谱站点中，您希望能够通过 URL 中的 id(例如`tastybites.com/recipes/27`，27 代表 id)来获取单个食谱。显然，我们不想为每一个菜谱及其 id 写出一条路线。用一个符号代替:`get '/recipes/:id'` do，其中`:id`可以是任何数字。然后可以通过`params[:id]`访问`:id`的值。让我们看看如何使用它来获取合适的食谱:

```
# application_controller.rb

get '/recipes/:id' do
  # The :id is passed through the URL,
  # which is accessible in the params hash.
  # Use it to assign a recipe to an instance variable
  @recipe = Recipe.find(params[:id])
  erb :'recipes/show'
end 
```

Enter fullscreen mode Exit fullscreen mode

注意 params 散列是如何用于从数据库中查找配方的。然后将该配方分配给一个实例变量。Sinatra 中的实例变量非常特别，因为我们可以用它们向视图传递数据！这让我想到了下一部分…

### 通过实例变量向视图模板传递数据

每当在控制器布线中创建实例变量时，该变量在相应的视图文件中可用。请注意，实例变量在控制器的其他路由中不可用；仅在单条路线中指定的视图。

让我们回到上面的例子:

```
# controllers/application_controller.rb

get '/recipes/:id' do
  @recipe = Recipe.find(params[:id])
  erb :'recipes/show'
end 
```

Enter fullscreen mode Exit fullscreen mode

这里假设您的`app/models`文件夹中也有一个菜谱模型，看起来可能是这样的:

```
# models/recipe.rb

class Recipe
  attr_accessor :title, :description, :ingredients, :method

  # The rest of your Recipe class code...
end 
```

Enter fullscreen mode Exit fullscreen mode

食谱有一些属性，如标题、描述等。因此，一旦 recipe 对象被分配给控制器路径中的`@recipe`，我们就可以将所有这些属性编织到我们的 ERB 模板中:

```
# views/recipes/show.erb

<h1><%= @recipe.title %></h1>
<p><%= @recipe.description %></p>

<h2>Ingredients</h2>
<ul>
<% @recipe.ingredients.each do |ingredient| %>
  <li><%= ingredient %></li>
<% end %>
</ul>

<h2>How to Cook</h2>
<p><%= @recipe.method %></p> 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至可以遍历视图中的数据。例如，假设我们想在食谱索引页面中显示所有的食谱。首先，我们可以将所有的食谱分配到`recipes/index`路径中的一个实例变量中:

```
# controllers/application_controller.rb

get '/recipes/' do
  @recipes = Recipe.all
  erb :'recipes/index'
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，迭代`recipes/index.erb`模板中的菜谱:

```
# views/recipes/index.erb

<h1>All Recipes</h1>
<% @recipes.each.do |recipe| %>
  <h2>
    <a href="../recipes/<%= recipe.id %>"><%= recipe.title %></a>
  </h2>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们还使用 recipe id 链接到 recipe 页面，这将由`get '/recipes/:id'`路由处理。相当酷。

请注意，这些实例变量不必是模型中的对象；它们可以是您想要在视图中分配和使用的任何变量。

现在我们知道了如何从 URL 获取和使用数据，让我们看看如何通过 POST 方法处理通过表单发送的数据。

### 用表单传递数据，用‘post’路由捕捉数据

从表单接收用户输入的数据是构建 web 应用程序的关键。我们只需要正确地把你的表格连接到我们的控制器上。让我们继续我们的食谱例子——这一次我们要创建一个食谱，所以我们首先需要的是一个基本的表单和呈现它的路径。

设置路由非常简单，只需将新菜谱帖子的 URL 连接到适当的视图，该视图将包含我们的表单。在这种情况下，假设我们希望 url 是`tastybitees.com/recipes/new` :

```
# app/controllers/application_controller.rb

get '/recipes/new' do
  erb :'recipes/new'
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在`views/recipes/new.erb`文件中，我们将设置一个基本表单:

```
# views/recipes/new.erb

<form method='POST' action='/recipes'> 
  <label for="title">Title</label>
  <input type="text" name="title">

  <label for="description">Description</label>
  <textarea name="description"></textarea>

  <label for="ingredients">Ingredients</label>
  <textarea name="ingredients"></textarea>

  <label for="method">How to Cook</label>
  <textarea name="method"></textarea>

  <input type="submit" value="Submit">
</form> 
```

Enter fullscreen mode Exit fullscreen mode

`<form method='POST' action='/recipes'>`线对于设置路线非常重要。`action`属性告诉控制器代码的哪一部分(即哪条路线)应该处理表单。把它想象成一个地址。`method`属性就是它如何到达那里，在这个例子中是通过`POST`。

另一个重要部分是每个输入标签上的`name`属性，因为这是设置我们的`params`散列的部分。上面我们有`name="title"`、`name="description"`、`name="ingredients"`和`name="method"`，它们对应于我们的配方模型中的属性。这将产生一个类似这样的散列，取决于用户提交的内容:

```
{ 
  "title" => "Apple Pie",
  "description" => "A recipe that I learned from my granny.",
  "ingredients" => "4 apples, 1 cup sugar, ...",
  "method" => "Preheat oven to 350 degrees. Cut the apples in small pieces..." 
} 
```

Enter fullscreen mode Exit fullscreen mode

当表单提交后，这些数据就可以在这个方便的`params` hash 中获得了！因此，让我们设置 post 路线，并使用参数来创建一个新的食谱:

```
# app/controllers/application_controller.rb

# This is responsible for PROCESSING a newly submitted recipe form
post '/recipes' do
  @recipe = Recipe.new

  # get data from params
  @recipe.title = params[:title]
  @recipe.descriptions = params[:descriptions]
  @recipe.ingredients = params[:ingredients]
  @recipe.method = params[:method]
  @recipe.save
end 
```

Enter fullscreen mode Exit fullscreen mode

唯一的问题是，一旦表单被提交，用户将看到一个空白页。这里，`POST`只是发送信息，它不会在事后显示。在配方数据被处理和完成之后，向用户显示完成的配方是有意义的。方便的是，Sinatra 有一个很好的`redirect`方法，将用户带到另一个页面，在这里是食谱展示页面。添加到上面的路线得到这个:

```
# app/controllers/application_controller.rb

post '/recipes' do
  @recipe = Recipe.new
  @recipe.title = params[:title]
  @recipe.descriptions = params[:descriptions]
  @recipe.ingredients = params[:ingredients]
  @recipe.method = params[:method]
  @recipe.save

  # show post after completion
  redirect "/recipes/#{@recipe.id}"
end 
```

Enter fullscreen mode Exit fullscreen mode

`redirect "/recipes/#{@recipe.id}"`将把它发送到我们的食谱展示路线`get '/recipes/:id'`，这样用户可以为他们的新创造感到自豪。

这就是我们使用 MVC 概念建立一个简单的基于 Sinatra 的应用程序的基础！你可以在路线上做无数的事情，在 Sinatra 的 README 页面上有一个获得更多信息的好地方。