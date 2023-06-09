# 如何向 Rails 应用程序添加“预览模式”样式的功能

> 原文：<https://dev.to/husteadrobert/how-to-add-preview-mode-style-functionality-to-a-rails-application-39f>

在 Rails 中实现“预览模式”风格的功能看似简单。假设我们有一个向客户介绍商品的应用程序。我们有一个普通的客户域，和一个后端管理域，我们在那里执行基本的 CRUD 操作，如创建，编辑和更新项目的信息。

现在，我们的作者希望能够预览他们正在创建的项目，以查看发布后的外观。但是，我们不希望普通用户能够访问我们的草稿项目。如果用户访问的项目页面是草稿，他们应该会得到一个 404 错误。然而，作者应该能够访问一个项目页面，这是一个草稿，看看它会是什么样子(用横幅或其他东西表明页面处于预览模式)。

让我们来实现这个功能。本教程将使用 Cancan 进行资源授权，但它可以根据您当前使用的库进行更改。

## 给你的模型添加状态栏。

首先，我们应该在模型中添加一个`status`列。实际上，该列的值应该是“草稿”或“已发布”。因此，该列的类型应该是 String，并且默认为“draft ”,因此默认情况下不会显示新创建的项目。此外，我们应该设置它，以便 null 是不可接受的。以下迁移将满足我们的目的:

```
 # In your Migration file

  def change
    add_column :items, :status, :string, default: "draft", null: false
  end 
```

Enter fullscreen mode Exit fullscreen mode

## 更新模型

在您的`item.rb`模型文件中，我们应该为我们的状态添加一些常量，以及在我们的视图中容易访问它们的范围。

对于我们的常数:

```
 # In item.rb

  STATUS_PUBLISHED = "published".freeze
  STATUS_DRAFT = "draft".freeze
  enumerize :status, in: [STATUS_PUBLISHED, STATUS_DRAFT] 
```

Enter fullscreen mode Exit fullscreen mode

和范围:

```
 # In item.rb

  scope :published, -> { where(status: STATUS_PUBLISHED) }
  scope :draft, -> { where(status: STATUS_DRAFT) } 
```

Enter fullscreen mode Exit fullscreen mode

## 改变物品#显示查找方法

我们需要改变项目控制器查找项目的方式。您可能已经在使用资源认证库(比如 CanCan ),但是我们需要绕过这个默认功能。在 Cancan 的情况下，确保从任何资源加载和授权中排除 show 操作。

我们想看看是否有一个“草稿”参数被传递给控制器。如果是这样，我们可以在所有的项目中寻找我们的项目。如果请求不是寻找草稿，那么我们应该只在已发布的项目中寻找。

```
 def show
    items = params[:draft].present? ? Item.all : Item.published
    @item = items.find(params[:id])
  end 
```

Enter fullscreen mode Exit fullscreen mode

假设我们有一个 id 为 4 的项目，它的状态是`draft`。如果用户访问~/items/4，他们将得到一个 404 错误(因为`services.find(params[:id])`将什么也没找到，因为它只在`Services.published`中查找)。但是，如果你的作者访问~/items/4？draft=true，他们将能够以草稿模式查看项目。

普通用户查找上面的 url，并且他们添加了？草稿=真的吗？他们不能在草稿模式下看到该项目吗？这根本不是我们想要的。让我们确保只有我们的作者可以访问该页面。

## 更新 CanCan(或者你使用的任何库)来删除普通用户的权限

您需要确保普通用户只能读取状态为“已发布”的项目。查看您选择的授权库的文档，但是对于 Cancan，您可能会有这样的文档:

```
# in models/ability.rb

class Ability
  include CanCan::Ability

  def initialize(user)
    if user.type == "writer"
      can :manage, Item
    else
      can :read, Item do |item|
        item.status == Item::STATUS_PUBLISHED
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这确保了非“作者”用户能够看到项目。但是，他们只能看到已发布的项目。因此，如果用户试图访问~/services/2？draft=true，Cancan 将在 ItemsController 尝试查找记录之前停止它们。

我们现在有了“预览模式”风格功能的所有后端代码。剩下的只是更新我们的观点。

## 添加更新物品状态的功能

无论您在哪里使用表单设置来创建项目，请确保包含一个选择器来选择项目是处于草稿模式还是已发布模式。

或者，您可以在 writer 保存项目时使用按钮。选择权在你，但是保存物品的时候一定要给写手选择权。

## 为作者和普通用户更新视图

在管理域中，添加一种访问草稿视图的方法，类似于:

```
# in views/admin/items/index.html.haml

# when displaying all the Items
%li= link_to "Preview", service_path(service, draft: true), target: "_blank" 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们链接到普通用户域中的服务页面，同时还包括设置为`true`的`draft`参数。如果您还记得，这在 ItemsController 中用于决定是从所有项目中查找，还是只从已发布的项目中查找。

在普通用户视图中，如果您在项目#show 之外的位置显示服务，请确保您仅显示已发布的服务。在向用户显示服务时，使用之前创建的`published`范围。

例如，如果您想要列出与某个类别相关的所有项目，您可能会得到如下结果:

```
@category.items.each do |item|
  #display code
end 
```

Enter fullscreen mode Exit fullscreen mode

但是你需要改成这样:

```
@category.items.published.each do |item|
  #display code
end 
```

Enter fullscreen mode Exit fullscreen mode

## 在草图视图顶部添加内容

最后，我们希望有一些东西向作者表明他们正在查看的服务处于预览模式。在正常的 Service#Show 视图文件中，在顶部添加一些 HTML:

```
- if params[:draft].present?
  %p.draftMessage You are in Preview Mode 
```

Enter fullscreen mode Exit fullscreen mode

然后更新 CSS，让它看起来你认为合适的样子！

你有它！现在，您已经在应用程序中实现了一个基本的“预览模式”特性。祝你好运，编码快乐！