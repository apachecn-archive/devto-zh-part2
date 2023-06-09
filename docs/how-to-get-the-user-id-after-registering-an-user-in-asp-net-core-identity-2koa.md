# 在 ASP 中注册用户后如何获取用户 id？网络核心身份

> 原文：<https://dev.to/coolgoose/how-to-get-the-user-id-after-registering-an-user-in-asp-net-core-identity-2koa>

回到我的副业项目，我通过 API 完成了用户注册。遵循 [JSON API](http://jsonapi.org/format/#crud-creating-client-ids) 规范，它要求在创建数据库条目时返回一个 201 created 和资源 id。

对于使用 ASP 的人来说，这可能看起来微不足道，但在创建新用户时就不那么明显了。

我们举个简单的例子:

```
var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
var result = await _userManager.CreateAsync(user, model.Password);
if (result.Succeeded)
{
    // do stuff here
} 
```

Enter fullscreen mode Exit fullscreen mode

通常你会认为`result`会以某种方式包含用户的 id，但事实并非如此。
但是，如果您的操作成功了，`user`变量将包含新创建的数据库属性。

所以你可以这样做:

```
return StatusCode(StatusCodes.Status201Created, new {
    data = new { id = user.Id }
}); 
```

Enter fullscreen mode Exit fullscreen mode