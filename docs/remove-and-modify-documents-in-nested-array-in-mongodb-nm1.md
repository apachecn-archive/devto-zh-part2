# 删除和修改 MongoDB 中的嵌套文档

> 原文：<https://dev.to/mfahlandt/remove-and-modify-documents-in-nested-array-in-mongodb-nm1>

我有一个相当复杂的文档结构来实现当您执行一个查询时所有数据都可用的方法。然而，修改它是一个大问题。

所以让我们看看我们想要修改的对象

```
 {
    "_id" : ObjectId("5b0bf696cb5dd80010bea0a3"),
    "domains" : [ 
        {
            "_id" : ObjectId("5b0bf696cb5dd80010bea0a4"),
            "DKIMRecordName" : "mailjet._domainkey.example.de.",
            "DKIMRecordValue" : "k=rsa; p=000000000000000000000000",
            "DKIMStatus" : "Not Checked",
            "Domain" : "example.de",
            "IsCheckInProgress" : false,
            "SPFRecordValue" : "v=spf1 include:spf.mailjet.com ?all",
            "SPFStatus" : "Not Checked",
            "emails" : [ 
                {
                    "_id" : ObjectId("5b0bf696cb5dd80010bea0a5"),
                    "CreatedAt" : ISODate("2018-05-28T12:31:18.000Z"),
                    "DNSID" : "2837371580",
                    "Email" : "no-reply@example.de",
                    "EmailType" : "unknown",
                    "Filename" : "",
                    "ID" : "216556",
                    "IsDefaultSender" : false,
                    "Name" : "",
                    "Status" : "Inactive"
                },
                {
                    "_id" : ObjectId("5b0bf696cb5dd45410bea0a5"),
                    "CreatedAt" : ISODate("2018-05-28T12:31:18.000Z"),
                    "DNSID" : "2837371580",
                    "Email" : "newsletter@example.de",
                    "EmailType" : "unknown",
                    "Filename" : "",
                    "ID" : "216556",
                    "IsDefaultSender" : false,
                    "Name" : "",
                    "Status" : "Inactive"
                }
            ]
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以如果你想修改其中一个，我们将使用下面的

```
db.yourCollection.update({'domains.emails.Email': 'no-reply@example.de'},
{ $set: {'email.$.emails':  
    {
                    "_id" : ObjectId("5b0bf696cb5dd80010bea0a5"),
                    "CreatedAt" : ISODate("2018-05-28T12:31:18.000Z"),
                    "DNSID" : "2837371580",
                    "Email" : "no-reply@example.de",
                    "EmailType" : "unknown",
                    "Filename" : "",
                    "ID" : "216556",
                    "IsDefaultSender" : false,
                    "Name" : "",
                    "Status" : "Active"
                }

  },
{multi: true}) 
```

Enter fullscreen mode Exit fullscreen mode

为什么我们要把整个物体放进去？遗憾的是，我们不能使用第二个位置$运算符。所以我们必须提供完整的嵌套文档。此外，它必须作为查询文档的一部分，否则它将无法识别它。这将引发错误“位置运算符没有从查询中找到所需的匹配。”

我们还可以做一个拉操作:

```
db.yourCollection.update({'email.emails.Email': 'no-reply@example.de'},
{ $pull: {'email.$.emails':  {'Email': 'no-reply@example.de'}}  },
{multi: true}) 
```

Enter fullscreen mode Exit fullscreen mode

这将从嵌套数组中移除子文档。

请注意，multi: true 不起作用，您必须多次运行查询。