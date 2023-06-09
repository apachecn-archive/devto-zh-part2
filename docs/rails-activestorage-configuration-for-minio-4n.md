# Minio 的 Rails 动态存储配置

> 原文：<https://dev.to/kevinjalbert/rails-activestorage-configuration-for-minio-4n>

*封面图片[小鸟棚车](https://flickr.com/photos/agent_ladybug/661200957) by [b.ug](https://flickr.com/people/agent_ladybug) 在[CC BY-NC](https://creativecommons.org/licenses/by-nc/2.0/)T7】下授权*

你现在看到的是 Rails 5.2 及其全新的[active storage](https://github.com/rails/rails/tree/master/activestorage)——一种处理文件存储的内置抽象/机制。你决定试一试，去掉你通常使用的一个依赖项(即[载波波](https://github.com/carrierwaveuploader/carrierwave)或[回形针](https://github.com/thoughtbot/paperclip))。

出于某种原因，你决定使用[Minio](https://minio.io/)——一个亚马逊 S3 兼容的开源项目。

浏览 ActiveStorage [文档](http://edgeguides.rubyonrails.org/active_storage_overview.html)和[存储库的自述文件](https://github.com/rails/rails/tree/master/activestorage)，你会发现如何使用 ActiveStorage 的`local`服务让一切在本地工作。

现在是时候尝试运行一切了，但是用 Minio 作为你的文件存储。查看您的`config/storage.yml`，您会看到亚马逊 S3 的模板:

```
amazon:
   service: S3
   access_key_id: <%= Rails.application.credentials.dig(:aws, :access_key_id) %>
   secret_access_key: <%= Rails.application.credentials.dig(:aws, :secret_access_key) %>
   region: us-east-1
   bucket: your_own_bucket 
```

Enter fullscreen mode Exit fullscreen mode

# 配置选项

现在是时候弄清楚如何将 S3 服务与你的迷你服务器结合使用了…

## 地区

你的 Minio 服务器并不真正支持像亚马逊的 S3 这样的地区。只需将其作为`us-east-1`或您最近的 S3 地区(尽管它实际上可以是任何字符串)。据我所知，这只是在亚马逊 S3 级别使用，对于你的托管 Minio 服务器来说没关系。

`region`值只是用来满足 ActiveStorage 和`aws-sdk-s3` gem。如果您省略了`region`选项，您会得到下面的异常`missing keyword: region (ArgumentError)`。如果您使用空字符串表示`region`，您将看到`missing region; use :region option or export region name to ENV['AWS_REGION'] (Aws::Errors::MissingRegionError)`。

## 终点

你的 Minio 服务器托管在某个 URL(即[https://minio123.com](https://minio123.com))，所以你需要通知 ActiveStorage 的 S3 服务关于这个*端点*。幸运的是，只需在配置中添加 URL 端点:

```
endpoint: "https://minio123.com" # Points to your Minio server 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用端点上的端口(即" [http://localhost:9000"](http://localhost:9000%E2%80%9D) )。

## 强制路径样式

因此，从配置的角度来看，您已经设置好了端点和区域。您的 Minio 服务器也启动并运行，还有一个 bucket，`your_own_bucket`。您尝试上传一个文件，并看到以下异常:

```
Aws::Errors::NoSuchEndpointError (Encountered a `SocketError` while attempting to connect to:

  https://you_own_bucket.minio123.com/RJioqjrTT4VmFobw5FhXkSby

This is typically the result of an invalid `:region` option or a
poorly formatted `:endpoint` option.

* Avoid configuring the `:endpoint` option directly. Endpoints are constructed
  from the `:region`. The `:endpoint` option is reserved for connecting to
  non-standard test endpoints. 
```

Enter fullscreen mode Exit fullscreen mode

嗯……但是那没有成功。如果我们看网址([https://your \ _ own \ _ bucket . minio 123 . com](https://your%5C_own%5C_bucket.minio123.com))，可以看到它采用了桶子域的方式。然而，Minio 期望桶在域之后(即 https://minio123.com/your\_own\_bucket 的)。再次，幸运的是有一个配置选项我们可以添加到*强制这个路径样式* :

```
force_path_style: true # Needed to be compliant with how Minio serves the bucket 
```

Enter fullscreen mode Exit fullscreen mode

# 完成配置

至此，我们已经涵盖了用 Minio 设置 ActiveStorage 的所有配置*问题*。即，缺失的`endpoint`和`force_path_style`选项。以下是完整的配置。

```
minio:
   service: S3
   access_key_id: <%= Rails.application.credentials.dig(:minio, :access_key_id) %>
   secret_access_key: <%= Rails.application.credentials.dig(:minio, :secret_access_key) %>
   region: us-east-1
   bucket: your_own_bucket
   endpoint: "https://minio123.com"
   force_path_style: true 
```

Enter fullscreen mode Exit fullscreen mode

将配置命名为`minio`并不是一个坏主意，只是为了明确它是一个 Minio 文件存储，而不是典型的亚马逊 S3。