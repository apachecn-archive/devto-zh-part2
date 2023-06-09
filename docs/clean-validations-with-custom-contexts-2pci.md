# 使用自定义上下文进行干净验证

> 原文：<https://dev.to/spidergears/clean-validations-with-custom-contexts-2pci>

活动记录验证是 Rails 中一个众所周知且广泛使用的功能。

```
class User < ApplicationRecord
  validates :name, presence: { message: "must be given please" }
end 
```

Enter fullscreen mode Exit fullscreen mode

这将在创建新记录或更新现有记录时对`save`进行验证。

`on`选项允许控制何时运行验证，通常使用值`create`或`update`T3

```
class User < ApplicationRecord
  belongs_to :club, optional: true 
  validates :name, presence: { message: "must be given please" }, on: :create
  validates :club, presence: { message: "must be given please" }, on: :update  
end 
```

Enter fullscreen mode Exit fullscreen mode

这允许在不将用户与俱乐部相关联的情况下创建用户，但会在后续更新中强制显示俱乐部。这种模式通常用于允许用户用最少的表单字段注册，然后强迫他们在以后的访问中用更多的信息更新他们的配置文件。

`on`选项的值不限于`create`和`update`，我们可以有自己的自定义上下文。像在多步表单中一样，我们可以对每个步骤进行验证。`on`选项让这变得非常简单

```
class User < ApplicationRecord
  validate :basic_info, on: :basic_info
  validate :education_details, on: :education_details
  validate :professional_info, on: :professional_info

  private
  def basic_info
    # Validation for basic info, first_name, last_name, email
  end

  def education_details
    # Validation for education_details
  end

  def professional_info
    # Validation for professional_info
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在控制器
中

```
class UsersController < ApplicationController
  ...

  def update_basic_info
    @user.assign_attributes(basic_info_params)
    @user.save(:basic_info)
  end

  def update_education_details
    @user.assign_attributes(education_details_params)
    @user.save(:education_details)
  end

  def update_professional_info
    @user.assign_attributes(professional_info_params)
    @user.save(:professional_info)
  end

  private
  def basic_info_params
    # strong params
  end

  def education_details_params
    # strong params
  end

  def professional_info_params
    # strong params
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

随着 Rails 5 增加了对多个上下文的支持，我们可以一起使用多个上下文

```
@user.save(:basic_info, :professional_info) 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很不错，让我们更进一步，用`update_attributes`来做这件事。在 Rails 的当前实现中，
`update_attributes`不支持验证上下文。我们可以通过定义自己的自定义方法
来解决这个问题

```
class ApplicationRecord < ActiveRecord::Base
  self.abstract_class = true

  def update_attibutes_with_context(attributes, *contexts)
    with_transaction_returning_status do
      assign_attributes(attributes)
      save(context: contexts)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在控制器
中

```
@user.update_attibutes_with_context({first_name: 'fname'}, :basic_info) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以使用`with_options`将多个验证分组到一个上下文
中

```
 with_options on: :member do |member_user|
    member_user.validates :club_name, presence: true
    member_user.validates :membership_id, presence: true
  end 
```

Enter fullscreen mode Exit fullscreen mode

这使得编写可读和可维护的代码变得非常容易，并很好地分离了关注点。

在这里阅读我上一篇关于活动记录验证的文章[https://dev . to/spider gears/rails-active-record-validation-messages-4d cf](https://dev.to/spidergears/rails-active-record-validation-messages-4dcf)