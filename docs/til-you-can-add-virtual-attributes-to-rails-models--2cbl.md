# 直到您可以向 Rails 模型添加虚拟属性

> 原文：<https://dev.to/andy/til-you-can-add-virtual-attributes-to-rails-models--2cbl>

*免责声明:我昨天才知道。哇哦。*

我正在构建一个电子邮件发送功能，基本思想是给用户分配一个有截止日期的角色，并允许发送电子邮件通知用户。最终结果是这样的:

[![Example image of role adding form](img/c751ca04b8a98f2fc49dfae0781968ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y7gCJPsB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q0rnn62h844pbshymnjc.png)

构建表单非常简单明了，尤其是因为大部分工作已经用[administration gem](https://github.com/thoughtbot/administrate)为我完成了。甚至有一个很好的 wiki 告诉我如何制作一个定制的表单域，它甚至包括一个命令行生成器！太好了！这太简单了！像大多数问题一样，这个问题没有我想象的那么简单。

生成器创建了一个局部视图，经过一点配置后，我所要做的就是再添加几个字段:

<figure>

```
<div class="field-unit__label">
  <%= f.label field.attribute %> #=> Give superpowers?
</div>
<div class="field-unit__field" style="width: 2em;">
  <%= f.check_box field.attribute %> #=> User.add_role superpowered
</div>
<%= f.date_field "superpower_expiration", 
    value: (Time.now + 1.year).strftime("%Y-%m-%d"), style: "width: 25%;" %>
  <div style="margin-left: 1em;">
    <%= f.label "Send email telling them they have it?" %>
  </div>
  <div style="margin-left: 1em;">
    <%= check_box_tag "superpower_email" %>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>View partial or partial view? You decide.</figcaption>

</figure>

好吧，这就是我要做的，对吗？表单提交后，一切正常，然后 bada bing bada boom *将代码投入生产*。嗯，一旦代码进入生产，我意识到它不起作用-实际上，Jess 意识到它不起作用，让我知道我犯了错误。吸取教训。总是检查你的代码，看看...

[![mipsytipsy profile image](img/b43ecce6a85296fb8da1758ff6810eaa.png) ](/mipsytipsy) [ Charity Majors ](/mipsytipsy) • [<time datetime="2018-02-21T19:21:58Z"> Feb 21 '18 </time>](https://dev.to/mipsytipsy/comment/2bmd) 

部署后会发生什么。

仅仅编译和通过测试而不被告知是不够的。你应该有肌肉记忆去检查它-你期望发生的事情，真的发生了吗？还有什么变化吗？

所以，回到代码。下面是我认为有问题的代码:

```
# Controller
  class UsersController < ApplicationController
    def update
      user = User.find(params[:id])
      UserRoleService.new(user).check_for_roles(params[:user])
      if user.errors.messages.blank? && user.update(user_params)
        # happy path
      else
        # sad path
      end
    end

   private

   def strong_params
     accessible = [user, model, attributes]
     params.require(:user).permit(accessible)
   end
  end

# user_role_service.rb, the service that handles new roles
  class UserRoleService
    def check_for_roles
      # some other logic
      NotifyMailer.delay.superpowers_awarded_email(@user) if params[:superpower_email] == "1"
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

啊，我忘了`params[:superpower_email]`是一个普通参数，是不允许的。我试着将`push`放入`accessible`阵中，但是`:superpower_email param`仍然无处可寻。嗯，因为它不起作用，我不得不改变复选框字段:

```
 <div style="margin-left: 1em;">
    <%= f.check_box "superpower_email" %>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

但是那扔了一个`NoMethodError, undefined method for User`。嗯，`superpower_email`不应该是模型中的一个实际的列，如果我必须创建一个迁移并生成那个列，那将是荒谬的。一定有更好的方法。我请求帮助，本说:

<figure>

[![Ben's response: virtual attributes](img/22e19b3a1ec17e5ec862e61f6a3ffef0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GInjuJ3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/umxi04o641o2u52wkxdb.png)

<figcaption>I replied with :explodingheademoji:, and yeah, it's a real emoji.</figcaption>

</figure>

既然你已经读了这么多这个故事，问题终于出现了:什么是虚拟属性？嗯，在谷歌上搜索了一下，我发现了。

Rails 模型是从`ApplicationRecord`继承的类，就像类一样，我们可以给它们添加属性。几乎总是，模型属性与数据库列链接，因为模型本质上是您正在与之交互的表。但是在某些情况下，您不需要将属性链接到数据库，因此**您可以像创建任何 Ruby 类一样创建属性。这是一个巨大的“啊哈！”我的时刻。**

现在，我可以用最简单的方法创建类属性:

```
class User < ApplicationRecord

  def superpower_email; end

end 
```

Enter fullscreen mode Exit fullscreen mode

但这似乎不太对。毕竟，我真的不需要一个方法来运行任何逻辑。所以，我转而求助于这个解决方案:

```
class User < ApplicationRecord
  attr_acccessor :superpower_email
end 
```

Enter fullscreen mode Exit fullscreen mode

我之所以需要它是一个`accessor`属性，而不是仅仅*一个读者*或者仅仅*一个作者*，是因为这样表单既可以正确地呈现属性，又可以将数据作为参数传递。此外，如果我需要的话，这将使以后添加额外的属性变得更加容易。

*更新:正如 [@kinduff](https://dev.to/kinduff) 在评论中提到的，`accessor`和`reader` + `writer`的区别可能会让人误解。`accessor`是同义词，是同时使用`attr_reader`和`attr_writer`的别名。*

因此，我继续将属性添加到用户模型 *(class)* ，添加新属性作为允许的强参数，检查电子邮件是否作为作业排队，并且它工作了！万岁。代码返回到生产中！

### 感谢阅读这篇长 TIL。:)

### 引用:

1.  [Go Rails 剧集和脚本-虚拟属性和 Rails 5 属性 API](https://gorails.com/episodes/virtual-attributes-and-rails-5-attribute-api)