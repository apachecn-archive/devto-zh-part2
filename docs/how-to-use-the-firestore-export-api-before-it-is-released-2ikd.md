# Firestore Export API 发布前如何使用

> 原文：<https://dev.to/christianalexander/how-to-use-the-firestore-export-api-before-it-is-released-2ikd>

## TL；速度三角形定位法(dead reckoning)

查看 [FirestoreRestore](https://github.com/christianalexander/FirestoreRestore) 。
它使用一种全新的 API 来备份和恢复你的 Firestore 数据库！

## 背景

已经有很多人要求 Google 在他们的云 Firestore 产品中增加备份和恢复功能。直到本周，备份 Firestore 数据库的唯一方法是作为管理客户端运行遍历树的实用程序，将每个文档的 JSON 表示保存在磁盘上。

## 发现

今天早上，我打开了[谷歌 API 浏览器](https://developers.google.com/apis-explorer/#s/firestore/v1beta1/)，发现 Firestore `v1beta1` : `exportDocuments`和`importDocuments`下面列出了两个新方法。

[![APIs Explorer Screenshot](img/6277286a29dc78e8c5b20f29ab9ca024.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OVajWMhp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mypdp1zzzof9nv0cl0l0.png)

## 探索

正如 API 文档所述，`firestore.projects.databases.exportDocuments`能够将 Firestore 数据导出到 Google 云存储中。

为了测试这一点，我打开了 [Firebase 控制台](https://console.firebase.google.com)并创建了一个新项目。为了备份数据，我从控制台创建了一个用户文档。

[![Creating a document](img/74a9b7125d13d18655e1efccfdb310f9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--40mUHeY2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cxrb8v1lyzh7zrzpv4ty.png)

在同一个项目中，我打开了 [Google 云存储控制台](https://console.cloud.google.com/storage/)，并创建了一个存储我的备份的桶。

从 [Google APIs Explorer 的`exportDocuments`页面](https://developers.google.com/apis-explorer/#s/firestore/v1beta1/firestore.projects.databases.exportDocuments)，我输入了 Firebase 项目名称，以及存储桶的路径，然后点击**执行**。

[![Execute!](img/7013b67fe59984be5f0a5d8d0e5efca5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_XMNJ1dM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oy92jzhev0sspj14dtoq.png)

以下是 API 的回应:

```
{  "name":  "projects/firestore-restore/databases/(default)/operations/<REDACTED>",  "metadata":  {  "@type":  "type.googleapis.com/google.firestore.admin.v1beta1.ExportDocumentsMetadata",  "startTime":  "2018-07-18T23:20:26.535130Z",  "operationState":  "PROCESSING",  "outputUriPrefix":  "gs://firestore-restore/backups/2018-07-18"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

过了一会儿，我打开了水桶。果然，数据出现了！

[![Backup in the bucket](img/697a5a52aac66642e659d1bf5216f818.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rsVq9jzt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/icikz8lm449v1ytzhy3d.png)

## 恢复

现在，为了测试恢复功能。我删除了 Firestore 数据库的内容，[用和导出请求相同的参数运行`importDocuments`](https://developers.google.com/apis-explorer/#s/firestore/v1beta1/firestore.projects.databases.importDocuments) ，果然数据回来了！

## 备份是否完成？

不幸的是，目前没有“记录”的方法来检查正在进行的 Firestore 导出的状态。幸运的是，通过一些 URL 猜测，我能够找到操作资源的端点。它一直都在那里。导出响应中的`name`字段是状态路径！

## 临时工具:[火店](https://github.com/christianalexander/FirestoreRestore)

由于我不知道 Google 何时会正式支持和构建用于备份和恢复云 Firestore 数据库的工具，所以我开发了一个简单的命令行实用程序来与该服务进行交互。

它需要创建一个具有“云数据存储导入导出管理员”角色的[服务帐户](https://console.cloud.google.com/iam-admin/serviceaccounts/project)。使用服务帐户的`JSON`密钥，该工具能够执行备份和恢复。

查看自述文件了解更多信息😀

## 猜测

谷歌可能会在 Firebase UI 中推出备份和恢复功能。它可能会公开 API 中存在的每个集合的备份功能。它可能比你在网上找到的命令行程序更容易使用。

在那之前，好好享受 CLI 吧！