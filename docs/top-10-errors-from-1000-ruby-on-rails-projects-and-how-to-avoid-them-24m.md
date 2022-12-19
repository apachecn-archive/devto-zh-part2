# 来自 1000 多个 Ruby on Rails 项目的 10 大错误(以及如何避免它们)

> 原文：<https://dev.to/philnash/top-10-errors-from-1000-ruby-on-rails-projects-and-how-to-avoid-them-24m>

为了回馈开发者社区，在 Rollbar 上，我们查看了数千个项目的数据库，发现了 Ruby on Rails 项目中的 10 大错误。我们将向您展示它们的成因以及如何防止它们发生。如果你避免这些“陷阱”，它会让你成为一个更好的开发者。

因为数据为王，我们收集、分析并排列了 Ruby on Rails 应用程序中的前 10 个。 **[滚动条](https://rollbar.com/)** 收集每个项目的所有错误，并汇总每个错误发生的次数。我们根据 **[指纹](https://docs.rollbar.com/docs/grouping-algorithm/)** 对错误进行分组。基本上，如果第二个错误只是第一个错误的重复，我们将这两个错误归为一组。这为用户提供了一个很好的概览，而不是像您在日志文件中看到的那样巨大的转储。

我们重点关注最有可能影响您和您的用户的错误。为了做到这一点，我们根据不同公司遇到错误的项目数量对错误进行了排序。我们有意查看项目的数量，以便高容量客户不会用与大多数读者无关的错误淹没数据集。

## 以下是 10 大 Rails 错误:

[![Screenshot of Rails Error Statistics](../Images/51b3cf4a830cb84d7040e5921eff1bf3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JR1Pb-pz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/529e081e467ee620417fedbfa90f83814aac2506/5c36c/static/rails-error-stat-1-d10fce70047361819ed77b431df956d2-7539a.png)

你可能已经注意到了一些熟悉的面孔。让我们更深入地研究一下这些错误，看看在您的生产应用程序中是什么导致了这些错误。

我们将提供基于 Rails 5 的示例解决方案，但如果您仍在使用 Rails 4，它们应该会为您指明正确的方向。

## 1。ActionController::RoutingError

我们从一个经典的 web 应用程序开始，Rails 版本的 404 错误。一个`ActionController::RoutingError`意味着一个用户请求了一个在你的应用程序中不存在的 URL。Rails 将记录这一点，这看起来像是一个错误，但在大多数情况下，这不是您的应用程序的错误。

这可能是由指向或来自应用程序内部的不正确链接引起的。它也可能是一个恶意用户或机器人测试您的应用程序的共同弱点。如果是这样的话，你可能会在你的日志中发现这样的内容:

```
ActionController::RoutingError (No route matches [GET] "/wp-admin"): 
```

一个常见的原因是，您可能会得到一个由您的应用程序而不是错误的用户引起的`ActionController::RoutingError`:如果您将您的应用程序部署到 Heroku 或任何不允许您提供静态文件的平台，那么您可能会发现您的 CSS 和 JavaScript 无法加载。如果是这种情况，错误将如下所示:

```
ActionController::RoutingError (No route matches [GET] "/assets/application-eff78fd93759795a7be3aa21209b0bd2.css"): 
```

要解决这个问题并允许 Rails 服务静态资产，您需要在应用程序的`config/environments/production.rb`文件中添加一行:

```
Rails.application.configure do
  # other config
  config.public_file_server.enabled = true
end 
```

如果您对记录由`ActionController::RoutingError`引起的 404 错误不感兴趣，那么您可以通过设置一个 catch all 路由并自己服务 404 来避免它们。 **[这种方法是由 lograge 项目](https://github.com/roidrage/lograge#handle-actioncontrollerroutingerror)** 建议的。为此，在您的`config/routes.rb`文件的底部添加以下内容:

```
Rails.application.routes.draw do
  # all your other routes
  match '*unmatched', to: 'application#route_not_found', via: :all
end 
```

然后将`route_not_found`方法添加到您的`ApplicationController` :

```
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  def route_not_found
    render file: Rails.public_path.join('404.html'), status: :not_found, layout: false
  end
end 
```

在实现这一点之前，您应该考虑了解 404 错误对您是否重要。您还应该记住，在应用程序加载之后挂载的任何 **[路由或引擎都将无法到达](https://github.com/ankane/ahoy/issues/29)** ，因为它们将被捕获所有路由捕获。

## 2。NoMethodError:nil:nil class 的未定义方法“[]”

这意味着你使用方括号符号从一个对象中读取一个属性，但是这个对象丢失了，或者`nil`，因此它不支持这个方法。由于我们正在使用方括号，很可能我们正在通过散列或数组来访问属性，并且在这个过程中遗漏了一些东西。当您从 JSON API 或 CSV 文件中解析和提取数据，或者只是从控制器动作中的嵌套参数中获取数据时，可能会发生这种情况。

假设一个用户通过表单提交地址信息。您可能希望您的参数看起来像这样:

```
{ user: { address: { street: '123 Fake Street', town: 'Faketon', postcode: '12345' } } } 
```

然后，您可以通过调用`params[:user][:address][:street]`来访问街道。如果没有传递地址，那么`params[:user][:address]`将是`nil`，调用`[:street]`将引发一个`NoMethodError`。

您可以对每个参数执行 nil 检查，并使用`&&`操作符提前返回，就像这样:

```
street = params[:user] && params[:user][:address] && params[:user][:address][:street] 
```

虽然这样就可以了，但谢天谢地，现在有了一种更好的方法来访问散列、数组和事件对象(如`ActionController::Parameters`)中的嵌套元素。从 Ruby 2.3 开始，哈希、数组和`ActionController::Parameters`有了 **[`dig`方法](http://ruby-doc.org/core-2.3.0_preview1/Hash.html#method-i-dig)** 。`dig`允许您提供想要检索的对象的路径。如果在任何阶段`nil`被返回，那么`dig`返回`nil`而不抛出`NoMethodError`。要从上述参数中获取街道，您可以调用:

```
street = params.dig(:user, :address, :street) 
```

你不会因此得到任何错误，尽管你需要知道`street`可能仍然是`nil`。

顺便说一句，如果你也在使用点符号挖掘嵌套对象，你也可以在 Ruby 2.3 中使用安全导航操作符安全地完成这项工作。所以，与其调用

```
street = user.address.street 
```

并且为`nil:NilClass`获取一个`NoMethodError: undefined method street`，现在可以调用。

```
street = user&.address&.street 
```

现在，上面的操作与使用`dig`相同。如果地址是`nil`，那么`street`将是`nil`，当你稍后引用`street`时，你将需要处理`nil`。如果所有对象都存在，`street`将被正确分配。

虽然这抑制了向用户显示错误，但如果它仍然影响用户体验，您可能希望在日志或 Rollbar 之类的错误跟踪系统中创建一个内部错误进行跟踪，这样您就可以查看并修复问题。

如果您没有使用 Ruby 2.3 或更高版本，您可以使用上面的 **[ruby_dig gem](https://github.com/Invoca/ruby_dig)** 和 **[ActiveSupport 的`try`](http://api.rubyonrails.org/classes/Object.html#method-i-try)** 来实现类似的结果。

## 3。action controller::invalidatuthenticytoken

列表中的第三点需要仔细考虑，因为它与我们的应用程序的安全性有关。当 POST、PUT、PATCH 或 DELETE 请求丢失或具有不正确的 CSRF(跨站点请求伪造)令牌时，将引发`ActionController::InvalidAuthenticityToken`。

CSRF 是 web 应用程序中的一个潜在漏洞，恶意站点代表不知情的用户向您的应用程序发出请求。如果用户登录了他们的会话，cookies 将随请求一起发送，攻击者就可以以用户身份执行命令。

**[Rails 通过包含所有形式的安全令牌来缓解 CSRF 攻击](http://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf)** ，该令牌被站点所知和验证，但不能被第三方所知。这是由熟悉的`ApplicationController`线
执行的

```
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
end 
```

因此，如果您的生产应用程序引发了`ActionController::InvalidAuthenticityToken`错误，这可能意味着攻击者正在瞄准您站点的用户，但是 Rails 安全措施正在保护您的安全。

不过，您可能无意中收到此错误还有其他原因。

### Ajax

例如，如果从前端发出 Ajax 请求，就需要确保在请求中包含 CSRF 令牌。如果您正在使用 jQuery 和内置的 **[Rails 不引人注目的脚本适配器](https://github.com/rails/rails/tree/master/actionview/app/assets/javascripts)** ，那么这已经为您处理好了。如果你想用另一种方式处理 Ajax，比如使用 **[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)** ，你需要确保包含 CSRF 令牌。对于这两种方法，您都需要确保您的应用程序布局在文档的`<head>`中包含 CSRF meta 标签:

```
<%= csrf_meta_tags %> 
```

这将打印出如下所示的 meta 标记:

```
<meta name='csrf-token' content='THE-TOKEN'> 
```

当发出 Ajax 请求时，读取 meta 标记内容并将其作为`X-CSRF-Token`头添加到头中。

```
const csrfToken = document.querySelector('[name="csrf-token"]').getAttribute('content');
fetch('/posts', {
  method: 'POST',
  body: JSON.stringify(body),
  headers: {
    'Content-Type': 'application/json'
    'X-CSRF-Token': csrfToken
  }
).then(function(response) {
  // handle response
}); 
```

### web hooks/API

有时有正当的理由关闭 CSRF 保护。如果您希望收到来自第三方的对应用程序中某些 URL 的 POST 请求，您不会希望基于 CSRF 阻止它们。如果您正在为第三方开发人员构建 API，或者如果您希望从某个服务接收传入的 webhooks，您可能会处于这种情况。

您可以关闭 CSRF 保护，但要确保将您知道不需要这种保护的终端列入白名单。您可以通过跳过认证在控制器中执行此操作:

```
class ApiController < ApplicationController
  skip_before_action :verify_authenticity_token
end 
```

如果您正在接受传入的 web 挂钩，您应该能够验证请求是否来自可信来源，而不是验证 CSRF 令牌。

## 4。Net::ReadTimeout

当 Ruby 从套接字读取数据的时间比`read_timeout`值长时，就会引发`Net::ReadTimeout`，默认情况下是 60 秒。如果您使用`Net::HTTP`、`open-uri`或 **[`HTTParty`](https://github.com/jnunemaker/httparty)** 发出 HTTP 请求，就会出现此错误。

值得注意的是，这并不意味着如果请求本身花费的时间比`read_timeout`值长，就会抛出错误，只是如果一个特定的读取花费的时间比`read_timeout`长。你可以阅读更多关于 **[`Net::HTTP`和菲利普菲利普](https://felipeelias.github.io/ruby/2017/08/20/net-http-timeouts.html)** 的暂停。

我们可以做几件事来避免出现错误。一旦您理解了抛出错误的 HTTP 请求，您可以尝试将`read_timeout`值调整为更合理的值。正如在上面的文章中，如果你向服务器发出请求，在一次发送之前，需要很长时间来收集响应，你会想要一个更长的`read_timeout`值。如果服务器分块返回响应，那么您将需要一个更短的`read_timeout`。

您可以通过在您使用的相应 HTTP 客户端上设置以秒为单位的值来设置`read_timeout`:

**同网::HTTP**

```
http = Net::HTTP.new(host, port, read_timout: 10) 
```

**同开-uri**

```
open(url, read_timeout: 10) 
```

**同 http party**

```
HTTParty.get(url, read_timeout: 10) 
```

您不能总是相信另一台服务器会在您预期的超时时间内做出响应。如果您可以在后台作业中重试运行 HTTP 请求，就像 **[Sidekiq](https://github.com/mperham/sidekiq)** ，这可以减少来自其他服务器的错误。您将需要处理服务器没有及时响应的情况。

如果您需要在一个控制器动作中运行 HTTP 请求，那么您应该挽救`Net::ReadTimeout`错误，为您的用户提供另一种体验，并在您的错误监控解决方案中跟踪它。比如:

```
def show
  @post = Post.find(params[:slug])
  begin
    @comments = HTTParty.get(COMMENTS_SERVER, read_timeout: 10)
  rescue Net::ReadTimeout => e
    @comments = []
  @error_message = "Comments couldn't be retrieved, please try again later."
    Rollbar.error(e);
  end
end 
```

## 5。active record::record unique:PG::only violation(活动记录::记录唯一:pg::仅违规)

这个错误消息是专门针对 PostgreSQL 数据库的，但是 MySQL 和 SQLite 的 ActiveRecord 适配器也会抛出类似的错误。这里的问题是，应用程序中的数据库表在一个或多个字段上有唯一的索引，而发送到数据库的事务违反了该索引。这是一个很难完全解决的问题，但让我们先来看一看容易实现的目标。

假设您已经创建了一个`User`模型，并且在迁移过程中确保了用户的电子邮件地址是惟一的。迁移可能如下所示:

```
class CreateUsers < ActiveRecord::Migration[5.1]
  def change
    create_table :users do |t|
      t.string :email
      t.timestamps
    end
    add_index :users, :email, unique: true
  end
end 
```

为了避免大多数的`ActiveRecord::RecordNotUnique`实例，你也应该为你的`User`模型添加一个唯一性验证。

```
class User < ApplicationRecord
  validates_uniqueness_of :email
end 
```

如果没有这种验证，当调用`User#save`时，所有的电子邮件地址都将被发送到数据库，如果它们不是唯一的，就会引发错误。然而，验证不能保证这不会发生。要获得完整的解释，你应该阅读`validates_uniqueness_of`文档 的 **[并发性和完整性部分。简单来说，Rails 唯一性检查容易出现基于多个请求的操作顺序的竞争情况。作为一个竞争条件，这也使得这个错误很难在本地重现。](http://api.rubyonrails.org/classes/ActiveRecord/Validations/ClassMethods.html#method-i-validates_uniqueness_of)**

处理这个错误需要一些上下文。如果错误是由竞争条件引起的，那可能是因为用户错误地提交了两次表单。我们可以尝试用一点 JavaScript 来缓解这个问题，在第一次点击后禁用提交按钮。类似这样的事情是一个开始:

```
const forms = document.querySelectorAll('form');
Array.from(forms).forEach((form) => {
  form.addEventListener('submit', (event) => {
    const buttons = form.querySelectorAll('button, input[type=submit]')
    Array.from(buttons).forEach((button) => {
      button.setAttribute('disabled', 'disabled');
    });
  });
}); 
```

**[这个关于 Coderwall 的提示使用 ActiveRecord 的`first_or_create!`和一个 rescue，并在错误出现时重试](https://coderwall.com/p/e8zu7q/using-retry-to-rescue-activerecord-recordnotunique)** 是一个巧妙的解决方法。您应该继续用您的错误监控解决方案记录错误，以便保持它的可见性。

```
def self.set_flag( user_id, flag )
  # Making sure we only retry 2 times
  tries ||= 2
  flag = UserResourceFlag.where( :user_id => user_id , :flag => flag).first_or_create!
rescue ActiveRecord::RecordNotUnique => e
  Rollbar.error(e)
  retry unless (tries -= 1).zero?
end 
```

看起来这可能是一个边缘案例，但它在这十大案例中排名第五，所以从用户体验的角度来看，它绝对值得考虑。

## 6。NoMethodError:nil:nil class 的未定义方法“id”

`NoMethodError`再次出现，不过这次带有不同的解释信息。这种错误通常是在具有关系的对象的创建动作附近发生的。愉快的途径——成功地创建对象——通常是可行的，但是当验证失败时会出现这个错误。让我们看一个例子。

这是一个带有动作的控制器，用于为课程创建应用程序。

```
class CourseApplicationsController < ApplicationController
  def new
    @course_application = CourseApplication.new
    @course = Course.find(params[:course_id])
  end
  def create
    @course_application = CourseApplication.new(course_application_params)
    if @course_application.save
      redirect_to @course_application, notice: 'Application submitted'
    else
      render :new
    end
  end
  private
  def course_application_params
    params.require(:course_application).permit(:name, :email, :course_id)
  end
end 
```

新模板中的表单看起来有点像这样:

```
<%= form_for [@course, @course_application] do |ca| %>
  <%# rest of the form %>
<% end %> 
```

这里的问题是当你从`create`动作调用`render :new`时，`@course`实例变量没有被设置。你需要确保`new`模板需要的所有对象也在`create`动作中初始化。为了修复这个错误，我们将更新`create`动作，如下所示:

```
 def create
    @course_application = CourseApplication.new(course_application_params)
    if @course_application.save
      redirect_to @course_application, notice: 'Application submitted'
    else
      @course = Course.find(params[:course_id])
      render :new
    end
  end 
```

如果你有兴趣了解更多关于**[Rails 中`nil`的问题以及如何避免它们](https://blog.ragnarson.com/2015/05/06/problems-with-nil.html)** 的信息，可以看看这篇文章。

# 7。ActionController::参数缺失

这个错误是 Rails **[强参数](http://edgeguides.rubyonrails.org/action_controller_overview.html#strong-parameters)** 实现的一部分。尽管它没有表现为 500 错误——它被`ActionController::Base`挽救并作为 400 错误请求返回。

完整的错误可能如下所示:

```
ActionController::ParameterMissing: param is missing or the value is empty: user 
```

这将伴随着一个可能看起来有点像这样的控制器:

```
class UsersController < ApplicationController
  def create
    @user = User.new(user_params)
    if @user.save
      redirect_to @user
    else
      render :new
    end
  end
  private
  def user_params
    params.require(:user).permit(:name, :email)
  end
end 
```

`params.require(:user)`表示如果`user_params`被调用，而`params`没有`:user`键或者`params[:user]`为空，`ActionController::ParameterMissing`将被引发。

如果您正在构建一个通过 web 前端使用的应用程序，并且您已经构建了一个表单来将`user`参数正确地发布到这个动作，那么缺少`user`参数可能意味着有人在搞乱您的应用程序。如果是这样的话，400 错误请求响应可能是最好的响应，因为您不需要迎合潜在的恶意用户。

如果您的应用程序正在提供一个 API，那么 400 Bad Request 也是对一个缺失参数的适当响应。

# 8。ActionView::Template::错误:未定义的局部变量或方法

这是我们在前 10 名中唯一的错误，这是一个好迹象。视图渲染模板的工作量越少越好。更少的工作导致更少的错误。但是，我们仍然会遇到这个错误，在这个错误中，您期望存在的变量或方法根本不存在。

这种情况在分部中最常见，可能是因为在一个页面上可以用许多不同的方式包含局部变量。如果您有一个名为`_post.html.erb`的分部，它包含一个 blog post 模板和一个设置在控制器中的实例变量`@post`，那么您可以像这样呈现这个分部:

```
<%= render @post %> 
```

或者

```
<%= render 'post', post: @post %> 
```

或者

```
<%= render partial: 'post', locals: { post: @post } %> 
```

Rails 喜欢给我们提供大量的选项，但是第二个和第三个选项可能会让人感到困惑。尝试渲染部分 like:

```
<%= render 'post', locals: { post: @post } %> 
```

或者

```
<%= render partial: 'post', post: @post %> 
```

会给你留下一个未定义的局部变量或方法。为了避免这种情况，请保持一致，并始终使用显式分部语法来呈现分部，在局部变量散列中表达局部变量:

```
<%= render partial: 'post', locals: { post: @post } %> 
```

还有一个地方，你可以在局部变量中出错。如果你只是偶尔将一个变量传递给一个分部，那么在一个分部代码中测试这个变量与普通的 Ruby 代码是不同的。例如，如果您更新上面的 post partial 以获取一个告诉您是否在该部分中显示标题图像的局部变量，那么您应该像这样呈现该部分:

```
<%= render partial: 'post', locals: { post: @post, show_header_image: true } %> 
```

那么分部本身可能是这样的:

```
<h1><%= @post.title %></h1>
<%= image_tag(@post.header_image) if show_header_image %>
<!-- and so on --> 
```

当您传递`show_header_image`局部变量时，这将很好地工作，但是当您调用
时

```
<%= render partial: 'post', locals: { post: @post } %> 
```

它将因未定义的局部变量而失败。要测试局部变量在分部中是否存在，您应该在使用它之前检查它是否已定义。

```
<%= image_tag(@post.header_image) if defined?(show_header_image) && show_header_image %> 
```

更好的是，我们可以使用一个名为`local_assigns`的散列来代替。

```
<%= image_tag(@post.header_image) if local_assigns[:show_header_image] %> 
```

对于不是布尔值的变量，我们可以使用其他散列方法，比如`fetch`来优雅地处理这个问题。以`show_header_image`为例，这个场景也是可行的:

```
<%= image_tag(@post.header_image) if local_assigns.fetch(:show_header_image, false) %> 
```

总的来说，当你把变量传递给部分变量时要小心！

## 9。ActionController::未知格式

像`ActionController::InvalidAuthenticityToken`一样，这个错误可能是由粗心或恶意用户而不是您的应用程序造成的。如果您构建了一个应用程序，其中的操作用 HTML 模板响应，并且有人请求页面的 JSON 版本，您会在日志中发现这个错误，看起来有点像这样:

```
ActionController::UnknownFormat (BlogPostsController#index is missing a template for this request format and variant.
request.formats: ["application/json"]
request.variant: []): 
```

用户将收到 406 不可接受的响应。在这种情况下，他们会看到此错误，因为您没有为此响应定义模板。这是一个合理的回应，因为如果您不想返回 JSON，他们的请求是不可接受的。

但是，您可能已经在同一个控制器中构建了 Rails 应用程序来响应常规的 HTML 请求和更多类似 API 的 JSON 请求。一旦你开始这样做，你就定义了你*想要*响应的格式，任何超出这个范围的格式也会导致一个`ActionController::UnknownFormat`，返回一个 406 状态。假设您有一个类似于
的博客文章索引

```
class BlogPostsController < ApplicationController
  def index
    respond_to do |format|
      format.html { render :index }
    end
  end
end 
```

请求 JSON 将导致 406 响应，您的日志将显示这个不太明显的错误:

```
ActionController::UnknownFormat (ActionController::UnknownFormat): 
```

这一次的错误并不是抱怨缺少模板——这是一个故意的错误，因为您已经定义了唯一要响应的格式是 HTML。如果这是无意的呢？

在响应中错过您想要支持的格式是很常见的。考虑一个动作，您希望在创建博客文章时响应 HTML 和 JSON 请求，以便您的页面可以支持 Ajax 请求。可能是这样的:

```
class BlogPostsController < ApplicationController
  def create
    @blog_post = BlogPost.new(blog_post_params)
    respond_to do |format|
      if @blog_post.save
        format.html { redirect blog_post_path(@blog_post) }
        format.json { render json: @blog_post.to_json }
      else
        render :new
      end
    end
  end
end 
```

这里的错误是在博客文章验证失败并且没有保存的情况下出现的。在`respond_to`块内，需要调用格式块范围内的`render`。为了避免失败，重写这段代码如下:

```
class BlogPostsController < ApplicationController
  def create
    @blog_post = BlogPost.new(blog_post_params)
    respond_to do |format|
      if @blog_post.save
        format.html { redirect blog_post_path(@blog_post) }
        format.json { render json: @blog_post.to_json }
      else
        format.html { render :new }
        format.json { render json: @blog_post.errors.to_json }
      end
    end
  end
end 
```

现在所有的格式都包括在内了，不会再有意外的`ActionController::UnknownFormat`异常了。

## 10。标准错误:发生了一个错误，此迁移和所有后续迁移都已取消

这最后一项让我有点失望。 **[`StandardError`](https://ruby-doc.org/core-2.5.0/StandardError.html)** 是所有其他错误都应该继承的基本错误类，因此在这里使用它会使错误看起来非常普通，而实际上它是在数据库迁移期间发生的错误。我更愿意把这个错误看作是`ActiveRecord::MigrationError`的后代。但是我跑题了…

导致迁移失败的原因有很多。例如，您的迁移可能与您的实际生产数据库不同步。在这种情况下，你将不得不四处挖掘，找出发生了什么，并解决它。

不过，这里应该包括一件事:数据迁移。

如果您需要为一个表中的所有对象添加或计算一些数据，您可能会认为数据迁移是一个好主意。举个例子，如果您想在一个用户模型中添加一个全名字段，包括他们的名字和姓氏(不太可能改变，但是对于一个简单的例子来说已经足够了)，您可以像这样编写一个迁移:

```
class AddFullNameToUser < ActiveRecord::Migration
  def up
    add_column :users, :full_name, :string
    User.find_each do |user|
      user.full_name = "#{user.first_name}  #{user.last_name}"
      user.save!
    end
  end
  def down
    remove_column :users, :full_name
  end
end 
```

这个场景有很多问题。如果 y x xour 集合中有数据损坏的用户，`user.save!`命令将抛出错误并取消迁移。其次，在生产环境中，您可能有很多用户，这意味着数据库将需要很长时间来迁移，可能会使您的应用程序一直处于离线状态。最后，随着应用程序的不断变化，您可能会删除或重命名`User`模型，这将导致迁移失败。一些建议建议您在迁移中定义一个`User`模型来避免这种情况。为了更加安全，Elle Meredith 建议我们 **[完全避免 ActiveRecord migrations](https://robots.thoughtbot.com/data-migrations-in-rails)** 中的数据迁移，而是构建临时数据迁移任务。

在迁移之外更改数据可以确保您做一些事情。最重要的是，它让你考虑如果数据不存在，你的模型如何工作。在我们的全名示例中，您可能会为`full_name`属性定义一个访问器，如果数据可用，该访问器就会响应。如果不是，那么通过连接组成部分来构建全名。

```
class User < ApplicationRecord
  def full_name
    @full_name || "#{first_name}  #{last_name}"
  end
end 
```

将数据迁移作为单独的任务运行也意味着部署不再依赖于生产数据库中的数据变化。Elle 的文章有更多的理由说明为什么这样做更好，还包括写任务的最佳实践。

# 结论

最常见的 Rails 错误可能来自应用程序中的任何地方。在本文中，我们已经看到了模型、视图和控制器中常见的错误。有些错误没什么好担心的，只是在保护你的应用程序。其他的应该尽快抓起来，消灭掉。

然而，跟踪这些错误发生的频率是很好的。这有助于更好地了解影响您的用户或应用程序安全性的问题，以便您可以快速修复它们。否则，这些错误消息会显示给用户，但是工程和产品管理团队不会知道，直到用户向支持部门投诉。

我们希望你能学到一些新的东西，并为将来避免这些错误做好准备。然而，即使有了最佳实践，生产中也会出现意外的错误。了解影响用户的错误，并拥有快速解决这些错误的好工具是非常重要的。

Rollbar 让您可以看到生产 Ruby 错误，这提供了更多的上下文来快速解决它们，包括表单验证错误、人员跟踪、启动错误等等。查看 **[Rollbar 的完整特性列表](https://rollbar.com/error-tracking/ruby/)** 和 **[Ruby SDK 文档](https://docs.rollbar.com/docs/ruby)** 。

* * *

*[来自 1000+ Ruby on Rails 项目的 10 大错误(以及如何避免)](https://rollbar.com/blog/top-10-ruby-on-rails-errors/)最初发表于 2018 年 4 月 18 日 [Rollbar 博客](https://rollbar.com/blog/)。*