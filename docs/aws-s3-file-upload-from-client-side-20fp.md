# 从客户端上传 AWS S3 文件

> 原文：<https://dev.to/alihaider907/aws-s3-file-upload-from-client-side-20fp>

最初发布在我的个人博客[http://haidrali.com/aws-s3-file-upload-from-client-side/](http://haidrali.com/aws-s3-file-upload-from-client-side/)

上周，我推出了一个关于产品的新功能，涉及 AWS S3 的文件上传。当你从前端上传文件到 S3 时，总是有暴露你的 AWS 秘密给用户的风险，所以你有以下选择来避免这种风险

1.  将服务器作为中间件，通过服务器 API 上传，你将对它有更多的控制
2.  允许用户匿名直接上传到 S3

我不喜欢以上两种方法，因为涉及中间件，你上传文件两次，即前端→服务器，服务器→ s3 桶，我真的不希望服务器做这种繁重的工作，代表用户上传文件到 s3，同时允许用户匿名上传，你是给你 S3 桶一张空白支票，即世界上任何人都可以上传到你的 S3 桶，这也是我不能接受的，因为这可能会有非常严重的后果。我希望对上传有更多的控制，同时不损害安全问题，所以我使用 AWS 安全令牌服务来生成秘密(access_key，access_secret ),这些秘密是暂时存在的，并按照您的定义具有有限的访问权限。

### H3 在 AWS 控制台上配置一个 IAM 用户生成临时密钥

为了生成临时密码，您需要在 AWS 控制台上进行以下配置

1.  创建新的 IAM 用户
2.  创建策略以允许在 S3 存储桶上上传
3.  为您的 IAM 用户创建一个角色，以承担安全令牌服务

观看此视频了解配置

[https://www.youtube.com/embed/z-r5w82C8uo](https://www.youtube.com/embed/z-r5w82C8uo)

现在，您可以用这个 ruby 代码
生成 AWS STS

```
aws_sts = AWS::STS::Client.new(
          access_key_id: ENV['AWS_ACCESS_KEY_ID'],
          secret_access_key: ENV['AWS_SECRET_ACCESS_KEY']
        ).assume_role(
          role_arn: ENV['AWS_ROLE_ARN'],
          role_session_name: 'session_name',
          duration_seconds: 12*60*60)
// this will generate access_key, access_secret
aws_sts.credentials 
```

AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY 是您在创建用户后下载的凭据，AWS_ROLE_ARN 是我在视频结尾复制的值

### H3 启用 ACL 上传到 S3

你将能够成功地上传文件与上述代码生成的秘密，因为我们已经使用了一个单独的用户来生成 STS，然后只有这个用户将能够访问文件。要使其他用户也可以访问此文件，您必须在将文件上载到 S3 时，在请求正文或标题中发送 ACL:“public-read”。你可以阅读更多关于 [ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#canned-acl)

我希望这也能帮助你改进你的文件上传。如果你需要任何帮助，请发微博给我。