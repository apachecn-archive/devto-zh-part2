# 统一记录

> 原文：<https://dev.to/zygo-tecnologia/unifying-records-61e>

[![](img/695dd9fad294ceda852198113c09eca0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9OGHfWbm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AfqhsCA2OlUbqxayxP0IouQ.jpeg)

自从 2009 年我开始作为软件开发人员工作以来，我已经在不同的项目中工作过，有一个问题似乎是许多项目的共同问题:将两个记录统一为一个。

例如，有时用户可以创建一个使用他的 google 帐户登录的帐户，忘记它，并在下一次创建另一个使用 facebook 帐户登录的帐户。通常情况下，这个用户会出现在支持中，询问他的历史，他的活动和第一个帐户的其他东西在哪里，因为他忘记了谷歌登录，但没有忘记过去的使用和平台中的信息。

一个真实的例子发生在我身上，几年前我使用我的电子邮件在 Meliuz 平台上创建了一个帐户，选择了一个自定义密码并注册了我的 CPF(CPF 是识别巴西每个人的唯一号码)。我有一段时间没有使用这个网站，当我再次登录时，我使用我的 facebook 帐户登录。我开始使用这个帐户，在某个时候，我被要求在这个新帐户中输入我的 CPF，以便能够在系统中执行操作，但当我试图输入我的 CPF 时，我得到一个错误，告诉我我的 CPF 已经注册。我必须给他们的支持发送一条信息，可能他们必须统一我的两个账户，这样我才能继续使用 facebook 账户。

统一记录的问题在于，在一个大项目中，你有很多依赖项。例如，假设我们有一个项目/任务管理器平台和下面的结构。

<figure>[![](img/265370dfcf17d079944331575bf33e2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MFNtziNE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/581/1%2A8d93yTnDy-xH4eAxyfpH2Q.png) 

<figcaption>UML 类图任务管理器项目</figcaption>

</figure>

因此，在这种情况下，当我们必须统一一个用户时，我们也必须改变项目和任务。在这个小例子中，我们可以认为向三个模型添加方法并完成特性并不是什么大不了的事情。但是当你有一个更大的项目时，当你统一一个记录时，你将不得不处理许多必须改变的模型。

另一个场景是，在您构建了 unify 用户特性之后，有人在一个任务中添加了一个带有 user_id 的新模型，但是忘记了在 unify 用户特性中添加这个模型。下次运行 unify users 时，它会中断，因为您有一个新的模型，它不准备在您统一两个用户后进行更改并继续工作。

因此，当我在当前的工作中获得一项构建统一用户功能的任务时，我提出了完成这项任务的两个必要条件:

1 —它必须易于运行，只有一个命令、一个服务来处理它，代码必须简短易懂；

2 —使用 user_id 创建的每个新模型都必须易于添加到统一用户流中。

为了完成第一项，我们得出这样一个代码:

```
ApplicationRecord.descendants.each do |model\_class|
  next unless model\_class.column\_names.include?('user\_id')

  model\_class.unify\_users!(user\_to\_empty, user\_to\_keep)
end

user\_to\_empty.delete 
```

Enter fullscreen mode Exit fullscreen mode

使用 ApplicationRecord.descendants，我们得到了所有模型的列表，因为它们都继承自 ApplicationRecord。因此，我们迭代这个列表，首先检查每个模型是否有一个名为 user_id 的列。如果没有，就继续下一步，如果有，我们就给 unify_users 打电话！方法。在迭代所有模型之后，我们只需删除重复的用户。因此，我们对完成第一个目标，第一个必要条件感到满意，我们有了一个好的、简短的和容易理解的代码。

但是第二种呢？我们有一个问题，对于我们用 user _ idwe 创建的每个新模型，我们必须记住创建一个名为 unify_users 的方法！，如果我们忘记上面的代码就会崩溃。即使对于现有的带有用户的模型，我们也必须为每个模型创建许多 unify_users 方法。所以，我们还不完全满意，我们必须完成第二个目标。

因此，我们创建了一个关注点，并在团队中设置了一个规则，我们不能直接在模型中添加一个`belongs_to :user`，我们应该总是使用我们的新模块 UserBelongs :

```
module UserBelongs
  extend ActiveSupport::Concern

  included do
    belongs\_to :user, optional: true
  end

  # Add the unify\_users! method to the model class.
  module ClassMethods
    def unify\_users!(from, to)
      # rubocop:disable Rails/SkipsModelValidations
      where(user: from).update\_all(user\_id: to.id)
      # rubocop:enable Rails/SkipsModelValidations
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

所以，现在我们只有一个 unify_users 的实现！这对几乎所有型号来说都足够了。对于一些需要特定规则的模型，我们仍然需要在每个模型文件上实现 if，但是这是可以的，因为这是关于那个模型的特定规则。另一个观察是，我们在这个方法上使用了 update_all，这是故意的。Update all 是我们发现的在单个事务中更新大量记录并跳过验证的最快方法，这是这个特定情况所需要的，因为速度是这里的主要关注点，因为用户可能有大量记录，如果我们运行验证并为每个记录运行一个事务，则可能需要很长时间来完成这项任务。

这种方法最大优势在上个月就显现出来了，当时我们必须为另一个模型创建另一个统一的特性，有了这个已经构建好的特性，将相同的思想和概念应用于另一个模型统一就变得非常容易和快速。

因此，如果你对此有任何建议，请留下评论，我们很乐意讨论并找到更好的方法来实现这一点。

* * *