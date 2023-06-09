# API 设计

> 原文：<https://dev.to/kyleparisi/api-design-5852>

## 引用

首先，也是最重要的，我想引用本文中的所有内容。请阅读所有细节。如果我们一起工作，我想强调三件事。

## 实体

几乎所有的路线都应该代表一个实体或实体的集合。这意味着分页和适当的过滤能力供客户端使用。如果客户端请求更新一个实体，该实体应该在响应中。上面的参考很好地涵盖了这个主题。

## 批量操作

批量操作不包含在上面的文章中。我的偏好如下:

```
POST /route
...headers

[{ids: [1, 3], status: 0}, {ids: [2], status: 1}] 
```

Enter fullscreen mode Exit fullscreen mode

这应该会返回修改后的实体，以便客户端可以将数据合并到它的状态中。

## 错误

最后一个话题，我想强调的是错误。对客户端问题使用 4xx http 状态，对服务器端问题使用 5xx 状态。大多数 js 客户端库自动将 4xx 和 5xx http 状态代码解析为错误。所以在我看来，使用字面上正确的 http 状态代码并不是一个硬性要求。响应签名应该是:

```
{  "message"  :  "Something bad happened :(",  "description"  :  "More details about the error here"  } 
```

Enter fullscreen mode Exit fullscreen mode

```
{  "message"  :  "Validation Failed",  "errors"  :  [  {  "field"  :  "first_name",  "message"  :  "First name cannot have fancy characters"  },  {  "field"  :  "password",  "message"  :  "Password cannot be blank"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

大多数情况下，客户端应该能够将消息字段直接放入界面。这意味着它应该是最终用户可读和可理解的。消息也应该少于 200 个字符。

## 额外

*   JSON 键不应该有空格
*   如果一个请求发送了`Accept: application/json`头，后端应该以同样的方式恭敬地回应。

太好了！现在我们可以一起工作了！