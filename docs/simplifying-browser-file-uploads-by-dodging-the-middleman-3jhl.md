# 通过避开中间人，简化浏览器文件上传

> 原文：<https://dev.to/akshaygoyal174/simplifying-browser-file-uploads-by-dodging-the-middleman-3jhl>

*原载于[媒体](https://medium.com/@akshay.g174_68301/simplifying-browser-uploads-by-bypassing-the-middleman-b7fdbab63d67)T3】*

**免责声明:**这是我的第一篇博文:)。经过多年的拖延，我终于能够专心致志地坐下来写一篇文章。如果你发现写得不够好，那么请让我知道我可以在未来的文章中改进什么。谢谢，请慢用！

[![Hassle Free Life](../Images/03ef3cbb212caa94b9794f640445c3fd.png "Hassle Free Life")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k1sq32cj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A3j7oRXpqinBlyydcf7U6-A.png)

我目前在一家专注于体育新闻和娱乐的媒体公司工作。我们的大部分可读内容都是众包的。我们的内容创作者根据他们的文章产生的收入获得报酬。这是一个非常透明的系统，而且扩展性很好。这种不断增长的规模有利于业务发展，但也给我们的工程团队带来了许多挑战。因此，我们总是专注于构建系统，能够处理大量的流量，而不会给我们的用户和内容创作者带来任何停机时间。

今天，很大一部分内容消费已经从文本媒体转移到视频。为了跟上时代的变化，我们计划扩展内容创建平台来处理视频上传。嗯……视频完全是另一回事。由于它们的文件非常大，所以需要单独存储和管理。

同时对系统的架构进行头脑风暴。我们提出了几种方法:

## 接近 1:

用户将视频上传到我们的服务器，然后我们将它上传到 s3，并使用 s3 bucket url 发送它以进行进一步编码。嗯，这听起来可能很简单，但这种方法有不必要的开销，可能会导致重大灾难(至少对我们来说)。你怎么问..让我告诉你..我们的微服务之一，服务于面向客户的请求以及内容创建请求。现在，在这种架构中，大量的视频上传请求可能会阻塞服务器(占用 CPU 或服务器带宽)，任何进一步的请求都会超时。

但是如果我有一个自动扩展的环境呢？对于那些不知道什么是自动缩放的人，让我给你一个概述。如果您的服务器每秒可以处理 1000 个请求，而您开始每秒处理 2000 个请求，那么您的服务器将无法满足这些请求，它们将为用户超时。为了解决这个问题，我们可以运行两个并行的服务器，并使用负载平衡器在其上分配流量。一个合理的问题是，为什么我们不总是运行两个并行服务器呢？嗯，我们可以，但是如果请求数量增长到 5000/秒，那么我们将需要 2 台以上的服务器。此外，每台运行的服务器都有一个相关的成本因素。理想的解决方案是仅在实际需要额外的服务器时才增加新的服务器。AWS 提供了这种开箱即用的自动伸缩解决方案。您可以指定一个或多个触发条件(例如:CPU 使用率超过 80%和/或 RAM 使用率超过 90%)，当满足这些条件时，将自动调整您的服务。

现在我们知道了自动缩放的基础知识，让我们来考虑一下我们案例中的一个可能场景。假设有大量来自内容创建者(卸载视频)和最终用户的请求传入服务器。满足这两种要求对企业来说至关重要。现在有了自动扩展，当满足触发条件时，将自动添加一个额外的服务器。一旦新的服务器(或节点)启动并运行，它将开始接收和服务请求。随着两个服务器上的视频请求传入，现在两个节点都接收视频上传并将它们本地存储在各自的 HDD 上。理想情况下，两台服务器都将继续上传到 s3。工作正常，对吧？嗯，不尽然..现在，如果流量减少了一点，并且一个服务器足以服务于请求，那么一个服务器将被杀死(当缩减规模时)。当这种情况发生时，我们不能 100%确定该节点文件系统上的视频是否已经上传到 S3。

## 接近 2:

为了克服前面提到的潜在问题，我们想到跳过中间人，直接从客户的浏览器上传视频到 s3。有了这种方法，我们不必担心请求阻塞服务器和用户在晚上 3 点钟报告问题。因为我们将所有东西都转移到客户端，所以存在另一个安全挑战。我们不希望我们的私人 s3 信息暴露给公众。因此，挑战在于直接从浏览器安全地上传文件到 s3。

**如何设置**
创建新存储桶
首选新存储桶，以便您可以限制用户对其他存储桶的访问
创建具有最低权限的新用户
创建可以写入您的新存储桶的新用户。保存好这些访问密钥和保密密钥，我们以后会用到它们。以下上传策略限制了用户的权限，即使他拥有您的访问密钥和秘密密钥。因此，最好创建一个新用户，不要使用根用户。

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::YOUR_BUCKET_ID_HERE/*"
            ]
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

**处理 CORS 请求**
跨源资源共享(CORS)是一种机制，它使用额外的 HTTP 标头，让用户代理获得从不同源(域)的服务器访问选定资源的权限，而不是当前使用的站点。

```
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <CORSRule>
        <AllowedMethod>POST</AllowedMethod>
        <AllowedOrigin>yourdomain.com</AllowedOrigin>
        <AllowedHeader>*</AllowedHeader>
    </CORSRule> </CORSConfiguration> 
```

Enter fullscreen mode Exit fullscreen mode

## 上传工作原理

在 HTTP 术语中，上传是对 S3 端点的简单 POST 请求。该请求包含文件、文件名(S3 术语中的密钥)、一些元数据、签署的策略和签名。

**流程顺序**

1.  客户端向您的服务器发送一个带有文件名和内容类型的 HTTP GET 请求。在这个请求中不发送实际的文件。
2.  服务器用客户端将用来向 s3 发送请求的字段进行响应。基于你的需求，你可以限制谁可以上传文件到 s3 或者一个人在 24 小时内可以上传的次数。
3.  客户机使用服务器响应，并向 s3 bucket 发送 HTTP POST 请求。这个请求包含实际的文件以及请求体中包含的各种参数。
4.  该请求在 AWS 处被认证。如果成功，则上传文件，否则返回错误。**注意:**如果你的文件很大(> 10MB)，你可以将文件分块，并对每个块重复整个过程。当所有的块都上传到 S3，你可以发送一个合并请求到 S3。这个合并请求告诉 AWS 文件的所有部分都已收到，现在可以合并文件了。

**示例服务器响应**

```
{
   "endpoint_url": "http://${your_bucket_name}.com.s3.amazonaws.com",
   "params": {
      "key": "e26413ddf89eadcde3040f73d0c4e3f4",
      "acl": "public-read",
      "success_action_status": "201",
      "policy": "eyJleHBpcmF0aW9uIjoiMjAxOC0wMi0wNFQ4OjE3OjAwWiIsImNvbmRpdGlvbnMiOlt7ImJ1Y2tldCI6InRlc3Qtdi5zcG9ydHNrZWVkYS5jb20ifSx7ImtleSI6ImUyNjQxM2RkZjg5ZWFkY2QlMzA0MGY3M2QwYzRlM2Y0LnBuZyJ9LHsiYWNsIjoicHVibGljLXJlYWQifSx7InN1Y2Nlc3NfYWN0aW9YXR1cyI6IjIwMSJ9PFsiY29udGVudC1sZW5ndGgtcmFuZ2UiLDBsMTA0ODU3NjBdLHsieC1hbXotYWxnb3JpdGhtIjoiQVdTNC1ITUFDLVNIQTI1NiJ9LHsieC1hbXotY3JlZGVudGlhbCI6IkFLSUFKR1RBMjVHSlJVVk4yNlJBXC8yMDE4MDIwNFwvYXAtc291dGhlYXN0LTFcL3MzXC9hd3M0X3JlcXVlc3QifSx7IngtYW16LWRhdGUiOiIyMDE4MDIwNFQwMDAwMDBaIn0sWyJzdGFydHMtd2l0aCIsIiRDb250ZW50LVR5cGUiLCIiXV10",
      "content-type": "image/png",
      "x-amz-algorithm": "AWS4-HMAC-SHA256",
      "x-amz-credential": "${your_access_key_id}/20180204/${your_s3_bucket_region}/s3/aws4_request",
      "x-amz-date": "20180204T000000Z",
      "x-amz-signature": "3787d8398c33548253ed102a8aas794cf2ebbfafad36251d46489513ef513d35"
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 如何验证 AWS 请求

**在客户端**

[![Client Side](../Images/76b1a4e1226fdaa5f0c9cb7fa7975b85.png "Client side")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sXd_KhXr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AtRuHjWXphDVm3P_dB4_CUw.png)

在客户端。来源:链接

1.  构造对 AWS 的请求。
2.  使用您的秘密访问密钥计算签名。
3.  将请求发送到亚马逊 S3。在您的请求中包括您的访问密钥 ID 和签名。亚马逊 S3 执行下面三个步骤。

**在 AWS 服务器上**

[![Server Side](../Images/ab024b210a42e8742eaea633b21a600d.png "Server side")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OMmpcGxM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AfubNyDwu3D9mzBbgE7s4Vw.png)

在 AWS 服务器上。来源:链接

1.  亚马逊 S3 使用访问密钥 ID 来查找您的秘密访问密钥。
2.  亚马逊 S3 通过请求数据和秘密访问密钥计算签名，使用的算法与您在请求中发送的计算签名的算法相同。
3.  如果亚马逊 S3 生成的签名与您在请求中发送的签名匹配，则该请求被认为是可信的。如果比较失败，请求被丢弃，亚马逊 S3 返回一个错误响应。

## 显示代码

您可以在:
[https://www.github.com/ninetyone/secureBrowserUploadsToS3](https://www.github.com/ninetyone/secureBrowserUploadsToS3)找到服务器和客户端的工作示例

如果你喜欢这篇文章，你可以在推特上关注我

## 附加资源:

[https://docs . aws . Amazon . com/Amazon S3/latest/dev/using http post . html](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingHTTPPOST.html)
T3】https://docs . AWS . Amazon . com/Amazon S3/latest/API/error responses . html(调试时有用)
[https://docs . AWS . Amazon . com/Amazon S3/latest/dev/S3 _ authentic ation 2 . html](https://docs.aws.amazon.com/AmazonS3/latest/dev/S3_Authentication2.html)(了解 AWS 请求认证)