# Rails 中的简单字符串加密

> 原文：<https://dev.to/shobhitic/simple-string-encryption-in-rails-36pi>

如果您曾经实现过任何种类的 SSO，您将会遇到“中继状态”。中继状态是您发送给身份方的参数，他们会不做任何修改就将其发送给您，以便您可以识别刚刚授权的用户。

这是一个非常常见的流程，被 Google 和许多其他 OAuth 提供商使用。

当我是一个新的程序员时，我只是简单地发送用户的`user_id`作为中继状态，并做一个`User.find(user_id)`来获取用户。

## 为什么我需要加密用户 ID

想象一下，如果你注册了我的服务，然后想添加你的谷歌证书，你可以点击一个按钮，它会带你通过整个授权工作流程，最后以纯文本的形式用中继状态作为你的用户 id 发出一个 Webhook 请求。

这非常糟糕，因为现在一些能够检查元素的用户可以将`user_id`从他们的数字更改为任何整数，而我的应用程序会认为其他用户已经授权了。现在，他们可以在登录页面上使用“使用 Google 登录”,并以其他用户的身份登录。

没有安全感的 af。

当我在我的应用程序中添加一个 [Stride](https://stride.com) 集成时，它会在[证书即将到期时通知](https://monitorcertificates.com)，我遇到了同样的问题，因为他们的官方文档要求我们添加一个按钮，在那里添加一个`relayState`参数。一旦用户添加了我们的应用程序，他们的应用程序就会向我们发送一个 webhook。与 Slack 不同，用户不会被重定向回我们的站点。

所以识别用户的唯一方法是用那个`relayState`并让它去计划`user_id`，这意味着如果你改变按钮的值并点击它，当其他用户的证书将要过期时，你可能会得到通知。

## 加密用户 ID

为了解决这个问题，我在助手类中添加了两个小函数，并分别命名为`encrypt`和`decrypt`。这些函数看起来像这样:

```
# Assuming your Secret Key Base is in Rails.application.secrets.secret_key_base

def encrypt text
  text = text.to_s unless text.is_a? String

  len   = ActiveSupport::MessageEncryptor.key_len
  salt  = SecureRandom.hex len
  key   = ActiveSupport::KeyGenerator.new(Rails.application.secrets.secret_key_base).generate_key salt, len
  crypt = ActiveSupport::MessageEncryptor.new key
  encrypted_data = crypt.encrypt_and_sign text
  "#{salt}$$#{encrypted_data}"
end

def decrypt text
  salt, data = text.split "$$"

  len   = ActiveSupport::MessageEncryptor.key_len
  key   = ActiveSupport::KeyGenerator.new(Rails.application.secrets.secret_key_base).generate_key salt, len
  crypt = ActiveSupport::MessageEncryptor.new key
  crypt.decrypt_and_verify data
end 
```

Enter fullscreen mode Exit fullscreen mode

## `encrypt`如何运作

加密一个`text`需要一个`key`和`salt`。解密一个加密的`text`需要同样的`key`和`salt`，否则这个不行。

我们用这些行生成一个 salt:

```
len   = ActiveSupport::MessageEncryptor.key_len
salt  = SecureRandom.hex len 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了 salt，我们就可以使用 Rails 的`secret_key_base`作为“密钥”来生成加密密钥。

```
key   = ActiveSupport::KeyGenerator.new(Rails.application.secrets.secret_key_base).generate_key salt, len 
```

Enter fullscreen mode Exit fullscreen mode

使用这个密钥，我们创建一个加密对象`crypt`

```
crypt = ActiveSupport::MessageEncryptor.new key 
```

Enter fullscreen mode Exit fullscreen mode

然后我们通过
加密我们的文本

```
encrypted_data = crypt.encrypt_and_sign text 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以简单地返回这个`encrypted_data`并将其作为中继状态提供，但是由于`salt`是随机生成的，我们将知道它是什么，因此无法解密该数据。

我使用了 Rails 的`has_secure_password`和`bcrypt`中的一个技巧，返回了一个包含`salt`和`encrypted_data`的字符串，从`encrypt`方法的最后一行可以看出:

```
"#{salt}$$#{encrypted_data}" 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个字符串，其中既有`salt`又有`encrypted_data`，用户在不知道您的`secret_key_base`的情况下无法将其更改为其他用户的 ID。

## `decrypt`如何运作

一旦你理解了`encrypt`方法，`decrypt`就相当简单了。

`decrypt`方法接受一个`text`参数，从中提取`salt`和`data`(或者更确切地说是`encrypted_data`)。

```
salt, data = text.split("$$") 
```

Enter fullscreen mode Exit fullscreen mode

一旦你有了盐，我们就创建`crypt`对象，就像我们在`encrypt`方法中做的一样:

```
len   = ActiveSupport::MessageEncryptor.key_len
key   = ActiveSupport::KeyGenerator.new(Rails.application.secrets.secret_key_base).generate_key salt, len
crypt = ActiveSupport::MessageEncryptor.new key 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们解密数据:

```
crypt.decrypt_and_verify data 
```

Enter fullscreen mode Exit fullscreen mode

我们从方法中返回。

## 结论

这是一种加密和解密`user_id`或任何其他数据的非常简单的方法。

在一个理想的场景中，您将创建一个表并保存一个应用程序的用户令牌，您将把它作为一个`relayState`发送，并再次使用它来标识用户。这需要时间来达到完美，所以这个简单的加密和解密工作惊人地尽快启动和运行。

但是从长远来看，您应该构建一个表来保证所有这些信息的安全性和健壮性。这就是我对我的应用程序所做的。:)

* * *

如果你是安全专家，你能告诉我我在这里做了什么会使它不安全吗？谢谢！