# 永久脸书页面访问令牌

> 原文：<https://dev.to/daviducolo/permanent-facebook-page-access-token-3epi>

在其他来源中，我们还集成了脸书，这是一个小 ruby 脚本，用于获得脸书页面永久令牌。

除非另有说明，否则所有这些步骤都使用 Graph API Explorer。

**创建脸书 App**

如果你已经有一个应用程序，跳过这一步。

```
Go to My Apps.
Click "+ Add a New App".
Setup a website app. 
```

Enter fullscreen mode Exit fullscreen mode

不需要改变它的权限什么的。你只需要一个在你完成访问令牌之前不会消失的应用程序。

**获取用户短期访问令牌**

```
Go to the Graph API Explorer.

Select the application you want to get the access token for (in the "Application" drop-down menu, not the "My Apps" menu).

Click "Get Token" > "Get User Access Token".

In the pop-up, under the "Extended Permissions" tab, check "manage_pages".
Click "Get Access Token". 
```

Enter fullscreen mode Exit fullscreen mode

授予有权管理目标页面的脸书帐户的访问权限。请注意，如果该用户失去访问权限，最终的永不过期的访问令牌可能会停止工作。

出现在“访问令牌”字段中的令牌是您的**用户访问令牌。**

```
 API_URL = "https://graph.facebook.com/v3.0"

  # 1 Generate Long-Lived Access Token
  response = RestClient.get("#{API_URL}/oauth/access_token?grant_type=fb_exchange_token&client_id=#{client_id}&client_secret=#{client_secret}&fb_exchange_token=#{user_access_token}")
  access_token = JSON.parse(response.body)['access_token']

  # 2 Get User ID
  response = RestClient.get("#{API_URL}/me?access_token=#{access_token}")
  id = JSON.parse(response.body)['id']

  # 3 Get Permanent Page Access Token
  response = RestClient.get("#{API_URL}/#{id}/accounts?access_token=#{access_token}")

  # 4 Array of items the user has access to
  JSON.parse(response.body)['data'] 
```

Enter fullscreen mode Exit fullscreen mode

JSON 响应应该有一个数据字段，该字段下是用户可以访问的一组项目。
找到您想要永久访问令牌的页面的项目。access_token 字段应该有您的永久访问令牌。

将其复制并在[访问令牌调试器](https://developers.facebook.com/tools/debug/accesstoken)中测试。在“过期”下面应该写“永不”。