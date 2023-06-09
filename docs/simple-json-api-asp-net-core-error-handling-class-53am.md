# 简单的 JSON API ASP。NET Core 错误处理类

> 原文：<https://dev.to/coolgoose/simple-json-api-asp-net-core-error-handling-class-53am>

作为我对 [API 响应](https://dev.to/coolgoose/writing-rest-api-error-handling-my-way--2l24)的调查的后续，我决定不再创建另一种随机格式，而是坚持使用 JSON API，即使这意味着更多的前端解析。

话虽如此，ASP。NET Core 对于 *ModelState* 验证的标准响应格式没有遵循约定，所以我写了一个小的 helper 类。

不幸的是。NET Core 2.0 我不知道是否有办法覆盖默认的响应格式(也许用一个扩展方法？下面让我知道)所以我刚刚截取了`ModelState.IsValid`。

这里有一个用户注册方法的小片段

```
[AllowAnonymous]
[HttpPost]
public async Task<IActionResult> Store([FromBody] Models.Validators.UserRegister item)
{
    if (item is null)
    {
        return BadRequest(_apiResponse.AddErrorResponse(code: "InvalidFormData").ErrorReponse);
    }

    if (!ModelState.IsValid)
    {
        return BadRequest(_apiResponse.ParseModelStateResponse(ModelState).ErrorReponse);
    }

    var user = new User { UserName = item.Email, Email = item.Email };
    var result = await _users.UserManager.CreateAsync(user, item.Password);

    return Ok(result);
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是这个类(以及[复制粘贴的要点](https://gist.github.com/CoolGoose/0ea96db498d0c9933af476ff6dc5156a)):

没有什么太特别的，只是一点方法链接和可选的命名参数。

```
using Newtonsoft.Json.Linq;
using System.Linq;
using Microsoft.AspNetCore.Mvc.ModelBinding;

namespace Craidd.Helpers
{
    /// <summary>
    /// Manage JSON Api response
    /// </summary>
    public class ApiResponseHelper : IApiResponseHelper
    {
        public JObject ErrorReponse { get; set; } = new JObject(new JProperty("errors", new JArray()));

        /// <summary>
        /// Create a JSON API Error Response
        /// </summary>
        /// <param name="title"></param>
        /// <param name="detail"></param>
        /// <param name="code"></param>
        /// <param name="sourceParameter"></param>
        /// <returns></returns>
        public ApiResponseHelper AddErrorResponse(string title = null, string detail = null, string code = null, string sourceParameter = null)
        {
            var error = new JObject();

            if (! (title is null) )
            {
                error.Add(new JProperty("title", title));
            }

            if (! (detail is null) )
            {
                error.Add(new JProperty("detail", detail));
            }

            if (! (code is null) )
            {
                error.Add(new JProperty("code", code));
            }

            if (! (sourceParameter is null) )
            {
                error.Add(new JProperty("source", new JObject( new JProperty("parameter", sourceParameter))));
            }

            ErrorReponse["errors"].Value<JArray>().Add(error);

            return this;
        }

        /// <summary>
        /// Parse a ModelState validation into a JSON API Error response
        /// </summary>
        /// <param name="modelState"></param>
        /// <returns></returns>
        public ApiResponseHelper ParseModelStateResponse(ModelStateDictionary modelState)
        {
            var errorData = modelState.Where(ms => ms.Value.Errors.Any())
                                      .Select(x => new { x.Key, x.Value.Errors });

            foreach (var stateError in errorData)
            {
                var errorMessages = stateError.Errors;

                foreach (var errorMessage in errorMessages)
                {
                    AddErrorResponse(sourceParameter: stateError.Key, detail: errorMessage.ErrorMessage);
                }
            }

            return this;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你是如何处理应用程序中的 API 响应的？