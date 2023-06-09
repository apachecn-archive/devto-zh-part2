# 使用 IAM 角色和 boto3 自动刷新 AWS 令牌

> 原文：<https://dev.to/li_chastina/auto-refresh-aws-tokens-using-iam-role-and-boto3-2cjf>

#### 时辰的诅咒

AWS 中的会话管理很复杂，尤其是在使用 IAM 角色进行身份验证时。获取 AWS 凭证的一种常见方式是假设一个 IAM 角色，并获得一组仅在特定时间段内有效的临时会话密钥。最大会话持续时间是 IAM 角色本身的设置，默认情况下为一小时。因此，如果用户没有为`DurationSeconds`参数指定一个值，他们的安全凭证只在一个小时内有效。

承担 IAM 角色的典型 *boto3* 请求看起来像:

```
response = client.assume_role(
    RoleArn='string',
    RoleSessionName='string',
    Policy='string',
    DurationSeconds=123,
    ExternalId='string',
    SerialNumber='string',
    TokenCode='string'
) 
```

Enter fullscreen mode Exit fullscreen mode

其中`DurationSeconds`是角色会话的持续时间。它可以达到角色的最大会话持续时间设置。**因此，如果您的 IAM 角色仅设置为最多一个小时，您将无法延长会话的持续时间，除非您更新 IAM 角色本身的设置。**

#### 长期有效的凭证

因此，必须引入一种替代方法来扩展 IAM 角色会话。这时我发现了`RefreshableCredentials`，一个 [botocore 类](https://www.pydoc.io/pypi/botocore-1.7.0/autoapi/credentials/index.html#credentials.RefreshableCredentials)，它的作用就像一个容器，用于存放认证请求所需的凭证。而且，它可以自动刷新凭据！这正是我所需要的。但是它的用法没有被很好的记录。看它的源代码:

```
def __init__(self, access_key, secret_key, token, expiry_time, refresh_using, method, time_fetcher=_local_now): 
```

Enter fullscreen mode Exit fullscreen mode

`__init__`函数有几个参数，其中一半我不认识。但是有一个类方法可以用来初始化一个对象:

```
@classmethod
def create_from_metadata(cls, metadata, refresh_using, method):
    instance = cls(
         access_key=metadata['access_key'],
         secret_key=metadata['secret_key'],
         token=metadata['token'],
         expiry_time=cls._expiry_datetime(metadata['expiry_time']),
         method=method,
         refresh_using=refresh_using)
    return instance 
```

Enter fullscreen mode Exit fullscreen mode

其中`metadata`是包含当前会话丰富信息的字典，即。`access_key`、`secret_key`、`token`、`expiry_time`，都是我们可以从 *boto3 的* STS 客户端的 [assume_role()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sts.html#STS.Client.assume_role) 请求中得到的东西。

为了构造元数据响应，我们做了一个简单的 *boto3* API 调用:

```
import boto3
sts_client = boto3.client("sts", region_name=aws_region)
params = {
    "RoleArn": self.role_name,
    "RoleSessionName": self.session_name,
    "DurationSeconds": 3600,
}
response = sts_client.assume_role(**params).get("Credentials")
metadata = {
    "access_key": response.get("AccessKeyId"),
    "secret_key": response.get("SecretAccessKey"),
    "token": response.get("SessionToken"),
    "expiry_time": response.get("Expiration").isoformat(),
} 
```

Enter fullscreen mode Exit fullscreen mode

`refresh_using`是一个可调用函数，它返回一组新的凭证，采用元数据的格式。记住在 Python 中，*函数是一等公民*。您可以将它们赋给变量，存储在数据结构中，作为参数传递给其他函数，甚至作为其他函数的值返回。所以我只需要一个生成并返回元数据的函数。

```
def _refresh(self):
    " Refresh tokens by calling assume_role again "
    params = {
        "RoleArn": self.role_name,
        "RoleSessionName": self.session_name,
        "DurationSeconds": 3600,
    }

    response = self.sts_client.assume_role(**params).get("Credentials")
    credentials = {
        "access_key": response.get("AccessKeyId"),
        "secret_key": response.get("SecretAccessKey"),
        "token": response.get("SessionToken"),
        "expiry_time": response.get("Expiration").isoformat(),
    }
    return credentials 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备创建一个`RefreshableCredentials`对象:

```
from botocore.credentials import RefreshableCredentials
session_credentials = RefreshableCredentials.create_from_metadata(
    metadata=self._refresh(),
    refresh_using=self._refresh,
    method="sts-assume-role",
) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用这些凭证来生成一个 IAM 角色会话，这个会话可以持续到我们需要的时间:

```
from boto3 import Session
from botocore.session import get_session
session = get_session()
session._credentials = session_credentials
session.set_config_variable("region", aws_region)
autorefresh_session = Session(botocore_session=session) 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们可以在该会话中生成一个 *boto* 客户端，即。:

```
db_client = autorefresh_session.client("rds", region_name='us-east-1') 
```

Enter fullscreen mode Exit fullscreen mode