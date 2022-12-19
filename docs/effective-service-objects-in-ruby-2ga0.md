# Ruby 中的有效服务对象

> 原文：<https://dev.to/mikamai/effective-service-objects-in-ruby-2ga0>

作为一名 Java 开发人员，我最近转到了一家 Ruby on Rails 公司，当我发现直接在控制器内部使用模型是一种常见的做法时，我感到有些失落。

我一直遵循**领域驱动设计**的良好实践，并将我的业务逻辑封装在名为 **[服务对象](https://en.wikipedia.org/wiki/Service_layer_pattern)** 的特殊类中，所以在 Java 中(使用 [Spring](https://spring.io/guides/gs/serving-web-content/) )控制器看起来像这样:

```
@Controller
@RequestMapping("/api/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping
    public ResponseEntity<Iterable<User>> getAllUsers() {
        return ResponseEntity.ok(userService.getUsers());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

撇开冗长不谈，这是一个很好的、干净的、关注点分离的例子。检索用户列表的实际业务逻辑被委托给`UserService`实现，并且可以随时换出。

然而，在 Rails 中，我们会这样写这个控制器:

```
class Api::UserController < ApplicationController
    def index
        @users = User.all
        render json: @users
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

是的，这确实比 Java 例子更短，甚至更干净，但是它有一个主要的缺陷。是一个 ActiveRecord 模型，通过这样做，我们将控制器与持久层紧密耦合，打破了 DDD 的一个关键方面。此外，如果我们想在请求中添加授权检查，可能只返回基于当前用户角色的用户子组，我们将不得不重构控制器，并让它负责不属于表示逻辑的部分。通过使用服务对象，我们可以向它添加更多的逻辑，同时对世界其他地方是透明的。

# 让我们构建一个服务对象

在 Java 中这很简单。它是一个由 IoC 容器注入到其他类中的单例类(在我们的例子中是 Spring DI)。在 Ruby 中，尤其是 Rails，这是不一样的，因为我们不能在控制器构造函数中注入任何东西。然而，我们能做的是从另一种编程语言中获取灵感: [Elixir](http://elixir-lang.org) 。在函数式语言 Elixir 中，没有类也没有对象，只有函数和结构。函数被分组到模块中，没有副作用，这是确保代码不变性和稳定性的一个很好的特性。
因为 Ruby 也有模块，我们可以用它们来实现我们的服务对象，作为无状态的方法集合。

我们的用户服务可以是这样的:

```
module UserService
    class << self
        def all_users
            User.all
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后会这样用:

```
class Api::UserController < ApplicationController
    def index
        @users = UserService.all_users
        render json: @users
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

这听起来不像是明智之举，不是吗？我们只是将`User.all`调用移到了另一个类中。这是真，但是现在，随着我们的应用程序的增长，我们可以在不破坏其他代码或重构的情况下向它添加更多的逻辑，只要我们保持我们的 API 稳定。
在继续之前，我要做一个小小的改动。由于我们可能希望在每次调用时将一些数据注入到我们的服务中，所以我们将使用名为`ctx`的第一个参数来定义我们的方法，该参数将包含当前的执行上下文。像当前用户这样的东西将包含在那里。

```
module UserService
    class << self
        def all_users _ctx # we'll ignore it for now
            User.all
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
class Api::UserController < ApplicationController
    def index
        @users = UserService.all_users { current_user: current_user }
        render json: @users
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

# 应用业务逻辑

现在我们构建一个更复杂的案例，先用一个用户故事来描述。让我们想象我们正在构建一个 ToDo 应用程序(哇，多么具有革命性！).故事是这样的:

> 作为一名普通用户，我希望能够看到我下个月的所有待办事项。

RESTful HTTP 调用类似于:
`GET /api/todos?from=${today}&to=${today + 1 month}`

我们的控制器将:

```
class Api::TodoController < ApplicationController
    def index
        @ctx = { current_user: current_user }
        @todos = TodoService.all_todos_by_interval @ctx, permitted_params
        render json: @todos
    end

    private

    def permitted_params
        params.require(:todo).permit(:from, :to)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们的服务:

```
module TodoService
    class << self
        def all_todos_by_interval ctx, params
            Todos.where(user: ctx[:current_user]).by_interval params
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们仍然将繁重的数据库提升任务委托给模型(通过范围`by_interval`)，但是服务实际上只控制对当前用户的过滤。我们的控制器保持精简，我们的模型仅用于持久性访问，我们的业务逻辑不会泄漏到源代码的每个角落。耶！

# 服务组成

我们可以用来增强业务层的另一个非常有用的 OOP 模式是[复合模式](https://en.wikipedia.org/wiki/Composite_pattern)。有了它，我们可以将公共逻辑分离成专用的、不透明的服务，并从其他服务中调用它们。例如，我们可能希望在 todo 更新时向用户发送通知(例如，因为它已过期)。我们可以将通知逻辑放入另一个服务中，并从前一个服务中调用它。

```
module TodoService
    class << self
        def update_todo ctx, params
            updated_todo = Todos.find ctx[:todo_id]
            updated_todo.update! params # raise exception if unable to update
            notify_expiration ctx[:current_user], updated_todo if todo.expired?
        end

        private

        def notify_expiration user, todo # put in a private method for convenience
            NotificationService.notify_of_expiration { current_user: user }, todo
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

# 用于重复性任务的命令

由于[四人组](https://en.wikipedia.org/wiki/Design_Patterns)给了我们大量优秀的 OOP 模式，我将从他们那里借用最后一个概念，并大大增加我们的代码分离。你看，我们的服务可以充当协调者而不是执行者，将实际工作委托给其他类，只关心调用正确的类。那些较小的“工人风格”的类可以作为命令来实现。通过使用更小的执行单元(单个命令而不是复杂的服务)和更多地分离关注点，这具有增强组合的最大优势。现在，服务充当动作协调器，协调如何执行逻辑，而实际执行在简单、可测试和可重用的组件中运行。

> 附注:我将使用 gem [simple_command](https://github.com/nebulab/simple_command) 来实现命令模式，但是您可以随意使用任何您想要的东西

让我们重构更新逻辑以使用命令模式:

```
class UpdateTodo
    prepend SimpleCommand

    def initialize todo_id, params
        @todo_id = todo_id
        @params = params
    end

    def call
        todo = Todos.find @todo_id

        # gather errors instead of throwing exception
        errors.add_multiple_errors todo.errors unless todo.update @params
        todo
    end
end

module TodoService
    class << self
        def update_todo ctx, params
            cmd = UpdateTodo.call ctx[:todo_id], params

            if cmd.success?
                todo = cmd.result
                notify_expiration ctx[:current_user], todo if todo.expired?
            end

            # let's return the command result so that the controller can
            # access the errors if any
            cmd
        end

        private

        def notify_expiration user, todo # put in a private method for convenience
            NotificationService.notify_of_expiration { current_user: user }, todo if todo.expired?
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

太美了。现在每个类都有一个任务(控制器接收请求并返回响应，命令执行小任务，服务将所有东西连接在一起)，我们的业务逻辑很容易测试，不需要任何支持基础设施(只是模仿所有东西)。模仿很好。)而且我们有更小更可重用的方法。我们只是有了一个稍微大一点的代码库，但与 Java 项目相比，这仍然是微不足道的，从长远来看，这是值得努力的。此外，我们的服务不再耦合到任何 Rails(或其他框架)特定的类。例如，如果我们想要更改持久性库，或者将一个业务领域迁移到外部微服务，我们只需重构相关命令，而不必触及我们的服务。

你在 Ruby 项目中使用服务对象了吗？你是如何实现这个模式的，你解决了哪些我的方法没有解决的挑战？