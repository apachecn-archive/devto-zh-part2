# 改造您现有的 Rails 应用程序，使用 JavaScript 前端

> 原文：<https://dev.to/kreopelle/revamp-your-existing-rails-app-to-use-a-javascript-frontend-4i5g>

这是教程的第一部分，介绍了如何在不使用`remote: true`的情况下将现有的 Rails 应用程序转换为使用 Javascript 前端。它遍历加载索引内容的过程，并使用 Javascript 在单个模型中显示操作。

第一部分概述了设置应用程序响应 jQuery 和在 JSON 中访问数据的要求的过程。第二部分将关注使用事件监听器和异步请求绘制 DOM。

### 复制存放你的 Rails 应用的仓库。

为了复制存储库，我按照 [GitHub 帮助](https://help.github.com/articles/duplicating-a-repository/)中列出的方法镜像存储库。

首先，[在 Github 上为你的 Rails/JS 应用程序创建一个新的存储库。我以我的 Rails 应用程序命名了这个回购，末尾加上了“-js”。](https://help.github.com/articles/creating-a-new-repository/)

接下来，打开您的终端，使用`https://` URL 创建存储库的“裸克隆”,该存储库包含您现有的 Rails 应用程序。

```
$ git clone --bare https://github.com/yourusername/existing-rails-repository.git 
```

如果您查看创建的新文件夹，它并不包含许多对人类有意义的信息，也不像您所期望的存储库的样子。

我对这个裸克隆的理解是一个知识库的 GitHub 框架副本，只是基本的，在引擎盖下组成回购结构的东西——可能不是你创建的东西。

`cd`变成这个骷髅复制品。

```
$ cd existing-rails-repository.git 
```

下一步是奇迹发生的地方

```
$ git push --mirror https://github.com/yourusername/new-repository-js.git 
```

该命令将您现有存储库的“镜像”推送到我们创建的新存储库，以存放您的 Rails 应用程序的 JS-ified 版本。

可以把`mirror push`想象成复制和粘贴旧 repo 中的所有内容——目录结构、文件、提交、分支等。–到新的存储库。您可能需要通过命令行登录您的 GitHub 帐户才能实现这一点。

请访问包含您的新存储库的页面进行检查。您应该会看到存放您的 Rails 应用程序的仓库的完美副本！访问您的 Rails 应用程序库的页面，您应该也不会看到任何变化！

让我们清理我们的工作。删除旧回购的裸克隆副本:

```
$ cd ..
$ rm -rf existing-rails-repository.git 
```

现在您有了 Rails repo 的新副本，可以开始添加 JavaScript 了。将这个库的副本克隆到您的设备上，让我们开始准备它来处理您的新 JS 代码。

```
$ git clone git@github.com:yourusername/new-repository-js.git
$ cd new-repository-js 
```

### 将 jquery-rails 和 active _ model _ serializers 添加到您的 Gemfile 中

以下几点有助于让你的转变变得更加容易:

*   **[jquery-rails](https://github.com/rails/jquery-rails)** :提供对 [jQuery](https://jquery.com/) 的访问，这是一个简化 DOM 操作和 AJAX 请求的 JavaScript 库，以及 [jQuery UJS](https://robots.thoughtbot.com/a-tour-of-rails-jquery-ujs) 适配器，为支持 Javascript 功能的浏览器启用该功能，而不会对不支持它的浏览器产生负面影响。

*   **[active _ model _ serializer](https://github.com/rails-api/active_model_serializers/blob/v0.10.6/docs/general/getting_started.md)**:通过指定数据的 JSON 版本中应该存在的属性和关系，提供了一种 Rails-y 方式来帮助将模型转换成 JSON。

在您的 gem 文件中，添加`active_model_serializers`和`gem ‘jquery-rails’`。运行捆绑包安装。

```
gem ‘jquery-rails’ 
gem ‘active_model_serializers’ 
```

### 将 jquery 和 jquery_ujs 添加到您的资产管道中

Rails 通过资产管道优化了 Javascript 和 CSS 的使用。根据[铁路指南](https://guides.rubyonrails.org/asset_pipeline.html)，管道的三个主要特征是:

1.  将资产连接成一个主文件。js 和一个 master。css 文件，允许浏览器对所有 JS 和所有 CSS 内容进行一次请求(请求越少->加载越快)

2.  缩小/压缩文件，将它们从人类友好的格式移动到尽可能小的版本(更小的文件->更快的加载)

3.  允许用更高级的语言编写资产，比如 CSS 的 Sass 和 JavaScript 的 CoffeeScript

`application.js`是一个清单文件——这个文件告诉资产管道在制作要发送到您的浏览器的缩小的、连接的 JS 文件时应该包括哪些文件。这通过“指令”来实现，这是由为资产管道提供动力的 gem[链轮 gem](https://github.com/rails/sprockets) 实现的功能。指令的语法是:

`//= require your_js_file`

这里应该列出所有 JS 需求，包括库(比如 jQuery)和您自己的 JavaScript 文件。

添加到`app/assets/javascripts/application.js` :

```
//= require jquery
//= require jquery_ujs 
```

当我们在这里时，让我们确保`//= require_tree`既存在又在文件的最后一行。

`require_tree`将位于`assets/javascripts`目录中的所有 JS 文件添加到 application.js 清单中。参见资产管道上的 [Rails 指南，了解如何将其他目录中的文件添加到您的`application.js`清单中。](https://guides.rubyonrails.org/asset_pipeline.html#manifest-files-and-directives)

指令是从上到下处理的，所以要确保 js 文件所依赖的外部库在 require_tree 或任何其他可能需要它们的 js 文件之前列出。

当您使用`rails new app_name`命令创建一个 Rails 应用程序时，JavaScript 清单文件会预加载到您的 html 中。嵌套在您的`app/views/layouts/application.html.erb`文件的`<head>`标签中，您应该看到:

```
<%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %> 
```

这一行代码的`javascript_include_tag ‘application’`部分告诉浏览器加载`application.js`文件，同时加载文件中列出的所有必需的 JavaScript。

### 创建您的序列化程序

活动模型序列化程序有一个生成器来为现有模型创建序列化程序:

`rails g serializer your_model`

这将在`app`目录中创建一个新的`serializers`目录，以及一个新的文件`your_model_serializer.rb`。文件的内容应该类似于:

```
class YourModelSerializer < ActiveModel::Serializer
  attributes :id
end 
```

默认生成的序列化程序只包含模型的`:id`属性。attributes 行中指定的任何属性都将包含在代表模型实例的 JSON 对象中。要添加额外的属性，只需在`attributes`后添加属性名作为符号。以 Post 模型为例，这可能看起来像:

```
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body
end 
```

如果您愿意，也可以删除 id 属性(尽管我喜欢使用 id 作为数据属性，将关于我的实例的信息传递给 JS click 事件)。

为了在您的序列化器中表示与其他模型的关系，使用上面的过程为该模型生成另一个序列化器，并向该序列化器添加适当的`has_many` / `belongs_to`关系。继续帖子的例子，如果它属于一个作者，你可以写:

`rails g serializer author`

```
# app/serializers/author_serializer.rb

class AuthorSerializer < ActiveModel::Serializer
  attributes :id, :name

   has_many :posts  
end

# app/serializers/post_serializer.rb
class PostSerializer < ActiveModel::Serializer
  attributes :id, :title, :body

  belongs_to :author 
end 
```

**注意:**如果在创建资源时安装了活动模型序列化程序 gem，也可以使用资源生成器生成序列化程序。如果是这种情况，基本属性和关系也可能包含在您生成的序列化程序中。

**注 2:** 主动模型序列化器正在翻新。 [jbuilder gem](https://github.com/rails/jbuilder) 是在运行脚手架生成器时开箱即用的替代品。

### 编辑控制器以渲染 json

有两种不同的方法(据我所知)告诉浏览器在你的控制器中呈现 JSON。以`posts#index`动作为例:

```
def index
  @posts = Post.all 
  render json: @posts 
end 
```

和

```
def index
  @posts = Post.all
  respond_to do |format|
    format.html
    format.json {render json: @posts}
  end 
end 
```

让我们做一个小实验。将模型的索引动作主体设置为第一个选项，启动 Rails 服务器。使用类似于 [localhost:3000/posts](http://localhost:3000/posts) 的东西在浏览器中访问索引操作。假设您在`views/your_model`目录中有一个`index.html.erb`文件，您漂亮的索引模板应该在窗口中被替换为空的 JSON。

第一个选项将您的操作更改为专门呈现 JSON。控制器将忽略任何 HTML，否则它可能会认为您希望在页面上呈现。

现在，将索引操作的主体设置为第二个选项，并刷新页面。您应该可以在浏览器中看到预先存在的索引模板。添加”。json”添加到 URL 的末尾([localhost:3000/posts . json](http://localhost:3000/posts.json))，您应该会再次看到浏览器中呈现的空 JSON。添加”。html”添加到 URL 的末尾，您将看到 index.html.erb 模板呈现在浏览器中。

为什么会这样？使用`respond_to`告诉你的浏览器，“如果用户想以 HTML 格式查看这个 URL 的内容(默认)，我会以 HTML 格式提供给他们。如果用户请求 JSON 中的数据(数据是保存在@posts 中的数据库中的信息)，只需添加。json 在 URL 的末尾，我会用这种方式展示给他们看。”

为您的控制器操作添加呈现 JSON 的能力，使您编写的 Javascript 能够以它本身理解的格式访问数据库中的信息。

### 现在你已经准备好开始编写 JavaScript 了！

我的下一篇博客文章将是关于用 JavaScript 事件侦听器来控制 DOM，用异步请求检索和发送到数据库的内容来描绘您的浏览器。