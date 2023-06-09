# 在没有 search_form_for 的情况下创建搜索参数

> 原文：<https://dev.to/husteadrobert/creating-ransack-params-without-searchformfor-4k8l>

有时你在一个区域有一个搜索框，但是你想用一个完全独立的控制器和视图来显示搜索结果。试图用一个像搜索这样的 gem 来做这件事可能有点令人困惑，所以本教程的目的是给出一个基本的运行说明，如何在一个控制器动作中实现搜索，并在另一个控制器动作中显示结果。

首先，你需要确保洗劫宝石在你的宝石文件中，然后运行`bundle install`。你可以在这里找到关于搜索的更多文档:[GitHub-active record-hackery/soracy:基于对象的搜索。](https://github.com/activerecord-hackery/ransack)本教程假设了如何使用搜索的基本知识。

通常情况下，当使用搜索时，你会有一个使用搜索提供的`search_form_for`助手的表单。大概是:

```
# in index.html.erb

<%= search_form_for @q do |f| %>

  # Search if the name field contains...
  <%= f.label :name_cont %>
  <%= f.search_field :name_cont %>

  <%= f.submit %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

在你的控制器中，你会看到这样的东西:

```
# in ItemsController.rb

def index
  @q = Person.ransack(params[:q])
  @people = @q.result(distinct: true)
end 
```

Enter fullscreen mode Exit fullscreen mode

很标准的东西。但是如果你想用搜索结果呈现不同的视图呢？例如，您想使用搜索控制器将用户发送到特定的“搜索结果”页面？

要做到这一点，您需要从用户那里获得搜索查询，并使用基本的表单帮助器以一种 sorava 希望接收的方式对其进行格式化。具体来说，sorack 想要一个带有键' q '的参数，它包含另一个 hash 的值。“q”的哈希值是单个键值对，其中键是包含搜索应该如何查找对象的方法的字符串，该键的值是来自用户的实际搜索查询的字符串。

基本上，您的 params 变量应该是这样的:

```
params = {"q": {"name_or_description_or_id_cont": "Actually query from the User"}} 
```

Enter fullscreen mode Exit fullscreen mode

您可以创建这个“q”参数，它要求显式地包含以下内容:

```
# in whatever view you'd like users to search from

<%= form_tag search_path, method: :get do %>
  <%= text_field_tag :"q[name_or_description_or_id_cont]" %>
  <%= submit_tag "Search", data: { disable_with: "Searching..." }
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

这个表单将创建我们的“q”参数，并按照 sorava 想要的方式对其进行格式化，然后将其发送到您在路线中设置的任何控制器和动作。当然，你可以根据自己的需要改变`name_or_description_or_cont`。

在你想用来显示结果的控制器中，你会有正常的搜索过程

```
 # in whatever controller you'd like to handle the search request

  def search
    @q = Item.ransack(params[:q])
    @items = @q.result
  end 
```

Enter fullscreen mode Exit fullscreen mode

现在可以在视图中使用`@items`实例变量来显示查询结果。

祝你好运，并快乐编码！