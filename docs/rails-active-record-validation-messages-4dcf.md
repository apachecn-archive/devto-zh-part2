# 更好的 Rails 活动记录验证消息

> 原文：<https://dev.to/spidergears/rails-active-record-validation-messages-4dcf>

验证用于确保只将有效数据保存到数据库中，并且不违反任何业务规则。例如，对于企业来说，确保用户提供有效的电子邮件地址和电话号码可能很重要。模型级验证是实现这一点的最常见方式(db 级约束是另一种选择)。模型级验证与数据库无关，易于测试和维护。Rails 通过内置的帮助器和对定制验证的支持让这变得更加有趣。

所有这些都完成了，下一个关键的步骤是在用户使用你的网站时给他们提供即时的反馈。Rails 允许通过`:message`选项做到这一点。它指定当验证失败时将被添加到 errors 集合中的自定义错误消息。

`:message`选项接受一个`string`或一个`proc`。

*   当使用字符串时，rails 提供了占位符/插值变量来帮助添加上下文并减少认知负荷。
    *   %{value}
    *   %{attribute}
    *   %{model}在消息字符串中使用它们作为，

```
validates :age, numericality: { message: "%{attribute} must be a number" } 
```

`Age must be a number`感觉比`must be a number`好多了。

*   使用 procs 为消息字符串增加了更多的灵活性和逻辑性。Proc 可以访问被验证的对象和带有关键字`model`、`attribute`和`value`的数据散列，与上面的插值变量相同。

```
validates :username,
    uniqueness: {
      message: ->(object, data) do
        "Hey #{object.name}!, #{data[:value]} is taken already!"
      end
    } 
```

```
validates :score,
    presence: {
      message: ->(object, data) do
        if object.member?
          "something"
        else
          "Something else"
        end    
      end
    } 
```

神奇的还不止于此，rails 还允许通过 i18n 本地化文件覆盖默认的验证消息。

```
en:
  activerecord:
    errors:
      models:
        user:
          attributes:
            firstname:
              blank: "custom message, can\'t be blank" 
```

[![Presence validation error on firstname](../Images/c613ef00f84c735b11d392558589b701.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DdMCO0z8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3z470kpbpa0qmq6t6e13.png) 
活动记录提供了几个可以放置消息翻译的名称空间，以便为某些模型、属性和/或验证提供不同的消息和翻译。它还透明地考虑了单表继承。

占位符/插值变量也仍然可用。

```
en:
  activerecord:
    errors:
      models:
        user:
          attributes:
            firstname:
              blank: "Please fill in your %{attribute}" 
```

[http://guides.rubyonrails.org](http://guides.rubyonrails.org/i18n.html#translations-for-active-record-models)

此外，active model::Errors # full _ messages 使用从`errors.format`开始查找的分隔符(默认为`%{attribute} %{message}`)将属性名称添加到错误消息的前面。这也可以定制为

```
en:
  errors:
    # format to use in full error messages.
    format: "%{message} %{attribute}" 
```