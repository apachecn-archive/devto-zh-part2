# Rails 存在方法

> 原文：<https://dev.to/edwardloveall/the-rails-presence-method-3h46>

> 注:本文最初写于[https://blog.edwardloveall.com/rails-presence-method](https://blog.edwardloveall.com/rails-presence-method)

Rails 有一个我前几天发现的便捷方法:`presence`。它所做的就是返回它自己，如果`present?`。这是一个非常简单的方法:

```
def presence
  self if present?
end 
```

Enter fullscreen mode Exit fullscreen mode

文档中有一个很好的例子可以简化这个问题:

```
state   = params[:state]   if params[:state].present?
country = params[:country] if params[:country].present?
region  = state || country || 'US' 
```

Enter fullscreen mode Exit fullscreen mode

对此:

```
region = params[:state].presence || params[:country].presence || 'US' 
```

Enter fullscreen mode Exit fullscreen mode

这是另一个用例。想象一下，你的应用程序有一个页面，你可以在那里搜索用户。有一个`show.html.erb`模板和两个片段:`user.html.erb`和`no_results.html.erb`。

您的控制器将搜索用户并将他们分配给一个实例变量。如果没有找到用户，我们宁愿显示`no_results`部分而不是空白页面:

```
<% if @users.present? %>
  <%= render @users %>
<% else %>
  <% render 'no_results' %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

用`presence`我们可以使代码更短，但仍然可读。

```
<%= render @users.presence || 'no_results' %> 
```

Enter fullscreen mode Exit fullscreen mode

大多数时候`present?`可能是你正在寻找的，但有时`presence`真的会清理你的代码。牢记在心。