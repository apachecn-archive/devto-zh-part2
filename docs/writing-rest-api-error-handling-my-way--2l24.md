# 用我的方式写 REST API 错误处理？

> 原文：<https://dev.to/coolgoose/writing-rest-api-error-handling-my-way--2l24>

花时间在我的副业项目上做更多的工作，我偶然发现了如何在 API 上全局管理错误处理的常见问题。

我通常会制作自己的结构，然后就到此为止，但我认为回顾一下其他人是如何做的会很好(特别是因为我是用 c#做的，这是一种我并不精通的语言)。

所以我像所有人一样从 [{json:api}](http://jsonapi.org/) 开始。
不幸的是，对我来说，有两件事我不喜欢

1.  大多数 c#库关注的是响应，而不是创建/更新/删除
2.  错误处理[规范](http://jsonapi.org/format/#errors)和[示例](http://jsonapi.org/examples/#error-objects-multiple-errors)不容易允许 1:n 字段- >错误映射。

为了进一步阐述第二点，让我们考虑注册页面的情况。

```
<form>
    <label>
        email
        <input type="text" name="username">
    </label>
    <label>
        password
        <input type="text" name="password">
    </label>
    <label>
        password confirm
        <input type="text" name="password_confirm">
    </label>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

使用 json api 的规范，如果用户名不够长，并且遇到了*黑名单*规则，那么响应应该是:

```
{  "errors":  [  {  "source":  {  "pointer":  "/data/attributes/username",  "parameter":  "username"  },  "title":  "Username validation error",  "detail":  "Your username needs to be at least 6 characters."  },  {  "source":  {  "pointer":  "/data/attributes/username",  "parameter":  "username"  },  "title":  "Username validation error",  "detail":  "Your username isn't valid."  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，如果不解析列表，就无法直接知道哪个字段出错了。我认为在这种情况下，*参数*的使用稍微超出了标准，因为它应该是*一个指示哪个 URI 查询参数导致了错误*的字符串。

进一步搜索，我偶然发现了这个[不错的简短列表](https://nordicapis.com/best-practices-api-error-handling/)关于谷歌、必应、推特、脸书是如何处理这种情况的。对我来说不幸的是，它们都返回一个项目数组，没有任何直接引用违规字段的方式。

查看 RFC 7807 揭示了类似的情况:

```
{  "type":  "https://example.net/validation-error",  "title":  "Your request parameters didn't validate.",  "invalid-params":  [  {  "name":  "age",  "reason":  "must be a positive integer"  },  {  "name":  "color",  "reason":  "must be 'green', 'red' or 'blue'"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

我不打算重复 [JSend](http://labs.omniti.com/labs/jsend) ，因为它是一个简单得多的标准。

也就是说，我设想的方法是将*个错误*作为一个对象集合，而不是数组，其结构如下:

```
{  "errors":  {  "username":  [  {  "source":  {  "pointer":  "/data/attributes/username"  },  "title":  "Username validation error",  "detail":  "Your username needs to be at least 6 characters."  },  {  "source":  {  "pointer":  "/data/attributes/username"  },  "title":  "Username validation error",  "detail":  "Your username isn't valid."  }  ],  "password":  [  {  "source":  {  "pointer":  "/data/attributes/password"  },  "title":  "Password validation error",  "detail":  "Your username needs to be at least 6 characters."  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

你们是如何处理错误回复的？

是我太懒了，没有解析响应来获取表单键，还是其他人知道响应中有键显然是个问题？