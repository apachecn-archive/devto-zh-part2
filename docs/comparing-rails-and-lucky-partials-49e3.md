# 比较 Rails 和 Lucky: Partials

> 原文：<https://dev.to/edwardloveall/comparing-rails-and-lucky-partials-49e3>

*注:本文原载于此:[https://blog . edwardloveall . com/comparisng-rails-and-lucky-partials](https://blog.edwardloveall.com/comparing-rails-and-lucky-partials)*

如果您是 Rails 开发人员，您很可能使用过 partials。它们是将一个视图分割成许多可重用部分的好方法。

考虑显示一个搜索表单:

```
<nav class="main">
  <%= render "search" %>
</nav> 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`render`插入文件名为`_search.html.erb`的片段。在这个实例中，它将呈现一个输入字段和一个按钮来获取搜索结果。当我们想在整个应用程序中使用这个搜索表单时，这尤其有用。

[Lucky](https://luckyframework.org) 也有能力[重用页面的一部分](https://luckyframework.org/guides/rendering-html/#extract-partials-and-shared-code)，但是方式略有不同。这些差异造就了一种更加精简和安全的方法。

Lucky 将以编程方式生成 HTML，而不是使用模板。例如:

```
def content
  nav class: "main" do
    h1 "My Awesome App"

    link "Sign In", to: Users::New
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这被转换成 HTML。这是在普通 HTML 和 Ruby 之间来回切换的一种全新的方式。但是渲染局部效果更好。Lucky 中的分音称为*成分*。下面是一个使用方法。

在`src/components/searches/search_component.cr`中，您将创建一个模块:

```
module Searches::SearchComponent
  def render_search(search_form : SearchForm)
    form_for Searches::New do
      text_input search_form.query
      submit "Search"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这与我们在 Rails 中的`_search.html.erb`部分相同。如果我们想在页面上使用它，我们可以包含我们的新模块并调用`render_search`方法:

```
include Searches::SearchComponent

needs search_form : SearchForm

def content
  nav class: "main" do
    h1 "My Awesome App"
    render_search @search_form

    link "Sign In", to: Users::New
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很像在 Rails 中渲染局部，结果*和*非常相似。这里最大的不同是 Lucky 是用水晶写的，用的是[类型检查](https://en.wikipedia.org/wiki/Type_system#Static_type_checking)。类型检查确保调用方法的唯一方式是传递所有参数。

我们的`render_search`方法需要一个`SearchForm`对象。因为类型检查，除非我们传递一个`SearchForm`，否则没有办法调用那个方法。

将这与 Rails `render`方法进行比较。我们不能保证一个`SearchForm`总是对部分可用。我敢打赌，任何使用 Rails 一年或更长时间的人都经历过这种痛苦。

幸运让你不可能忘记这些需要的物品。如果我们忽略了上面的`@search_form`，应用程序甚至不会编译，更不用说运行时崩溃了。

> 换句话说，要使用分部或组件，我们需要某种标识符。对于 Rails 来说，唯一的标识符是文件名，*而不是*文件中使用的对象。在 Lucky 的例子中，它是方法的名字和你传递给它的对象，*包括*在里面使用的对象。如果每次都不能通过所有对象，app 会拒绝编译。

当然，我们可以(也应该！)经常使用这个技巧:

```
render_search(@search_form)
render_sign_in(@sign_in_form)
banner(@annoucments)
user_list(@users) 
```

Enter fullscreen mode Exit fullscreen mode

我还没决定给这些命名的最佳方式。它们可能都是`render`或`display`，只是通过它们的方法参数来区分。或者也许他们都像`render_search_form`一样啰嗦。或者别的什么！我还没有决定最好的模式。

与 Lucky 一起工作时，反复出现的一件事就是安全。尽管 Rails 很棒，但我们经常会发现自己用一个`nil`或不完整的对象来呈现一个视图。因为 Lucky 有 Crystal 作为基础，所以它是一个比 Rails 更安全的框架。安全带来的好处是更少的撞车和不必要的副作用。但是它也支持灵活性和开发人员的信心。