# 合金开发日记—第二部分

> 原文：<https://dev.to/suprnova32/alloyci-dev-diarypart2-1jfl>

# 构建新的凤凰计划

[![Photo by valor kopeny on Unsplash](img/df7a92558060b396198b2e13611739cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lkQ11QmD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1440613740265-a37aa8a61131%3Fixlib%3Drb-0.3.5%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26s%3D78b205db80905893f41d2f694258e376%26auto%3Dformat%26fit%3Dcrop%26w%3D2674%26q%3D80)

这篇博客文章是详细介绍 AlloyCI 开发过程的系列文章的一部分。之前的条目有:

*   [第一部分:开始一个新项目](https://dev.to/supernova32/alloyci-dev-diary---part-1--1ai9)

* * *

如果您来自 Rails 背景，您可能会发现 Phoenix 项目的文件夹结构有点奇怪，尤其是在 1.3 中的更改之后。但是不要因此而退缩，这个结构实际上很有意义，特别是新的 1.3 结构。

不像 Rails，没有`app/`文件夹来存放你的大部分代码。Phoenix 1.3 项目遵循几乎所有常规 Elixir 项目的结构，这意味着您的大部分代码将位于`lib/my_app`文件夹中。

正如你现在可能知道的，Phoenix 1.3 改变了项目的结构。在这个改变之前，你的大部分代码都在`web/`之下，很少或者没有进一步的组织。你会有一个模型目录，在那里你会放置你所有的业务逻辑，而不用考虑它们是如何相互作用的。这个文件夹会变得很大，很快。此外，名称 model 似乎意味着一个对象，但在 Elixir 中，没有对象，因此将数据访问层存储在名为“models”的文件夹下没有什么上下文意义。

## 数据&语境

默认情况下，Phoenix 1.3 会引导您以更好的方式组织代码。控制器、视图、模板放在`lib/my_app_web`下，数据库迁移和相关文件放在`priv/repo`下，而你的长生不老药代码的其余部分将放在`lib/my_app`下。这是基本的设置，您可以对其进行调整，并根据自己的喜好进行更改。你有完全的自由去组织你的代码。

由于我在 Phoenix 1.3 完全发布之前就开始编写 AlloyCI，所以有些文件夹结构与最新的 1.3 生成器创建的不同。我更喜欢 AlloyCI 现在的结构，因为我真的不喜欢他们把`web/`文件夹重命名为`alloy_ci_web/`的方式，以及里面的所有东西从`AlloyCi.Web.XXX`重命名为`AlloyCiWeb.XXX`。我真的更喜欢在模块名称中的分隔，以及在 web 文件夹名称中不重复应用程序名称的事实。不过，由于 Phoenix 提供的灵活性，我不需要遵循惯例。

无论如何，关于结构变化的最重要的部分是，Phoenix 现在指导您使用上下文来构造您的数据访问层。

以 AlloyCI 为例，我们有`Accounts`上下文(在`lib/alloy_ci/accounts`文件夹下)，其中有`User`、`Authentication`和`Installation`模式。这 3 种模式密切相关，属于同一个业务逻辑，即账户的处理。

如果您仔细查看 accounts 文件夹下的文件，您会发现除了`changeset`函数之外，模式文件中没有其他函数。这意味着我需要直接通过`Ecto`来操作数据库数据(不推荐),或者我需要一个 API 边界来允许我在账户相关的模式上执行操作。

这就是`AlloyCi.Accounts`模块发挥作用的地方。如果 AlloyCI 需要对与帐户相关的任何模式执行操作，这个模块就是 alloy ci 与之交互的边界。该模块的所有公共函数提供了一种操作数据的简单方法，同时通过间接层提供了安全性。

这就是上下文的目的。它们在您的数据层和您的业务逻辑之间提供了一个边界，并允许您拥有一个明确的契约来告诉您如何操作您的数据。它还能让你独立于埃克托。比方说，在未来，你想从 Ecto 换成“最新、最酷的 DB 驱动程序”。如果你不使用抽象层，比如上下文，你将不得不重构代码库中使用 Ecto 与数据层通信的每个功能。但是由于我们使用了上下文，我们只需要在上下文中重构代码。

## 数据展示

实际将你的数据呈现给用户的代码可以存在于`lib/my_app/web`或`lib/my_app_web`文件夹下，这取决于你想如何构建它(自动生成器将默认为`lib/my_app_web`，但我更喜欢前者)。

在这里你可以找到你的控制器、视图、模板和通道所在的文件夹。让我们从表示层开始。

### 视图&模板

如果您来自 Rails 背景，您可能想知道为什么有两个组件来呈现数据，而在 Rails 中您只需要 views 文件夹。在 Phoenix 中,“视图”不是由模板化的 HTML 文件组成的，而是常规的 Elixir 模块。这些模块可以帮助您与模板共享代码，并实现与 Rails“视图助手”相似的目的，但默认情况下，这些模块特定于单个控制器(不加载其他视图，不像 Rails 加载所有视图助手，不管调用哪个控制器)。

这种分离使得在呈现数据所需的相似助手函数上使用相同的签名变得更加容易(而不会真正使它们过载)，这取决于调用的是哪个控制器，从而简化了您的代码。

模板就是你的 HTML 代码所在的地方。模板文件保存为`*.html.eex`文件(意思是嵌入的药剂)，与`erb`文件非常相似。语法是完全相同的，但是您编写的不是 Ruby 代码，而是 Elixir 代码😄

Phoenix 和 Rails 之间一个非常重要的区别是如何在控制器和模板之间共享信息。在 Rails 中，用`@something`声明一个实例变量就足够了，它将对模板/视图可用。
考虑到 Elixir 的功能特性，在 Phoenix 中，您需要显式地将您希望提供的信息传递给 render 函数中的视图。这些被称为分配。作为一个例子，这里是`PipelineController` :
的`show`动作

```
def show(conn, %{"id" => id, "project_id" => project_id}, current_user, _claims) do
  with %Pipeline{} = pipeline <- Pipelines.show_pipeline(id, project_id, current_user) do
    builds = Builds.by_stage(pipeline)
    render(conn, "show.html", builds: builds, pipeline: pipeline, current_user: current_user)
  else
    _ ->
      conn
      |> put_flash(:info, "Project not found")
      |> redirect(to: project_path(conn, :index))
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

“show.html”之后的所有内容都是赋值符，因此与 show 动作相关的模板可用的变量是`builds`、`pipeline`和`current_user`。我们可以在 pipeline info 头的代码片段中看到如何使用它们的示例:

```
<div class="page-head">
  <h2 class="page-head-title">
    <%= @pipeline.project.owner %>/<%= @pipeline.project.name %>
  </h2>
  <nav aria-label="breadcrumb" role="navigation">
    <!-- Breadcrumb -->
    <ol class="breadcrumb page-head-nav">
      <li class="breadcrumb-item">
        <%= ref_icon(@pipeline.ref) %>
        <%= clean_ref(@pipeline.ref) %>
      </li>
      <li class="breadcrumb-item">
        <%= icon("github") %>
        <%= sha_link(@pipeline) %>
      </li>
      <li class="breadcrumb-item">
        <%= icon("book") %>
        <%= pretty_commit(@pipeline.commit["message"]) %>
      </li>
      <%= if @pipeline.commit["pr_commit_message"] do %>
        <li class="breadcrumb-item">
          <%= icon("code-fork") %>
          <%= @pipeline.commit["pr_commit_message"] %>
        </li>
      <% end %>
      <li class="breadcrumb-item">
        <%= icon("tasks") %>
        <%= String.capitalize(@pipeline.status) %>
        <%= status_icon(@pipeline.status) %>
      </li>
    </ol>
  </nav>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

一旦变量被赋值，它就可以通过`@var_name`进入模板，就像 Rails 一样。在与控制器同名的视图文件中定义的函数(在本例中为`pipeline_view.ex`)立即可用于模板。在上面的例子中，`sha_link/1`在 GitHub 上创建了一个指向特定提交的 HTML 链接。

### 控制器

在结构上，Phoenix 控制器与 Rails 控制器非常相似，主要区别如上所述。当由助手工具生成时，它们将具有与 Rails 对应部分相同的`index`、`show`、`edit`、`update`和`delete`动作。就像 Rails 控制器一样，您可以通过定义一个函数并连接一个路由来定义您想要的任何动作。

### 通道

Phoenix 通道用于通过 web 套接字与 Web 客户端通信。它们类似于 Rails 中的 ActionCable，但在我看来，功能更强大，性能更好。在 AlloyCI 中，它们用于实时推送构建日志的输出，并呈现一段预格式化的 HTML 代码以显示用户的存储库(关于 AlloyCI 如何使用通道的更多信息将在另一篇文章中讨论)。

### 路线

菲尼克斯的路线定义有点类似于铁路路线。有些语法是不同的，但它是立即可识别的，感觉很熟悉。看看 AlloyCI:
上是如何定义项目路线的

```
scope "/", AlloyCi.Web do
...
  resources "/projects", ProjectController do
    resources("/pipelines", PipelineController, only: [:create, :delete, :show])

    resources("/builds", BuildController, only: [:show, :create]) do
      get("/artifact", BuildController, :artifact, as: :artifact)
      post("/artifact/keep", BuildController, :keep_artifact, as: :keep_artifact)
    end

    resources("/badge/:ref", BadgeController, only: [:index])
  end
...
end 
```

Enter fullscreen mode Exit fullscreen mode

当谈到路线时，Phoenix 和 Rails 之间的真正区别是使用插头时获得的功率。我们将在以后的文章中详细讨论它们。

* * *

现在你知道了。这是凤凰计划的基本架构。还有一些我们在这里没有涉及的组件，比如插头或后台工作。我们将在以后的博客文章中讨论这些高级主题。