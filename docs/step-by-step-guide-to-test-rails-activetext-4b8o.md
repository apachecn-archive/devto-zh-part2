# 测试 Rails ActiveText 的分步指南

> 原文：<https://dev.to/adrienpoly/step-by-step-guide-to-test-rails-activetext-4b8o>

## 🚧❌编辑的 ActionText 现在在 Rails 6 中完全发布，不要遵循这个为预览版编写的指南

你可能看过 DHH 关于 Rails 6 即将推出的 ActionText 功能的视频。这是创建与视频中完全相同的示例的分步指南。

⚠️:如果你和我一样，从来没有运行过 Rails 的 edge 版本，不知道该怎么做，那么这是给你的

## 从 Rails 的 Edge 版本创建新的 app

### 克隆 Rails 回购

```
git clone https://github.com/rails/rails.git
cd rails
bundle install 
```

Enter fullscreen mode Exit fullscreen mode

### 用 Rails Edge 创建一个新的应用程序 YABE (YABE:又一个博客例子)

我们希望在同一个目录下创建一个新的应用程序，并使用本地安装的 Rails 生成器

```
#move out of rails folder first
cd ..

#create the app with the local rails generator and the edge flag to use GH master branch
rails/railties/exe/rails new yabe --edge

cd yabe 
```

Enter fullscreen mode Exit fullscreen mode

### 启动 app

⚠️记得总是使用本地 rails 命令:`bin/rails`

```
bin/rails s 
```

Enter fullscreen mode Exit fullscreen mode

访问`http://localhost:3000`

现在应该会显示 rails 启动屏幕

**Rails 版本:6.0.0.alpha** 🎉🎉

## 安装动作文本

### 添加 ActionText gem(和 image variants for active Storage):

```
# Gemfile
gem "actiontext", github: "rails/actiontext", require: "action_text"
gem "image_processing", "~> 1.2" # for Active Storage variants 
```

Enter fullscreen mode Exit fullscreen mode

### 安装 gem、资产、npm 依赖项和迁移

```
bundle install
bin/rails action_text:install
bin/rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

### 脚手架立柱

```
bin/rails g scaffold post title:string
bin/rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

### 添加富文本字段进行发布

```
# app/models/post.rb
class Post < ApplicationRecord
  has_rich_text :content
end 
```

Enter fullscreen mode Exit fullscreen mode

活动文本为管理`RichText`带来了一个多态模型

### 向表单添加字段

**<% = form . rich _ text _ area:content %>**:rich _ text _ area 是显示矩阵
的新内容类型

```
<%#app/views/posts/_form.html.erb %>
<%= form_with(model: post) do |form| %>
  …
  <div class="field">
    <%= form.label :content %>
    <%= form.rich_text_area :content %>
  </div>
  …
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

### 最后，在页面上显示富文本:

```
<%= @post.content %> 
```

Enter fullscreen mode Exit fullscreen mode

### 更新 PostsController 中的强参数

```
def post_params
  params.require(:post).permit(:title, :content)
end 
```

Enter fullscreen mode Exit fullscreen mode

### 开始写博客

```
http://localhost:3000/posts 
```

Enter fullscreen mode Exit fullscreen mode

## 我的 2 分钱

*   我的第一次尝试是使用 Rails 的 edge 版本。
*   关于 ActiveText，我对现在在 Rails 应用程序中添加富文本编辑是如此简单印象深刻。
*   我爱特里克斯不要误会我的意思，但我想有一个抽象层，以潜在地为编辑器的其他前端解决方案。