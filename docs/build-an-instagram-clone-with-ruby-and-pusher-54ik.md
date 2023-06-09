# 用 Ruby 和 Pusher 构建 Instagram 克隆

> 原文：<https://dev.to/troy34/build-an-instagram-clone-with-ruby-and-pusher-54ik>

[https://thepractical dev . S3 . Amazon AWS . com/I/nxqfrbmepu 3 pwj 7 fmb 1d . gif](https://thepracticaldev.s3.amazonaws.com/i/nxqfrbmepu3pwj7fmb1d.gif)

上周，我参加了[首届开发竞赛](https://dev.to/devteam/first-ever-dev-contest-build-a-realtime-app-with-pusher-4nhp?utm_source=additional_box&utm_medium=internal&utm_campaign=devteam_boosted&booster_org=devteam)，用 Pusher 开发了一个实时应用。在非常忙碌的一天和大约 4 个小时的编码后，我提交了一个 Instagram 克隆。

你可以看看我下面的条目，看看直播 app 和 Github repo。也很高兴得到你的❤️ &🦄在我的入门帖上。

[![troy34 image](img/9ff20fd28c97d3e5978574e8151c1682.png)](/troy34) [## [词条] Instagram 克隆

### Chuks Opia

#pushercontest#rubyrails#instagram#pusher](/troy34/entry-instagram-clone-2gbp)

在这篇文章中，我将带你了解我是如何构建 Instagram 克隆应用的。

### 设置应用程序

我使用 Ruby on Rails 构建了这个应用程序。我利用[设备](Devise)进行用户认证，利用[载波](https://github.com/carrierwaveuploader/carrierwave)进行图像上传。
如果你已经安装了 Ruby 和 Rails，并且想继续使用，运行下面的命令来生成一个新的 Rails 应用程序。

```
$    rails new instaclone -T --database=postgresql 
```

在应用程序的根目录中，打开 Gemfile 并添加以下内容，然后在终端中运行`bundle install`:

```
# Gemfile

gem 'bootstrap', '~> 4.1.0'
gem 'jquery-rails'
gem 'pusher'
gem 'figaro'
gem 'devise'
gem 'will_paginate', '~> 3.1.0'
gem 'carrierwave'
gem "fog-aws" 
```

### 数据库设置

为了让我们的应用程序启动并运行，我们将为它创建一个数据库。您可以查看这篇关于如何创建 Postgres 数据库以及相关用户和密码的文章。

一旦您有了数据库的详细信息，在您的`database.yml`文件中，在 development 键下，添加以下代码:

```
# config/database.yml

...
development:
  <<: *default
  database: instaclone_development // add this line if it isn't already there
  username: database_user // add this line
  password: user_password // add this line
... 
```

确保上面代码中输入的用户名和密码可以访问`instaclone_development`数据库。之后，运行以下代码来设置数据库:

```
#    setup database
$    rails db:setup 
```

### 用户认证

设置好数据库后，我们将使用 Devise 设置用户身份验证。Devise 是一个灵活的 Ruby on Rails 认证解决方案。它可以帮助您在几秒钟内设置用户身份验证。在您的终端中，运行以下命令:

```
$    rails generate devise:install 
```

以上命令将安装 Devise。此时，控制台中会出现一些指令，其中一条涉及到向您的`application.html.erb`文件添加一些代码。我们还将把我们的 Pusher 脚本添加到文件中。

用这里的中的[替换你的`app/views/layouts/application.html.erb`文件中的代码](https://github.com/9jaswag/instaclone/blob/develop/app/views/layouts/application.html.erb)

接下来，我们将生成我们的身份验证视图页面，然后使用 Devise 创建用户模型。在您的终端中，运行以下命令:

```
#    generate Devise view pages
$    rails generate devise:views

#    generate user model
$    rails generate devise user

#    generate migration to add extra columns to the user model
$    rails generate migration add_username_to_users username:string:uniq

#    generate a likes model
$    rails generate model like like_count:integer post:references 
```

现在我们已经生成了迁移文件，让我们在运行迁移之前对一些文件进行一些修改。

使用以下内容更新应用程序控制器中的代码:

```
# app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  before_action :configure_permitted_parameters, if: :devise_controller?
  before_action :authenticate_user!

  protected

  def configure_permitted_parameters
    added_attrs = [:username, :email, :password, :password_confirmation, :remember_me]
    devise_parameter_sanitizer.permit :sign_up, keys: added_attrs
    devise_parameter_sanitizer.permit :account_update, keys: added_attrs
  end
end 
```

此外，用以下内容更新 likes 迁移文件中的代码

```
# db/migrate/20180524215616_create_likes.rb

class CreateLikes < ActiveRecord::Migration[5.1]
  def change
    create_table :likes do |t|
      t.integer :like_count, default: 0
      t.references :post, foreign_key: true

      t.timestamps
    end
  end
end 
```

现在，我们准备好运行我们的迁移并查看我们的应用程序。在您的终端中，运行以下命令:

```
#    run database migrations
$    rails db:migrate 
```

运行迁移之后，通过运行 rails s 在您的终端上启动开发服务器。在您的浏览器中访问 [http://localhost:3000](http://localhost:3000) 以查看您的全新应用程序:

### 推杆账户设置

现在我们的应用程序已经启动并运行了，去 Pusher 和[注册一个免费账户](https://dashboard.pusher.com/accounts/sign_up)。
创建推送账户后，选择侧边栏上的**渠道应用**，点击侧边栏底部的**创建渠道应用**按钮，即可创建新的应用。
通过提供表格中要求的基本信息来配置您的应用程序。您还可以选择想要与 Pusher 集成的环境，并提供一些样板设置代码。之后，点击**应用程序密钥**选项卡取回您的密钥。

### 页面样式

为了设计我们的页面，我们将使用 Bootstrap。我们已经在设置应用程序时添加了 Bootstrap，所以我们需要做的就是请求它并添加我们的标记和样式。首先，我们将为我们的帖子生成一个控制器；在您的终端中，运行以下命令:

```
#    generate a posts controller
$    rails g controller posts 
```

用下面提供的每个链接中的代码更新以下文件中的代码:

*   将`app/assets/javascripts/application.js`中的代码替换为[中的代码](https://github.com/9jaswag/instaclone/blob/develop/app/assets/javascripts/application.js)
*   将`app/views/devise/registrations/new.html.erb`中的代码替换为[中的代码](https://github.com/9jaswag/instaclone/blob/develop/app/views/devise/registrations/new.html.erb)
*   将`app/views/devise/sessions/new.html.erb`中的代码替换为[中的代码](https://github.com/9jaswag/instaclone/blob/develop/app/views/devise/sessions/new.html.erb)
*   将您的`app/assets/stylesheets/application.css`文件重命名为`app/assets/stylesheets/application.scss`，并将那里的代码替换为这里的代码
*   将这里的代码添加到您的`app/controllers/posts_controller.rb`文件中。
*   将这里的代码添加到您的`config/routes.rb`文件中。

如果我们在浏览器中重新加载我们的应用程序，我们应该会看到一个可爱的景象。继续创建一个帐户。

如果遇到任何与 application.html.erb 相关的错误，在 config/boot.rb 中，将 ExecJS 运行时从 Duktape 更改为 Node。

```
# config/boot.rb
ENV['EXECJS_RUNTIME'] ='Node' 
```

### 实时发布与推送

为了让用户实时看到我们的帖子，每当用户添加一个帖子，我们都会将其保存到数据库中，并通过在我们的应用程序前端广播新帖子并订阅它来通知 Pusher。
让我们初始化我们的推送客户端。在`config/initializers`文件夹中，创建一个`pusher.rb`文件，并添加以下代码:

```
# config/initializers/pusher.rb

require 'pusher'

Pusher.app_id = ENV["PUSHER_APP_ID"]
Pusher.key = ENV["PUSHER_KEY"]
Pusher.secret = ENV["PUSHER_SECRET"]
Pusher.cluster = ENV["PUSHER_CLUSTER"]
Pusher.logger = Rails.logger
Pusher.encrypted = true 
```

由于我们不想让我们的按键对公众可见，我们将添加 save 它们作为环境变量。为此，我们将利用 Figaro。
在您的终端中运行`figaro install`。它将生成一个`application.yml`文件。在`application.yml`文件中添加您的按键:

```
# config/application.yml

PUSHER_APP_ID: '11234'
PUSHER_KEY: '1234567890'
PUSHER_SECRET: '1234556677'
PUSHER_CLUSTER: 'us' 
```

Pusher 现在已经初始化，每当保存一个新帖子时，让我们通知 Pusher。将下面的代码添加到您的帖子模型中:

```
# app/models/post.rb

class Post < ApplicationRecord
  belongs_to :user
  has_many :likes
  after_create :notify_pusher

  mount_uploader :image, ImageUploader

  validates :caption, presence: true

  def serialised
    {
      id: self.id,
      caption: self.caption,
      user: self.user.username,
      image: self.image_url,
      likes: self.likes[0].like_count
    }
  end

  def notify_pusher
    Pusher.trigger('post', 'new', self.serialised)
  end
end 
```

在上面的代码中，我们有一个在新帖子创建后调用的`notify_pusher`动作。在`notify_pusher`动作中，我们通过触发新帖子事件并传入新帖子来通知 pusher 有新帖子。

此外，每当有新的图像时，我们都必须通知 Pusher。将以下代码添加到 likes 模型中

```
# app/models/like.rb

class Like < ApplicationRecord
  after_save :notify_pusher, on: :create
  belongs_to :post

  def notify_pusher
    Pusher.trigger('post', 'new-like', self.post.serialised)
  end
end 
```

与用户模型一样，我们也通过广播一个`new-like`事件来通知 Pusher 一个新的 like。

为了在我们的应用程序前端实时更新，我们必须订阅我们在服务器端广播的新发布事件。让我们将我们的`app/assets/javascripts/posts.coffee`文件重命名为`app/assets/javascripts/posts.coffee.erb`，并将代码[添加到文件中。在我们添加到上面文件中的代码中，我们为 Pusher 客户端订阅了分别针对帖子和赞的`new`和`new-like`事件。每当有新的活动，我们都会用新的帖子和赞来更新页面。](https://github.com/9jaswag/instaclone/blob/develop/app/assets/javascripts/posts.coffee.erb)

### 结论

好了，如果你已经按照本教程学习到这一步，你应该拥有自己的全功能 Instagram 克隆。Pusher 是一个非常强大的软件，可以帮助你实现任何你能想到的实时功能。

如果你觉得这个帖子有用，请添加❤️ &🦄在这里和我的入口职位。

[![troy34 image](img/9ff20fd28c97d3e5978574e8151c1682.png)](/troy34) [## [词条] Instagram 克隆

### Chuks Opia

#pushercontest#rubyrails#instagram#pusher](/troy34/entry-instagram-clone-2gbp)