# 塑造一个业余的 API 认证方法

> 原文：<https://dev.to/spacekidlat/mold-an-amateurs-api-authentication-approach-27k8>

流程
[![](img/4a7987d19df1e68b618e78e4ac90c56e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jGgcnp6h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qxfhwii6yos8in3zwklf.jpg)

我知道，我也很熟悉，我们在 API 认证领域有 oauth/oauth2 的进步，但是我忍不住要探讨一下我自己的想法。

我有这个 API 认证的想法。简单明了。但我不禁觉得，在设计这个流程上，我缺少多几个场景可以考虑。

术语表/图例:
令牌=字符串的唯一组合
用户=项目/应用程序中的注册实体
红框=会引发“选择”子句的流程
蓝框= "更新"子句
绿框= "插入"子句

首先，用户向 API 发出创建令牌的请求，API 随后检查令牌表，看是否有标记给用户的令牌尚未过期，如果令牌存在，过期时间将增加并返回给用户，如果没有，则创建并返回新令牌。

成功获得令牌后，用户现在可以从 API 请求资源了。

现在，对于用户向 API 发出的每个资源请求，该请求只需要包括令牌(除了所请求的资源的参数之外),并且已经可以确定哪个用户发送了该请求，API 然后将检查数据库中所述令牌的有效性和到期时间，如果令牌有效，则返回资源，如果不有效，则返回 401 http 代码。

附加说明:考虑第三方登录，可以将某种访问类型附加到令牌上，以确定其目的，即通过 facebook、twitter 等进行访问

我相信这种逻辑，但我想找到一些东西，可以说，使这种方法更好，更符合行业标准。

干杯！