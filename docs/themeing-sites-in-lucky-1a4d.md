# Lucky 中的主题化网站

> 原文：<https://dev.to/jwoertink/themeing-sites-in-lucky-1a4d>

*注意:这是基于我第一轮获得一个多租户应用程序来使用主题。这不是最干净的方法，但目前有效*

假设你使用的是 [Lucky](https://luckyframework.org/) ，并且你向同一个应用程序发送了多个域名，你可能需要对每个网站进行稍微不同的设计。要做到这一点，您需要为每个站点设置主题。在这些例子中，我有一个包含一个`theme`列的`Site`模型。

我们要做的第一件事是为我们的动作创建一个 mixin。在`src/actions/mixins/`中创建一个新文件`themable.cr`。

```
module Themable
  macro included
    expose current_theme
  end

  enum Themes
    Default
    Dark
  end

  # if it returns nil or a non-listed theme
  # just return the default 
  def current_theme : String
    # `current_site` is exposed from a different bit
    t = current_site.theme
    Themes.from_value?(t) ? Themes.from_value?(t).to_s : "Default"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经访问了一个`current_theme`方法，我们只需要包含这个 mixin。在你的`src/actions/browser_action.cr`

```
abstract class BrowserAction < Lucky::Action
  include Themable
  #...

  macro theme_page
    if current_theme == "Default"
      {{ @type }}Page
    else
      case Themes.parse(current_theme)
      when .dark?
        Dark::{{ @type }}Page
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个 BrowserAction 中，我添加了一个宏来帮助确定我们要呈现哪个页面。这将使动作更有意义。

```
class Dashboards::Index < BrowserAction
  get "/" do
    render(theme_page)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个根动作中，我们现在渲染`Dashboards::IndexPage`或者`Dark::Dashboards::IndexPage`。这取决于哪个`current_site`被装载。只要您遵循传统的命名约定，那么这一切都是可行的。

现在是时候设置视图部分了。在我们的`src/pages/main_layout.cr`文件中，我们需要更新一些东西。

```
abstract class MainLayout
  include Lucky::HTMLPage

  needs current_theme : String

  abstract def content

  def render
    html_doctype

    html lang: "en" do
      head do
        #... other stuff
        css_link(dynamic_asset("#{@current_theme.downcase}.css"))
      end
      body do
        content
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

对于这个主布局，我们调用`default.css`或`dark.css`。你有很多不同的方法来处理这件事。也许你有一个`base.css`，然后只是继承和覆盖，或者也许你包括一个二级 css 文件？无论你决定做什么，只要确保你的 webpack mix 文件更新了所有这些主题风格。

你可能遇到的另一个问题是，根据主题的不同，你可能会有完全不同的页眉或页脚。你可能需要在这里做一些额外的逻辑，比如:

```
body do
  if @current_theme == "Dark"
    render_dark_header
  end
  content
end 
```

Enter fullscreen mode Exit fullscreen mode

这当然可以抽象成`src/components/header_component.cr`
中的一个模块

```
module HeaderComponent
  private def render_header(theme)
    case Themable::Themes.parse(theme)
    when .dark?
      _dark_header
    end
  end

  private def _dark_header
    div(class: "dark") do
      h1("Dark Header")
    end
  end
end

abstract class MainLayout
  #...
  include HeaderComponent

  def render
    #...
    body do
      render_header(@current_theme)
      content
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们只需要主视图部分！为此，我们将使用一个特殊的目录结构。我们目前有`src/pages/dashboards/index_page.cr`，我们将只添加`src/pages/dashboards/dark/index_page.cr`。这打破了等级标准，但对我个人来说更有意义。(至少现在，我确定 3 个月后我会讨厌它 lol)

```
class Dashboards::IndexPage < MainLayout
  def content
    #... home page content here
  end
end

class Dark::Dashboards::IndexPage < ::Dashboards::IndexPage
  def content
    #... dark theme home page content.

    # AND... Bonus! 
    previous_def
    # add additional stuff that's on dark theme and not default
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

真的，基本就是这样。其他一切都只是你的个人喜好，或者你可能需要的额外的东西。

如果你正在读这篇文章，并且你能想到一些更干净的方法，请在下面评论！