# Rails 5.2 活动存储:预览、弹出器和解决许可陷阱

> 原文：<https://dev.to/heroku/rails-52-active-storage-previews-poppler-and-solving-licensing-pitfalls-2b80>

Rails 5.2 上个月刚刚发布，它有一个主要的新特性:活动存储。活动存储通过各种支持服务(如 AWS S3)为活动记录模型提供文件上传和附件。虽然有像[回形针](https://github.com/thoughtbot/paperclip)这样的库来做类似的工作，但这是 Rails 第一次提供这样的功能。在 Heroku，我们认为云存储是一种最佳实践，因此我们确保它在我们的平台上工作。在这篇文章中，我们将分享我们如何为 Rails 5.2 的发布做准备，以及如何使用新的活动存储功能部署应用程序。

## 信任但核实

在 Heroku，信任是我们的首要价值观。当我们得知 Rails 5.2 附带了活动存储时，我们开始试验它的所有特性。活动存储最大的便利之一是它预览 pdf 和视频的能力。不是通过文本链接到资产，而是从文件中提取一个小的 PDF 或视频截图，并呈现在页面上。

Rails 5.2 的测试版使用流行的开源工具 FFmpeg 和 MuPDF 来生成视频和 PDF 预览。我们通过我们的安全和法律部门审查了这些新的二进制依赖项，在那里我们发现 MuPDF 是在 AGPL 下许可的，并且需要商业许可才能使用。如果我们只是在默认情况下将 MuPDF 添加到 Rails 5.2+应用程序中，我们的许多客户将不会意识到他们需要购买 MuPDF 才能在商业上使用它。

2017 年 9 月，AGPL 的限牌引起了公众的关注。为了准备 5.2 版本，我们的工程师[特伦斯·李](https://twitter.com/hone02)努力更新活动存储，以便这个 PDF 预览功能也可以使用没有商业许可证的开源后端。我们在 2018 年 2 月向 Rails [开放了一个 PR，介绍了使用 poppler PDF 作为 MuPDF](https://github.com/rails/rails/pull/31906) 的替代方案的能力。大约一个月后，PR 被合并，现在任何 Rails 5.2 用户——无论是不是 Heroku 用户——都可以呈现 PDF 预览，而无需购买商业许可证。

## Heroku 示例 App 上的主动存储

如果你已经有了一个实现活动存储的应用，你可以[跳到我们关于活动存储的 DevCenter 文档](https://devcenter.heroku.com/articles/active-storage-on-heroku?preview=1)。

或者，您可以使用我们的示例应用程序。这是一个 Rails 5.2 应用程序，它是一个数字公告板，允许人们发布视频、pdf 和图像。您可以[在 GitHub](https://github.com/heroku/active_storage_with_previews_example) 上查看源代码，或者使用 Heroku 按钮部署应用程序:

[![Deploy](../Images/c5f2ec62efbee76fd41eb9eff7b5c1c0.png)T2】](https://heroku.com/deploy?template=https://github.com/heroku/active_storage_with_previews_example)

> 注意:这个示例应用程序需要一个付费的 S3 插件。

这里有一个应用程序做什么的视频示例。

[![Active Storage on Heroku](../Images/6136e0d96a4a312781bf2deca58c0c72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0FtnvaKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.dropbox.com/s/nxnsidob5j8bwev/active-storage.gif%3Fraw%3D1)

打开主页时，选择适当的资产，然后提交表单。在视频中，`mp4`文件被上传到 S3，然后 Rails 在`ffmpeg`的帮助下动态生成预览。相当整洁。

## Heroku 上的主动存储

如果您使用按钮部署了示例应用程序，它已经通过`app.json`配置为在 Heroku 上工作，但是如果您有自己想要部署的应用程序，您如何设置它以便在 Heroku 上工作？

按照针对活动存储的 [DevCenter 文档](https://devcenter.heroku.com/articles/active-storage-on-heroku?preview=1)，您将需要一个所有 dynos 都可以与之对话的文件存储服务。该示例为 S3 使用了一个名为 [Bucketeer](https://elements.heroku.com/addons/bucketeer) 的 Heroku 附加组件，不过您也可以使用现有的 S3 凭证。

首先，将 S3 的 AWS gem 添加到 gem 文件中，如果你也要修改图像，添加 Mini Magick:

```
gem "aws-sdk-s3", require: false
gem 'mini_magick', '~> 4.8' 
```

更新 gem 文件后，不要忘记`$ bundle install`。

接下来，在您的`config/storage.yml`文件中添加一个`amazon`选项，指向 S3 配置，在本例中我们使用的是 Bucketeer 设置的配置:

```
amazon:
  service: S3
  access_key_id: <%= ENV['BUCKETEER_AWS_ACCESS_KEY_ID'] %>
  secret_access_key: <%= ENV['BUCKETEER_AWS_SECRET_ACCESS_KEY'] %>
  region: <%= ENV['BUCKETEER_AWS_REGION'] %>
  bucket: <%= ENV['BUCKETEER_BUCKET_NAME'] %> 
```

然后确保您的应用程序被设置为在生产中使用`:amazon`配置存储:

```
config.active_storage.service = :amazon 
```

如果您忘记了这一步，默认存储是使用`:local`将文件保存到磁盘。这不是在生产中处理上传文件的可扩展方式。如果您不小心将它部署到 Heroku，看起来文件一开始是上传的，但是如果您运行不止一个 dyno，它们会在随机请求时消失。当 dynos 重新启动时，这些文件将一起消失。你可以在开发中心获得更多关于[Heroku 短命圆盘的信息。](https://devcenter.heroku.com/articles/active-storage-on-heroku?preview=1#ephemeral-disk)

最后，你需要做的最后一件事是安装一个定制的构建包，它会安装二进制依赖项`ffmpeg`和`poppler`，这两个依赖项用于生成资产预览:

```
$ heroku buildpacks:add -i 1 https://github.com/heroku/heroku-buildpack-activestorage-preview 
```

一旦你完成，你就可以部署到 Heroku！

## 向现有应用添加活动存储

如果你的应用还没有活动存储，你可以添加它。首先，您需要通过运行:
来启用活动存储 blob 存储

```
$ bin/rails active_storage:install 
```

这将添加一个迁移，让 Rails 跟踪上传的文件。

接下来，您需要一个模型来“附加”文件。您可以使用现有模型，也可以创建新模型。在示例应用程序中，使用了一个几乎为空的`bulletin`模型:

```
$ bin/rails generate scaffold bulletin 
```

接下来，在应用程序上运行迁移:

```
$ bin/rails db:migrate 
```

在数据库被迁移之后，更新模型，让 Rails 知道您打算能够向它附加文件:

```
class Bulletin < ApplicationRecord
  has_one_attached :attachment
end 
```

一旦完成，我们还需要三个部分:上传附件的表单、保存附件的控制器和呈现附件的视图。

如果您有一个现有的表单，您可以通过`file_field`视图助手添加一个附件字段，如下所示:

```
<%= form.file_field :attachment %> 
```

您可以在示例应用程序的[中看到一个带有附件的表单示例。一旦有了表单，您需要保存附件。](https://github.com/heroku/active_storage_with_previews_example/blob/ab0370f77f35f8eb0813727b8d49758926450f5e/app/views/welcome/_upload.html.erb#L14)

在这个示例应用程序中，主页包含表单和视图。在[公告控制器](https://github.com/heroku/active_storage_with_previews_example/blob/ab0370f77f35f8eb0813727b8d49758926450f5e/app/controllers/bulletins_controller.rb#L26-L32)中，附件被保存，然后用户被重定向回主公告列表:

```
def create
  @bulletin = Bulletin.new()
  @bulletin.attachment.attach(params[:bulletin][:attachment])
  @bulletin.save!

  redirect_back(fallback_location: root_path)
end 
```

最后，在[欢迎视图](https://github.com/heroku/active_storage_with_previews_example/blob/ab0370f77f35f8eb0813727b8d49758926450f5e/app/views/welcome/index.erb)中，我们遍历每个公告条目，并根据我们拥有的附件类型，以不同的方式呈现。

在活动存储中，如果系统安装了正确的二进制文件，那么对于 pdf 和视频，`previewable?`方法将返回 true。如果安装了`mini_magick`，则`variable?`方法将为图像返回 true。如果这两种情况都不成立，那么附件可能是下载后最好查看的文件。下面是[我们如何表示那个逻辑](https://github.com/heroku/active_storage_with_previews_example/blob/ab0370f77f35f8eb0813727b8d49758926450f5e/app/views/welcome/index.erb#L24-L37) :

```
<ul class="no-bullet">
  <% @bulletin_list.each do |bulletin| %>
    <li>
      <% if bulletin.attachment.previewable? %>
        <%= link_to(image_tag(bulletin.attachment.preview(resize: "200x200>")), rails_blob_path(bulletin.attachment, disposition: "attachment"))
        %>
      <% elsif bulletin.attachment.variable? %>
        <%= link_to(image_tag(bulletin.attachment.variant(resize: "200x200")), rails_blob_path(bulletin.attachment, disposition: "attachment"))%>
      <% else %>
        <%= link_to "Download file", rails_blob_path(bulletin.attachment, disposition: "attachment") %>
      <% end %>
    </li>
  <% end %>
</ul> 
```

一旦你在应用中安装了所有这些组件，并配置了活动存储用于生产，你的用户就可以轻松地上传和下载文件了。