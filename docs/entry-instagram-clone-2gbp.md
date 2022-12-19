# [词条] Instagram 克隆

> 原文：<https://dev.to/troy34/entry-instagram-clone-2gbp>

# 我造了什么

下面是 Google Drive 上一个视频的链接:[https://Drive . Google . com/file/d/1 q 9 ritdg 4 xdbaocbntgmzkdwnthxf 6 vpr/view？usp =共享](https://drive.google.com/file/d/1q9rITDg4XdbAoCbNTGmzkDWNTHXf6Vpr/view?usp=sharing)

我做了一个 Instagram 克隆。你可以上传图片，其他人可以实时查看。也可以心形图片。Instagram 克隆使用 Pusher 实时更新图片和赞

## 演示链接

https://pushergram.herokuapp.com/
这款应用反应灵敏，可以在移动和桌面设备上查看。

## 链接到代码

[https://github.com/9jaswag/instaclone](https://github.com/9jaswag/instaclone)

# 我是怎么建的(栈是什么？在这个过程中，我遇到了问题或发现了新的东西吗？)

#### 堆栈

*   Ruby on Rails
*   推进器
*   设计
*   引导程序
*   载波波
*   Heroku 主持
*   用于保存图像的 AWS

我用了 4 个小时构建了这个应用程序😴 😪。我使用 Ruby on Rails 构建它，并用 Devise 处理用户认证。

#### 问题

我在开发这个应用时面临的两个问题是处理图片上传和实时更新赞和新帖子。

在对图片上传进行了不同的尝试后，我选择了回形针，一切都很顺利，直到我部署到 Heroku，在上传 1 分钟后找不到上传的图片，在谷歌搜索了几分钟后，我发现 Heroku 不存储图片。我最终使用 Carrierwave 上传图片，因为它与 AWS 集成得很好。

当需要实时更新图片喜好时，我必须找到一种方法来识别 DOM 中需要更新喜好的图片。为了解决这个问题，我使用`data attributes`通过 id 定位帖子并更新他们的喜欢。

# 附加资源/信息

- [载波](https://github.com/carrierwaveuploader/carrierwave)和[设计](https://github.com/plataformatec/devise)是救生员。我可以用它们轻松处理用户认证和图片上传。
-Pusher 让实现实时功能变得像 ABC 一样简单

*更新*:这里有一篇关于我如何开发这个应用的帖子:

[![troy34 image](img/9ff20fd28c97d3e5978574e8151c1682.png)](/troy34) [## 用 Ruby 和 Pusher 构建 Instagram 克隆

### Chuks Opia

#pushercontest#rubyrails#instagram#pusher](/troy34/build-an-instagram-clone-with-ruby-and-pusher-54ik)