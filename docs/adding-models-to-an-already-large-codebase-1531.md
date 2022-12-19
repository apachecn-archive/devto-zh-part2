# 在不使用 scaffold 的情况下向已经很大的代码库中添加模型。

> 原文：<https://dev.to/husteadrobert/adding-models-to-an-already-large-codebase-1531>

TL；遵循这个顺序:

1.  创建一个 ERD
2.  创建迁移
3.  创建模型
4.  管理模型的权限
5.  指定路线
6.  创建控制器
7.  创建视图

如果你是第一次加入一个开发团队，在添加功能或新模型时，想知道从哪里开始可能会令人生畏。有数百个代码文件和如此多的活动部件，你可能不知道它们是做什么的。虽然有很多关于 TDD(测试驱动开发)和在编写任何代码之前编写规范的讨论，但我发现在深入测试之前创建一个我喜欢的基本原型或框架是有帮助的。好的一面是，向已经很大的代码库添加模型很容易！

有许多发展的方法，但是我发现遵循一系列逻辑步骤对我有帮助。因此，这是我向已经很大的代码库添加功能的一般思维过程和步骤。我不会深入讨论语法的主要细节(比如具体如何设置迁移)，因为您可以很容易地在 google 上搜索这些信息，并且我假设读者对 Rails 有基本的了解。而且，每个代码库和团队都不一样。相反，当你退后一步思考“下一步是什么？”时，我会专注于创建一个总体指南来影响你的决策过程这篇文章的目标读者是初级开发人员，他们了解 Rails 的基础知识，但可能还没有足够的经验来完全适应，并让他们不必担心设置问题并直接进入“真正的编码”。

应该注意的是，大部分工作都可以在命令行中使用 Rails `generate scaffold`命令来完成。然而，如果您不能 100%确定 Rails 到底会创建什么和做什么，或者如果您还不习惯自己创建基本的搭建，我建议您遵循以下步骤。

## 1。创建 ERD(实体关系图)

在你写任何代码之前，你需要计划好你到底要创建什么。您应该花一些时间来弄清楚您将需要什么类型的模型，它将具有什么属性，以及它将如何与您的代码库中的其他模型相关联。
举例 ERD:
[![Imgur](../Images/24d2e8efee7925a456b7ad6f4b1be988.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0qa2XfNx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/RVfc1Tk.png) 建造于 [www.draw.io](http://www.draw.io) 
打造一个坚固的 ERD 可能是创作过程中最重要的一步，所以慢慢来！俗话说，“量两次，切一次。”

## 2。创建迁移！

创建新模型的第一步是准备数据库来存储它的信息。如果你进入一个代码库，并且已经让它在本地工作，那么数据库(通常在你的`config/database.yml`文件中定义)可能已经被创建了。因此，您只需要创建一个迁移。

迁移的一般语法:`$ rails generate migration name_here`

这将为您创建一个迁移文件，使用您在`name_here`中使用的名称并添加一个时间戳。打开该文件，并创建包含所需字段的表。请注意，表的名称将是复数。例如，如果您想要创建一个`Article`模型，数据库中的表的名称将是`articles`。
参见:[活动记录迁移— Ruby on Rails 指南](https://guides.rubyonrails.org/v5.2/active_record_migrations.html)

当您的迁移文件完成后，运行:`$: rails db:migrate`
如果出于某种原因您注意到一个错误，或者想要撤销更改，您可以使用:`$: rails db:rollback`回滚

迁移成功运行后，您的数据库中就有了一个表来存储新模型的数据！

## 3。创建模型

接下来，我们需要为实际的模型编写 Rails 代码。根据您的项目，您可能有不同的模型域，但是您通常可以在`app/models`目录中找到您需要的所有模型。一些需要注意的事情:文件名应该以小写字母开头，并且是单数。文件中的类名也应该是大写和单数。因此，在 models 目录中用您的模型名创建一个新文件。

让我们看一个例子。假设您刚刚通过迁移创建了一个新表`articles`。
在你的`app/models/article.rb`文件中(注意小写和单数)

```
class Article < ApplicationRecord

end 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！你现在有了一个模型。进入 rails 控制台(`$ rails c`)检查它是否按预期工作。现在还不要担心验证或关系。让我们先把所有的东西都弄好，然后我们再考虑如何把它们连接起来。

## 4。管理使用模型的权限

谁将能够使用您的模型？我的意思是，网站的每个用户都能够阅读模型包含的信息吗？他们能创造新的吗？或者，您是否将创建新模型的权利仅保留给管理员？如果你进入了一个大型代码库，很可能已经有了某种用户认证服务(比如 device([GitHub-plataformatec/device:带有 Warden 的灵活的 Rails 认证解决方案。](https://github.com/plataformatec/devise)))和资源权限管理服务(像 CanCan([GitHub-plataformatec/device:带 Warden 的 Rails 灵活认证解决方案。](https://github.com/plataformatec/devise)))

无论您使用什么库，您都应该研究如何向您的模型添加(或限制)权限。在康康的情况下，你会在你的`app/models`目录中有一个能力文件。在该能力文件中，您可以基于用户类型添加权限。

例如，你的能力文件可能是这样的:

```
class UserAbility
  include CanCan::Ability

  def initialize(user)
    can :read, Instruction
    can :manage, Gadget if user.admin?
    can [:show, :map], Restaurant
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

不一定要理解这里发生的一切，但是你应该尽可能地了解你所使用的库是如何工作的。在 CanCan 的例子中，在一个 initialize 方法中，你指定用户可以在一个模型上执行什么动作(在控制器中)。`:read`是`:show, :index`的简写，而`:manage`允许所有正常的 CRUD 动作。如果您需要执行其他操作，也可以使用符号数组来添加它们。

如果你不确定，暂时你可以添加你的模型，允许用户`:manage`并在以后修改它，但是你应该考虑谁会使用这个模型，他们会用它做什么。

现在必须完成这一步，因为如果您不授予访问模型的权限，您将无法在以后测试您的控制器或视图。每个使用资源的请求都将被应用程序控制器停止，您将得到一个错误。

## 5。创建您的路线

授予访问模型的权限后，您需要定义用户到达该资源的路径。很容易忘记设置路线，所以我发现在创建控制器和视图之前设置路线是这个过程中合乎逻辑的下一步。

在您的`config/routes.rb`文件中，您需要指定将访问您的控制器的路径(我们将在接下来进行)。如果您的应用程序使用许多名称空间，您应该注意在正确的名称空间中指定路由，或者在适当的相关资源中嵌套路由。此外，您需要指定任何超出正常 CRUD 操作范围的操作。如果您在另一个资源中嵌套了路由，请记下它，因为下一步将需要该信息。

## 6。创建控制器

最后，我们可以为模型创建控制器。您可能已经在担心所有您知道需要添加的功能、数据解析或逻辑，但是现在还不要担心这些！我们现在应该担心的是，当我们放入一个 URL 时，我们的应用程序会像预期的那样做出反应，并将我们的请求路由到我们的控制器。

首先，我们必须创建控制器文件，这可能有点棘手。如果您的控制器没有嵌套在名称空间或其他资源中，您可以在您的`app/controllers`目录中创建一个新文件。但是，如果您的控制器嵌套在另一个资源中，您将需要为您的新控制器创建目录，或者确保在正确的目录中创建您的控制器(如果它已经存在)。

在正确的目录中，创建一个新文件作为控制器。这个文件需要遵循模型+下划线+控制器的基本命名结构。例如，`articles_controller.rb`

在您的控制器中，定义控制器将具有的所有动作。你不需要给它们添加任何逻辑；给它们下定义吧。此示例针对非嵌套资源。注意型号名称是大写的复数形式，没有下划线:

```
class ArticlesController < ApplicationController
  def show; end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果你的控制器是嵌套的，你会得到类似这样的东西:

```
class AdminDomain::ArticlesController < AdminDomain::ApplicationController
  def show; end
end 
```

Enter fullscreen mode Exit fullscreen mode

有几件事需要注意:记住`AdminDomain::ArticlesController`不是继承自一个叫做 AdminDomain 的东西。`::`是一个范围解析操作符，用来告诉编译器这个 ArticlesController 位于 AdminDomain 中，而不是它继承了任何东西。继承显示在`<`之后，表示`AdminDomain::ArticlesController`继承自`AdminDomain::ApplicationController`，但是您也可以很容易地从`WriterDomain::ApplicationController`或`ApplicationController`继承它(尽管这可能会导致意外的行为)。)此外，从技术上来说，定义空白方法是不必要的，因为 Rails 已经足够友好，可以在没有显式方法声明的情况下执行默认操作。但是为了清楚起见(也为了让你仔细检查你将拥有什么方法)，我建议声明它们。

## 7。创建视图

控制器中动作的默认行为是呈现共享相同名称的视图。因此，在控制器中声明了我们的空白动作后，我们应该开始创建我们的视图。与控制器一样，您需要记住您的资源是否是嵌套的。如果没有，您可以简单地在您的`app/views`目录中创建一个文件夹，它是您的型号名称的复数。例如，假设 ArticleController 没有嵌套在另一个资源中，您可以为所有的文章视图创建一个`app/views/articles`目录。

在您的新目录中，为您的视图创建一个空白文件，并给它一个反映控制器中动作的文件名。Rails 使用现成的 ERB，但是要检查您的团队使用什么样的模板语言，并创建一个以该扩展名结尾的文件。
为 ERB，`app/views/articles/show.html.erb`
为 HAML，`app/views/articles/show.html.haml`
等。

现在，只需在文件中添加一行来确保它正常工作。类似于，

```
<h1>Hello World!</h1> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该能够输入主题控制器的 show 操作的 URL(如果您不确定如何访问它，请检查您的路线)，并收到一个大的“Hello World！”回应。

## 开始编码！

你们都完了！也就是说，你才刚刚开始。但是您已经完成了向代码库添加模型、控制器、路径和权限所需的所有基本工作。现在，您可以开始担心更实际的问题，例如您希望页面看起来如何，或者您希望从数据库中查询什么数据，或者您希望模型验证什么数据，您将需要创建的测试，或者创建新功能带来的任何其他问题。

祝你好运，编码快乐！