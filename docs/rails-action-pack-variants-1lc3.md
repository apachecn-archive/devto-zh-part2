# Rails 操作包变体

> 原文：<https://dev.to/spidergears/rails-action-pack-variants-1lc3>

最近在[雄辩工作室](https://eloquent.studio)的一个项目中，我需要为桌面和移动渲染两个非常不同的视图模板，甚至 Bootstrap responsive design 也不符合要求。

我有点犹豫要不要为这样一个常见的需求推出一个定制的解决方案，于是我在谷歌上搜索了一下，并创建了 RailsGuides。这正是我一直在寻找的。`ActionPack Variants`是`request format`的专门化。随着 Rails 4.1 的发布，这个 api 后来在 Rails 5 中被改进到目前的状态，正如这个 [PR](https://github.com/rails/rails/pull/18939)

在控制器动作的响应块内

```
# app/controllers/patterns_controller.rb
before_action :set_variant, only: :show

def show
  format.html do |html|
    html.phone
    html.tablet
  end
end

private

def set_variant
  case request.user_agent
    when /iPad/i
      request.variant = :tablet
    when /iPhone/i
      request.variant = :phone
    when /Android/i && /mobile/i
      request.variant = :phone
    when /Android/i
      request.variant = :tablet
    when /Windows Phone/i
      request.variant = :phone
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

并将视图模板设置为

```
# app/views/patterns/show.html.erb

# Show page for desktop view 
```

Enter fullscreen mode Exit fullscreen mode

```
# app/views/patterns/show.html+tablet.erb

# Show page for tablet view 
```

Enter fullscreen mode Exit fullscreen mode

```
# app/views/patterns/show.html+phone.erb

# Show page for phone view 
```

Enter fullscreen mode Exit fullscreen mode

不仅仅是设备，变体还可以用于更多不同的用例，比如基于用户角色呈现不同的视图模板。

封面图片致谢:[https://unsplash.com/@bugsster](https://unsplash.com/@bugsster)