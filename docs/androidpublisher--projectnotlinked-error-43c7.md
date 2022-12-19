# Android publisher & project not linked 错误

> 原文：<https://dev.to/sandris/androidpublisher--projectnotlinked-error-43c7>

所以花了几个小时处理 Google Play 上的项目链接错误。我会把它放在这里，以防有人出错。

如果您在向 Android Publisher API 发出请求时收到此响应，但是**您的项目已经链接** :

```
{  "error":  {  "code":  403,  "message":  "The project id used to call the Google Play Developer API has not been linked in the Google Play Developer Console.",  "errors":  [  {  "domain":  "androidpublisher",  "message":  "The project id used to call the Google Play Developer API has not been linked in the Google Play Developer Console.",  "reason":  "projectNotLinked"  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

尝试创造新的应用产品。如果链接前有产品创建，则购买时产品交易取数失败。有了新产品，错误就消失了。