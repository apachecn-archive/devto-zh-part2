# 从头开始:用户认证(第 2 部分)

> 原文：<https://dev.to/absolux/from-scratch--user-authentication-part-2-21mc>

在我之前的[文章](https://dev.to/absolux/from-scratch-user-authentication-37a7)中，我展示了用户识别解决方案，以及我们如何让它变得简单明了。在这篇文章里，我只是简单的说一下认证成功后的下一步，叫做:`User authorization`。

综上所述，用户认证是根据用户凭证检查和检索用户对象的过程，如下所示:

```
 ++++++++++++++++++
                      +                +
    Credentials --->  + Authentication +  ---> User?
                      +                +
                      ++++++++++++++++++ 
```

但是，在用户被成功识别后会发生什么呢？

在大多数情况下，我们必须检查用户访问资源的能力，如果没有授权，就用一个`403 Forbidden`错误拒绝传入的请求。

我对实现还没有一个清晰的愿景，但是它将遵循与认证相同的理念，其中`boolean`结果指示用户是否被授权:

```
 +++++++++++++++++
               +               +
    User --->  + Authorization +  ---> boolean
               +               +
               +++++++++++++++++ 
```

我欢迎任何关于如何尽可能干净地实现的建议、想法或文章。所以，不要犹豫，在评论中分享吧。

谢了。