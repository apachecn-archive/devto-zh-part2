# 在 Elixir 的 ExTwitter 上发推特

> 原文：<https://dev.to/dala00/elixirextwitter-4c58>

Elixir 中有一个名为[ExTwitter](https://github.com/parroty/extwitter) 的 Twitter 用程序库。
于是试着发了推特。

## setting

正如 ExTwitter 上的说明。 access_token 和 access_token_secret 为空，因为它们使用用户的东西。

## 推文

```
 def tweet(token, secret, body) do
    ex_twitter_configure(token, secret)
    ExTwitter.API.Tweets.update(body)
  end

  defp ex_twitter_configure(token, secret) do
    conf = [
      consumer_key: Application.get_env(:extwitter, :oauth)[:consumer_key],
      consumer_secret: Application.get_env(:extwitter, :oauth)[:consumer_secret],
      access_token: token,
      access_token_secret: secret
    ]

    ExTwitter.configure(:process, conf)
  end 
```

Enter fullscreen mode Exit fullscreen mode

为了使用登录中的用户的访问令牌，在执行 API 之前进行了设定。