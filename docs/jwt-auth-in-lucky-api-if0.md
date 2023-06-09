# 使用幸运 Api 的 JWT 验证

> 原文：<https://dev.to/mikeeus/jwt-auth-in-lucky-api-if0>

尽管 Lucky 非常适合构建完整的应用程序，但我喜欢用 Angular 构建我的前端，所以我通常使用 Lucky 作为 JSON Api。与 Rails Api 相比，我更喜欢它，因为它提供了类型检查、模型与表单和查询的分离，以及操作和路径的组织方式。

JSON api 中我通常需要的一个特性是认证，今天我们将通过 Lucky Api 来设置 JWT 认证。

## 入门 App

首先，我们将使用定义了`User`和`Post`模型的 lucky api 演示应用程序。跑:

```
git clone git@github.com:mikeeus/lucky_api_demo.git
git checkout jwt-auth-0
bin/setup 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过切换到每个部分标题下显示的分支来跟进。或者通过查看 *jwt-auth-10-complete* 来查看成品

## 依赖关系

**分支** : *jwt-auth-0*

我们唯一需要的依赖是 jwt 编码和解码的碎片。我们可以使用[crystal jwt 包](https://github.com/crystal-community/jwt)，所以让我们将以下内容添加到我们的 shard.yml 并运行`shards`。

```
dependencies:
  jwt:
    github: crystal-community/jwt 
```

Enter fullscreen mode Exit fullscreen mode

## 从测试开始

**分支**:*jwt-auth-01-签到测试*

否则我们如何知道应用程序正在工作？好的，在`spec/blog_api_spec.cr`中，我们将添加一个用于认证的描述块，我们的第一个测试将用于登录。

**注意**我从[汉尼斯·考夫勒的博客](https://github.com/hanneskaeufler/blog)得到了`AppVisitor`，这是一个我用作参考的非常幸运的网站。

```
# spec/blog_api_spec.cr
require "./spec_helper"

describe App do
  visitor = AppVisitor.new
  ...
  describe "auth" do
    it "signs in valid user" do
      # create a user

      # visit sign in endpoint

      # check response has status: 200 and authorization header with "Bearer"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 Lucky 的盒子来简化测试数据的生成。我们还将使用 Authentic 的`generate_encrypted_password`方法来生成我们的密码。

```
# spec/support/boxes/user_box.cr
class UserBox < LuckyRecord::Box
  def initialize
    name "Mikias"
    email "hello@mikias.net"
    encrypted_password Authentic.generate_encrypted_password("password")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以在测试中生成一个用户，并使用其电子邮件和密码向我们的 sign_in 端点发出 post 请求。我们将检查响应中是否有正确的状态代码和授权头。

```
# spec/blog_api_spec.cr
...
    it "signs in valid user" do
      # create a user
      user = UserBox.new.create

      # visit sign in endpoint
      visitor.post("/sign_in", ({
        "sign_in:email" => user.email,
        "sign_in:password" => "password"
      }))

      # check response has status: 200 and authorization header with "Bearer"
      visitor.response.status_code.should eq 200
      visitor.response.headers["Authorization"].should_not be_nil
    end
... 
```

Enter fullscreen mode Exit fullscreen mode

现在这个测试将会失败，因为我们没有这个路由的动作或者表单来处理用户创建，所以让我们来构建它们。

## 签到

**分行**:*jwt-auth-02-签到表*

如果我们生成一个普通的幸运应用程序，它将带有已经配置好的[真实的](https://github.com/luckyframework/authentic)，并且将为我们生成几个表单和动作。目前，Lucky api 配置了`Authentic`，但是没有生成这些文件，所以我们需要自己添加并更新它们以适应我们的用例。

### 表格

让我们从用于验证用户凭证的`SignInForm`开始，生成一个令牌并在响应的`Authorization`头中返回它。这个表单将与`Authentic`在新的非 api 应用程序中生成的表单相同，我们还需要创建一个没有生成的表单 mixin `FindAuthenticable`。

```
# src/forms/mixins/find_authenticable.cr
module FindAuthenticatable
  private def find_authenticatable
    email.value.try do |value|
      UserQuery.new.email(value).first?
    end
  end
end

# src/forms/sign_in_form.cr
class SignInForm < LuckyRecord::VirtualForm
  include Authentic::FormHelpers
  include FindAuthenticatable

  virtual email : String
  virtual password : String

  private def validate(user : User?)
    if user
      unless Authentic.correct_password?(user, password.value.to_s)
        password.add_error "is wrong"
      end
    else
      email.add_error "is not in our system"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 动作

**分行**:*jwt-auth-03-完成-签到*

按照 Lucky 的约定，我们将创建两个动作:

```
lucky gen.action.api SignIn::Create 
```

Enter fullscreen mode Exit fullscreen mode

这些命令将在`src/actions/sign_up/create.cr`和`src/actions/sign_in/create.cr`生成类，并生成到`/sign_up`和`/sign_in`的两条 post 路径。

现在我们需要一种从用户生成令牌的方法，我们将把这个方法放在一个`GenerateToken` mixin 中，因为我们将在几个动作中使用它。

```
# src/actions/mixins/auth/generate_token.cr
require "jwt"

module GenerateToken
  def generate_token(user)
    exp = 14.days.from_now.epoch
    data = ({ id: user.id, name: user.name, email: user.email }).to_s
    payload = { "sub" => user.id, "user" => Base64.encode(data), "exp" => exp }

    JWT.encode(payload, Lucky::Server.settings.secret_key_base, "HS256")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要使我们的`User` `PasswordAuthenticatable`与`Authentic`一起使用。如果您计划在注册、密码重置等方面向您的用户发送电子邮件，您可以选择包含`Carbon::Emailable`和`emailable`方法。

```
# src/models/user.cr
class User < BaseModel
  include Carbon::Emailable
  include Authentic::PasswordAuthenticatable

  table :users do
    column name : String
    column email : String
    column encrypted_password : String
  end

  def emailable
    Carbon::Address.new(email)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以将`GenerateToken`包含在我们的`SignIn`动作中，并使用我们的`SignInForm`来完成认证。

```
# src/actions/auth/sign_in.cr
class SignIn::Create < ApiAction
  include GenerateToken

  route do
    SignInForm.new(params).submit do |form, user|
      if user
        context.response.headers.add "Authorization", generate_token(user)
        head 200
      else
        head 401
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用`crystal spec`运行规格，瞧！有用！:)

## 报名

**分支**:*jwt-auth-04-报名-测试*

我不允许在我的博客上注册，所以我为我的`SignIn`动作返回`head 401`，但是当然你可能想在你的博客上实现它。这将是非常相似的`SignIn`功能，有一些细微的区别。我们开始吧。

### 测试

让我们首先编写一个创建用户的测试，确保它返回`Authorization`头，并且我们可以从数据库中查询我们的新用户。

```
# spec/blog_api_spec.cr
describe App do
  ...
  describe "auth" do
    ...
    it "creates user on sign up" do
      visitor.post("/sign_up", ({
        "sign_up:name" => "New User",
        "sign_up:email" => "test@email.com",
        "sign_up:password" => "password",
        "sign_up:password_confirmation" => "password"
      }))

      visitor.response.status_code.should eq 200
      visitor.response.headers["Authorization"].should_not be_nil

      UserQuery.new.email("test@email.com").first.should_not be_nil
    end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

### 表格

**分行**:*jwt-auth-05-报名表*

现在我们的`SignUpForm`将需要一个`PasswordValidations`模块来检查密码，我们将首先创建它。

```
# src/forms/mixins/password_validations.cr
module PasswordValidations
  private def run_password_validations
    validate_required password, password_confirmation
    validate_confirmation_of password, with: password_confirmation
    validate_size_of password, min: 6
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就可以构建我们的注册表单了。

```
# src/forms/sign_up_form.cr
class SignUpForm < User::BaseForm
  include PasswordValidations

  fillable name, email
  virtual password : String
  virtual password_confirmation : String

  def prepare
    validate_uniqueness_of email
    run_password_validations
    Authentic.copy_and_encrypt password, to: encrypted_password
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 动作

**分公司**:*jwt-auth-06-完成-报名*

完成这两件事后，我们可以创建看起来和我们的`SignIn::Create`动作一模一样的`SignUp::Create`动作。运行`lucky gen.action.api SignUp::Create`并填写:

```
# src/actions/sign_up/create.cr
class SignUp::Create < ApiAction
  include GenerateToken

  route do
    SignUpForm.create(params) do |form, user|
      if user
        context.response.headers.add "Authorization", generate_token(user)
        head 200
      else
        head 401
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以运行我们的测试，看着它们通过！

## 保护路线

很好，我们可以登录和退出，但如果我们不能保护我们的资源，这对我们有什么好处呢？由于 lucky inerits 中的每个动作都是从`ApiAction`或`BrowserAction`开始的，所以构建我们自己的`AuthenticatedAction`非常简单，它处理从`Authorization`头获取当前用户，如果无效，则返回`head 401`。

### 测试

**分支**:*jwt-auth-07-认证-动作-测试*

首先，让我们编写测试，以确保我们的功能按预期工作。因为我们使用这个 api 创建帖子，所以让我们确保端点受到保护。我们将创建两个规格，并更新一个旧的将受到我们的变化。

确保在我们的规范中包含`GenerateToken`模块，这样我们就可以模拟一个经过验证的请求。

```
# spec/blog_api_spec.cr
describe App do
  include GenerateToken
  ...
  describe "/posts" do
    ...
    it "creates post" do
      user = UserBox.create

      visitor.post("/posts",
                   new_post_data,
                   { "Authorization" => generate_token(user) })

      visitor.response_body["title"].should eq "New Post"
    end
  end
  ...
  describe "auth" do
    ...
    it "allows authenticated users to create posts" do
      user = UserBox.create

      visitor.post("/posts",
                   new_post_data,
                   { "Authorization" => generate_token(user) })

      visitor.response_body["title"].should eq new_post_data["post:title"]
    end

    it "rejects unauthenticated requests to protected actions" do
      visitor.post("/posts", new_post_data)
      visitor.response.status_code.should eq 401
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的测试肯定会失败，所以让我们构建我们的`AuthenticatedAction`来让它们通过。

### 认证动作

为了做到这一点，我们需要一种从令牌获取用户的方法，所以让我们创建一个名为`UserFromToken`的 mixin 来完成这一任务。

**注意**我选择使用 mixins 来生成和解析令牌，但是您也可以将这些方法直接包含在用户模型中。

```
# src/actions/mixins/user_from_token.cr
module UserFromToken
  def user_from_token(token : String)
    payload, _header = JWT.decode(token, Lucky::Server.settings.secret_key_base, "HS256")
    UserQuery.new.find(payload["sub"].to_s)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在我们的`AuthenticatedAction`类中使用它。

```
# src/actions/authenticated_action.cr
abstract class AuthenticatedAction < ApiAction
  include UserFromToken

  before require_current_user

  getter current_user : User? = nil

  private def require_current_user
    token = context.request.headers["Authorization"]?

    if token.nil?
      head 401
    else
      @current_user = user_from_token(token)
    end

    if @current_user.nil?
      head 401
    else
      continue
    end
  rescue JWT::ExpiredSignatureError
    head 401
  end

  def current_user
    @current_user.not_nil!    
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么？我们使用回调`before`在动作被调用之前运行`require_current_user`。在该方法中，我们从`Authorization`令牌中获取用户，并将其设置为`current_user` getter。如果没有令牌，如果用户不存在或者令牌过期(引发`JWT::ExpiredSignatureError`)，我们返回`401`。

为了方便我们的操作，我们还添加了一个`current_user`方法来别名我们的 nilable getter。

### 保护动作

**分支**:*jwt-auth-09-完成-认证-动作*

现在我们可以在我们的`Posts::Create`行动中使用它。

```
class Posts::Create < AuthenticatedAction # changed this
  route do
    post = PostForm.create!(params, author: current_user) # and this
    json Posts::ShowSerializer.new(post), Status::Created
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以运行我们的规格...然后嘣！受到保护。

这是怎么回事。

## 加入我们

我希望你喜欢这个教程，并发现它很有用。加入我们的 [Lucky gitter 频道](https://gitter.im/luckyframework/Lobby)了解框架的最新进展，或者[查看文档](https://luckyframework.org/guides)了解如何通过 Lucky 将您的应用理念付诸实践的更多信息。