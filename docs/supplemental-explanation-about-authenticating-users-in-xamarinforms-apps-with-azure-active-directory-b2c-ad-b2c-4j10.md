# 关于在 Xamarin 中验证用户身份的补充说明。使用 Azure Active Directory B2C 的表单应用程序(AD B2C)

> 原文：<https://dev.to/muak_x/supplemental-explanation-about-authenticating-users-in-xamarinforms-apps-with-azure-active-directory-b2c-ad-b2c-4j10>

本文补充描述了[用 Azure Active Directory B2C](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/data-cloud/authentication/azure-ad-b2c) 认证用户的文章。

## 最具参考性的样本项目

由于有太多类似的样本项目，我很难了解它，但我设法找到了以下样本。

[https://github . com/xa marin/xa marin-forms-samples/tree/master/web services/todoazureathadb 2 cclientflow](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoAzureAuthADB2CClientFlow)

这是一个非常有参考价值的示例项目。

## 密码重置链接不起作用的问题。

虽然 AD B2C 本地账户登录表单上有一个密码重置页面链接，但是点击该链接时出现异常，无法过渡到密码重置页面。

这个问题可以通过捕捉异常并判断错误消息来解决。

```
public async Task<bool> LoginAsync()
{
    AuthenticationResult authResult = null;

    try
    {
        authResult = await _client.AcquireTokenAsync(
            Scopes,
            GetUserByPolicy(_client.Users, PolicySignUpSignIn),
            UIParent
        );
    }
    catch (MsalException msalex)
    {
        // As an exception contains the following message occurs when tapping the password reset link,
        // identifing the exception by the message, password reset page is jumped to.
        if (msalex.Message.Contains("AADB2C90118"))
        {
            try
            {
                // If calling no delay, AquireTokenAsync doesn't sometimes return a result forever
                // It is avoided by a few delays is set.
                await Task.Delay(500); // very important! 
                authResult = await _client.AcquireTokenAsync(
                    Scopes,
                    _client.Users.FirstOrDefault(),
                    UIBehavior.SelectAccount,
                    "",
                    new string[] { },
                    ResetAuthority, // https://login.microsoftonline.com/tfp/your_tenant/your_resetpassword_policy
                    UIParent
                );
            }
            catch (MsalException msalex)
            {
                if (msalex.ErrorCode == "authentication_canceled")
                {
                    Debug.WriteLine("password reset user_cancel");
                }
                return false;
            }            
        }

        else if(msalex.ErrorCode == "authentication_canceled" ){
            return false;
        }

        else {
            Debug.WriteLine($"Error Acquire Token: {msalex}");
            throw msalex;
        }        
    }
    catch(Exception ex)
    {
        throw ex;
    }

    if(authResult == null){
        return false;               
    }

    return true;
} 
```

请注意，在调用 AcquireTokenAsync 方法以调用密码重置页面之前，应该设置一些延迟。