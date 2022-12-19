# 停止将逻辑与您的 HTTP 层耦合！

> 原文：<https://dev.to/hecrj/stop-coupling-logic-with-your-http-layer-163>

作为一名开发人员，有一个问题我倾向于在许多代码库中注意到:**逻辑与特定的通信层**耦合。

这个问题在大多数 web 后端的**控制器**中尤其常见，在这些后端中，应用程序逻辑通常与对 HTTP 层的访问交织在一起。这并不奇怪。很多一些最知名框架的文档例子( [Rails](https://guides.rubyonrails.org/action_controller_overview.html#parameters) 、 [Django](https://docs.djangoproject.com/en/2.1/intro/tutorial03/) 、 [Flask](http://flask.pocoo.org/docs/1.0/quickstart/#a-minimal-application) 、 [Phoenix](https://hexdocs.pm/phoenix/overview.html) 等)。)有这个问题。

但是，当我说“逻辑与对 HTTP 层的访问交织在一起”时，我指的是什么呢？让我们看看如何在 Ruby on Rails 中实现一个简单的登录端点:

```
class UsersController < ApplicationController
  def login
    user = User.find_by(email: params[:email])

    unless user.password_match?(params[:password])
      return error_response(:invalid_credentials)
    end

    json_response(auth_token: Tokens.auth(user))
  end
end 
```

简单吧？但是...我们如何使用这个`UsersController`类？我们如何测试我们的`login`逻辑？谁居住在`params`？它需要哪些数据？**这个代码的公约一点都不清楚！**我们有一个类，它的方法不带参数，使用内部(隐藏)状态来完成工作。

当代码不容易孤立使用时，理解、测试和调试就会变得更加困难。我们要尽量避免。

上面的代码将函数的逻辑绑定到某个特定的通信层(在本例中是 HTTP)。使用`params`和`json_response`迫使我们在每次想要使用这个`login`方法的时候意识到这一层。

我们可以分离出端点的逻辑:

```
ApiLogin = ->(email:, password:) do
  user = User.find_by(email: email)
  return { data: nil, errors: [:invalid_credentials] } unless user.password_match?(password)

  { data: { auth_token: Tokens.auth(user) }, errors: [] }
end 
```

这现在是一个与通信无关的函数。它的输入(`email`和`password`)和输出(带有`data`和`errors`的简单散列)是显式的。因此，我们可以很容易地测试它:

```
ApiLogin.call(email: "some@email.com", password: "12345678")
# => { data: { auth_token: "..." }, errors: [] } 
```

现在，我们只需要将这个功能连接到我们的通信层。我们可以用一个`ApplicationController`，像之前一样:

```
class UsersController < ApplicationController
  def login
    response = ApiLogin.call(email: params[:email], params[:password])

    if response.fetch(:errors).empty?
      json_response(response.fetch(:data))
    else
      error_response(response.fetch(:errors))
    end
  end
end 
```

这里的关键点是，这种连接不包含任何需要具体测试的相关逻辑，因为它很容易被抽象出来！大多数情况下，将一个函数连接到 HTTP 层包含相同的步骤:

1.  从请求中获取输入数据。
2.  用获得的数据调用一个函数。
3.  将函数的输出转换成 HTTP 响应。

考虑到这一点，我们可以编写一个 Ruby DSL 来生成定制的控制器，就像这样:

```
UsersController = Http.controller do
  action :login, ApiLogin, email: param(:email), password: param(:password)
end 
```

并根据需要扩展它。例如，我们可以实现`header`和`json`方法，类似于`param`。

有许多方法可以通过 HTTP 公开一个函数。我个人喜欢构建一个转换管道，其中输入是 HTTP 请求，输出是 HTTP 响应，特别是在具有强大类型系统的函数式语言中。这个管道是用灵活的 API 一步一步安全准确地定义的。我将很快写下我是如何在 Haskell 中做到这一点的！

总之，一旦我们将逻辑从交流中分离出来，我们就会获得一些有趣的好处:

*   我们的应用程序逻辑变得更容易使用、理解和测试。
*   可以使用单一责任抽象来集中、更改和测试通信细节。
*   我们可以实现一个新的通信层(CLI，一个不同的 HTTP 层)来公开相同的逻辑。

* * *

请在评论中告诉我你的想法！为什么你认为这是一个如此普遍的问题？你已经在做类似的事情了吗？