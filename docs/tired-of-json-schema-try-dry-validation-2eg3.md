# 厌倦了 json-schema，尝试干验证

> 原文：<https://dev.to/alirezabashiri/tired-of-json-schema-try-dry-validation-2eg3>

### 为什么应该在 json-schema 上使用干式验证？

您正在处理来自 API 的 JSON 响应，并且想要验证它们的模式？你的第一选择是什么？也许你会采用 Thoughtbot 的人在本文中解释的伟大方法。我已经启动并测试了基于该技术的各种项目，但我总是觉得自己做错了什么，因为这个过程太痛苦了。我来解释一下；如果您想验证 JSON 响应，首先您应该基于它创建一个 JSON 模式，然后您将编写您的测试并得到一些错误，因为缺少逗号/双引号。

[![errors everywhere](img/ec7c2ee64f7511b30860e5aec3b319a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wyDHUSIf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AinL-FgfOPxYjynFh5-oNfA.png)

测试 JSON 响应的老方法的一个例子。首先，定义 JSON 模式:

```
 {  "type":  "object",  "required":  ["user"],  "properties":  {  "user"  :  {  "type"  :  "object",  "required"  :  [  "id",  "token",  "token_expired_at",  "email",  "firstname",  "lastname",  "gender",  "phone_number",  "birthday"  ],  "properties"  :  {  "id"  :  {  "type"  :  "integer"  },  "token"  :  {  "type"  :  "string"  },  "token_expired_at"  :  {  "type"  :  "string"  },  "email"  :  {  "type"  :  "string"  },  "lastname"  :  {  "type"  :  "string"  },  "firstname"  :  {  "type"  :  "string"  },  "gender"  :  {  "type"  :  "string"  },  "phone_number"  :  {  "type"  :  "string"  },  "brithday"  :  {  "type"  :  "string",  "format":  "date-time"  },  "updated_at"  :  {  "type"  :  "string",  "format":  "date-time"  },  "created_at"  :  {  "type"  :  "string",  "format":  "date-time"  },  }  }  }  } 
```

然后编写您的测试

```
describe "Sign in" do
  context "anonymous user" do
    it "returns data in the specified format" do
      sign_in_as_anonymous

      expect(response).to match_response_schema("users/anonymous_detail")
    end
  end
end 
```

更不用说严重的错误了，你不得不一次又一次地切换到 JSON 模式语法和 Ruby(上下文切换),这样你就失去了时间和注意力。现在我将向您展示一种更好的方法来轻松验证您强大的 JSON。

### 创建自定义匹配器

```
# Gemfile
group :test do
  gem "dry-validation"
end 
```

```
# spec/support/matchers/match_schema.rb
RSpec::Matchers.define :match_schema do |schema|
  match do |response|
    @result = schema.call(JSON.parse(response.body, symbolize_names: true))
    @result.success?
  end

  def failure_message
    @result.errors
  end
end 
```

> 注意:这里我为我的自定义匹配器选择了`/spec/support/matchers/*`，命名约定为`match_*`。另外，`schema.call` `schema`是一个干验证类，`call`用给定的参数初始化 schema 类，然后我返回验证的结果，这是一个真/假值。我甚至定义了`failure_message`来查看在 Rspec 结果中验证模式所产生的错误。

### 定义匿名用户模式

将此添加到您的`spec/rails_helper.rb`

```
require "support/views/schemas" 
```

现在定义匿名用户模式:

```
AnonymousUserSchema = Dry::Validation.Schema do
  required(:user).schema do
    required(:email)
    required(:token).filled
    required(:token_expired_at).filled
    required(:firstname)
    required(:lastname)
    required(:gender)
    required(:phone_number)
    required(:birthday)
  end
end 
```

现在你可以将你的规格改为:

```
describe "Sign in" do
  context "anonymous user" do
    it "returns data in the specified format" do
      sign_in_as_anonymous

      expect(response).to match_schema(AnonymousUserSchema)
    end
  end
end 
```

这就对了。如果我添加了一个新字段呢？您只需将具有所需条件的字段添加到块的末尾。

```
AnonymousUserSchema = Dry::Validation.Schema do
  required(:user).schema do
    required(:email)
    required(:token).filled
    required(:token_expired_at).filled
    required(:firstname)
    required(:lastname)
    required(:gender)
    required(:phone_number)
    required(:birthday)
    required(:age).maybe(int?)
  end
end 
```

有了它，你几乎可以做任何事情。欲知详情，请点击下面的链接。

[http://dry-rb.org/gems/dry-validation/](http://dry-rb.org/gems/dry-validation/)

我希望你喜欢:D