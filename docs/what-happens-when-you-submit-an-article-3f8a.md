# 提交文章会怎么样？

> 原文：<https://dev.to/antogarand/what-happens-when-you-submit-an-article-3f8a>

# 开发的引擎盖下(第一部分)

本系列文章将揭开 [dev.to](////dev.to) 源代码的秘密，帮助世人理解和改进这个应用程序。

源代码在 [github](https://github.com/thepracticaldev/dev.to) 上有，贡献的话[会得到一个很酷的徽章](https://dev.to/badge/dev-contributor)！

声明:我不了解 ruby，也不了解 ruby on rails，所以这篇文章可能有不正确或缺失的地方。请随意指出这些，我会尽力改正的！

# 简介

提交文章很容易吧？

你只需要按下`SAVE POST`按钮，就可以了！

有更多的复杂性，在这篇文章中，我将揭示幕后发生的神奇！

# 应用概述

Dev.to 在后端使用 [Ruby On Rails](https://rubyonrails.org/) ，在前端使用 [Preact](https://preactjs.com/) 。

后端托管一个 REST api，前端使用这些来访问和发布数据。

前端是一个[单页面应用](https://en.wikipedia.org/wiki/Single-page_application)，同时也是[服务器端渲染](https://medium.freecodecamp.org/what-exactly-is-client-side-rendering-and-hows-it-different-from-server-side-rendering-bd5c786b340d)。

这意味着如果你直接访问`dev.to/new`,服务器将为你生成所有的 HTML，准备好供你的浏览器显示。
然后，每当加载捆绑的 preact 脚本时，我们就获得了 SPA 功能:当试图访问一个新页面时，它将被 JavaScript 获取，preact 将使用接收到的 html 更新页面内容。

# 显示新的文章视图

好吧，你想写一篇文章。

首先，你抬头看向 dev.to/new 的。

Ruby on rails 使用 GET 协议在 [/config/routes](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/config/routes.rb#L230) 中检查其路由，以找到 [/new](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/config/routes.rb#L230) 。

这条路径告诉它加载`articles`控制器和`new`方法。

```
get "/new" => "articles#new"
get "/new/:template" => "articles#new"

get "/pod" => "podcast_episodes#index"
get "/readinglist" => "reading_list_items#index" 
```

Enter fullscreen mode Exit fullscreen mode

这个控制器可以在[/app/controllers/articles _ controller . Rb](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/controllers/articles_controller.rb#L41)下找到。

在加载`new`方法之前，将执行一些权限检查。
这些都是在控制器上声明的，包括确保你登录和防止被禁止的用户创建文章等方法。

```
 class ArticlesController < ApplicationController
  include ApplicationHelper
  before_action :authenticate_user!, except: %i[feed new]
  before_action :set_article, only: %i[edit update destroy]
  before_action :raise_banned, only: %i[new create update]
  before_action :set_cache_control_headers, only: %i[feed]
  after_action :verify_authorized
// ... 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成这些，就调用`new`方法:

```
 def new
    @user = current_user
    @tag = Tag.find_by_name(params[:template])
    @article = if @tag&.submission_template.present? && @user
                 authorize Article
                 Article.new(body_markdown: @tag.submission_template_customized(@user.name),
                             processed_html: "")
               else
                 skip_authorization
                 if params[:state] == "v2" || Rails.env.development?
                   Article.new
                 else
                   Article.new(
                     body_markdown: "---\ntitle: \npublished: false\ndescription: \ntags: \n---\n\n",
                     processed_html: "",
                   )
                 end
               end
end 
```

Enter fullscreen mode Exit fullscreen mode

这非常简单:它检查您是否正在使用模板(又名。使用路径`/new/:template`)，并加载这个模板，或者创建一个通用的[前体](https://jekyllrb.com/docs/front-matter/)体。

Article.new 代表`New Article View`，在[/app/views/articles/new . html . erb](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/views/articles/new.html.erb)
下有

```
<% title "New Article - DEV" %>

<% if user_signed_in? %>
  <% if params[:state] == "v2" || Rails.env.development? %>
    <%= javascript_pack_tag 'articleForm', defer: true %>
    <%= render 'articles/v2_form' %>
  <% else %>
    <%= render 'articles/markdown_form' %>
  <% end %>
<% else %>
  <%= render "devise/registrations/registration_form" %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

这将根据我们的条件加载正确的视图，通常是[articles/markdown _ form](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/views/articles/_markdown_form.html.erb)

```
<%= form_for(@article, html: {id:"article_markdown_form"}) do |f| %>
  <% if @article.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@article.errors.count, "error") %> prohibited this article from being saved:</h2>

      <ul>
      <% @article.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
<% end %>

<!-- ... --> 
```

Enter fullscreen mode Exit fullscreen mode

这个表单呈现了您通常在访问`dev.to/new`时看到的 HTML，我们终于到了！
生成的 HTML 在 Ruby On Rails 的 magic 中的某个点被用作[/app/views/layouts/application . HTML . erb](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/views/layouts/application.html.erb#L90)的主体。

# 保存文章

好吧，你写了一篇关于本·哈尔彭的网站有多好的精彩文章，现在你想把它发表给所有人看！

你已经将`published`值设置为`true`，然后按下这个蓝色的大`SAVE POST`按钮。接下来会发生什么？

您的 HTML 被加载，Preact 被加载，它监听保存按钮的 click 事件。

## 前端

我们现在在前端代码中，在[/app/JavaScript/article-form/article form . jsx](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/javascript/article-form/articleForm.jsx#L333)下。

按钮本身在[elements/publish toggle . jsx](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/javascript/article-form/elements/publishToggle.jsx#L15)下，我们的`articleForm.jsx`为点击添加了一个事件监听器。

publisshtogle . jsx:t0]

```
<button onClick={onPublish}>
  {published ? 'SAVE CHANGES' : 'PUBLISH' }
</button> 
```

Enter fullscreen mode Exit fullscreen mode

articleForm.jsx:

```
<PublishToggle
  published={published}
  onPublish={this.onPublish}
  onSaveDraft={this.onSaveDraft}
  onChange={linkState(this, 'published')}
  // ...
/> 
```

Enter fullscreen mode Exit fullscreen mode

articleForm.jsx:

```
onPublish = e => {
  e.preventDefault();
  this.setState({submitting: true, published: true})
  let state = this.state;
  state['published'] = true;
  submitArticle(state, this.handleArticleError);
}; 
```

Enter fullscreen mode Exit fullscreen mode

`submitArticle`函数是从[导入的。/行动](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/javascript/article-form/actions.js#L21)。

actions.js - submitArticle

```
export function submitArticle(payload, errorCb, failureCb) {
  const method = payload.id ? 'PUT' : 'POST'
  const url = payload.id ? '/api/articles/'+ payload.id : '/api/articles'
  fetch(url, {
    // ...
    body: JSON.stringify({
      article: payload,
    })
  })
  .then(response => response.json())
  .then(response => {
    if (response.current_state_path) {
      window.location.replace(response.current_state_path);
    } else {
      errorCb(response)
    }
  })
  .catch(failureCb);
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，一旦点击`SAVE ARTICLE`按钮，就会发生以下情况:

*   基于当前的`state`变量创建文章
*   文章发送至`/api/articles`
*   保存完成后，我们会重定向到它的新 URL。

我们现在可以开始深入了解后端了！

## 后端

我们现在从前端以 JSON 文件的形式收到一篇文章，在通过 POST 的`/api/articles`路径上。

### 路由

同样，在 [/config/routes.rb](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/config/routes.rb#L47) 文件中，我们需要搜索我们的端点。

有一个包含我们的[文章](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/config/routes.rb#L47)资源的 [api](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/config/routes.rb#L44) 名称空间。

[Ruby on Rails 资源将一些默认的 CRUD 动词映射到它们各自的方法](https://guides.rubyonrails.org/routing.html#crud-verbs-and-actions)，所以在我们的例子中`POST`方法将调用`articles#create`方法。

routes.rb

```
namespace :api, defaults: { format: "json" } do
  scope module: :v0,
        constraints: ApiConstraints.new(version: 0, default: true) do
    resources :articles, only: %i[index show create update] do
      collection do
        get "/onboarding", to: "articles#onboarding"
      end
    end
    resources :comments
// ... 
```

Enter fullscreen mode Exit fullscreen mode

### 控制器

我们现在在[/app/controllers/articles _ controller](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/controllers/articles_controller.rb#L86)，在`create`方法下:

```
def create
  authorize Article
  @user = current_user
  @article = ArticleCreationService.
    new(@user, article_params, job_opportunity_params).
    create!
  redirect_after_creation
end 
```

Enter fullscreen mode Exit fullscreen mode

### 服务

这个方法调用 [ArticleCreationService](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/services/article_creation_service.rb#L10) ，它将创建我们的文章！

```
def create!
  raise if RateLimitChecker.new(user).limit_by_situation("published_article_creation")
  article = Article.new(article_params)
  article.user_id = user.id
  article.show_comments = true
  if user.organization_id.present? && article_params[:publish_under_org].to_i == 1
    article.organization_id = user.organization_id
  end
  create_job_opportunity(article)
  if article.save
    if article.published
      Notification.send_all(article, "Published")
    end
  end
  article.decorate
end 
```

Enter fullscreen mode Exit fullscreen mode

这个服务创建了一个新的 [Article](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/models/article.rb) 模型的实例，并保存它。

### 型号

有了 Ruby on Rails，我们的模型就是[活动记录](https://guides.rubyonrails.org/active_record_basics.html)，并且附带了一点魔法。

虽然我不会深入研究对象的数据库映射部分，但我觉得有趣的是在创建或保存对象时调用的之前的[方法。](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/models/article.rb#L43) 

```
before_validation :evaluate_markdown
before_validation :create_slug
before_create     :create_password
before_save       :set_all_dates
before_save       :calculate_base_scores
before_save       :set_caches
after_save :async_score_calc, if: :published 
```

Enter fullscreen mode Exit fullscreen mode

在确保对象有效之前，将调用`before_validation`方法。

*   [evaluate_markdown](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/models/article.rb#L295) 会将我们的 markdown 转换成 HTML
*   [create_slug](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/models/article.rb#L431) 将为 URL 创建一个最有可能的唯一 slug
*   [create_password](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/models/article.rb#L444) 将生成一个唯一的预览密码值

其余方法的名称应该非常明确。

该模型还将对其属性执行[多次验证](https://github.com/thepracticaldev/dev.to/blob/92a494a1ea3ee3e02c208017eb1af1b6ef7466a7/app/models/article.rb#L20)。

```
 validates :slug, presence: { if: :published? }, format: /\A[0-9a-z-]*\z/,
                   uniqueness: { scope: :user_id }
  validates :title, presence: true,
                    length: { maximum: 128 }
  validates :user_id, presence: true
  validates :feed_source_url, uniqueness: { allow_blank: true }
  validates :canonical_url,
            url: { allow_blank: true, no_local: true, schemes: ["https", "http"] },
uniqueness: { allow_blank: true } 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

唷，这篇文章现在有救了！对于一个简单的动作来说，这是很大的工作量。

简单回顾一下，要查看一篇文章，我们加载正确的*控制器*，它加载一个*视图*并将其呈现到页面上。

当试图执行 CRUD 操作时，我们基于我们的 *API 资源*找到正确的路径，它加载了一个*控制器*。这个控制器可以使用*服务*与数据交互，自己使用*模型*与数据库交互。

* * *

既然技术方面已经讨论过了，我想得到一些关于这篇文章的反馈。

我对这个系列没有什么目标:

1.  帮助人们浏览*大*代码库并理解它们的架构
2.  降低像这个网站这样的开源项目的投稿门槛。

这就是为什么反馈很重要。这对你理解来源有帮助吗？也许有你想看的特别的东西？

请在下面评论中告诉我，我会尽全力改进这个系列！