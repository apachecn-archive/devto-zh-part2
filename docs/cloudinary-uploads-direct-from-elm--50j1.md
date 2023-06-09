# Cloudinary 直接从 Elm 上传

> 原文：<https://dev.to/elmupdate/cloudinary-uploads-direct-from-elm--50j1>

如果你需要在第三方服务器上存储你的图片，你可能已经考虑过 cloud inary-[https://cloudinary.com/](https://cloudinary.com/)。它有许多很酷的图像管理特性，可以消除你在应用程序中处理图像的许多麻烦。

Cloudinary 支持多种语言，但是不出所料，对 Elm 没有明确的支持。

但是没有问题，因为有 elm-cloud inary-[http://package . elm-lang . org/packages/GHI vert/elm-cloud inary/latest](http://package.elm-lang.org/packages/ghivert/elm-cloudinary/latest)

好消息是它似乎开箱即用。您需要将一些 JS 代码放入 index.js，然后按照示例添加必要的字段、msg 和更新处理。

现在，我实际上没有让它工作，因为我们的要求是直接从浏览器上传未签名的上传。这是 Cloudinary 支持的一个功能，但它看起来像是在 elm-cloudinary 发布后才出现的。

那么，是时候黑掉 cloudinary.elm 文件了。我发现只需要一个不同的设置文件。

现有的设置文件是:

```
type alias Settings =
    { username : String
    , apiKey : String
    , timestamp : Int
    , signature : String
    } 
```

对于未签名的呼叫，这些需要替换为:

```
type alias SettingsUnsigned =
    { username : String
    , uploadPreset : String
    } 
```

您可以从 Cloudinary 管理门户获取 uploadPreset 字符串。

而不是有一个整体设置记录并填写不同的字段，设置必须改变，因为如果我们继续在原始设置中包括授权字段，那么 Cloudinary 将不会接受上传。

我所做的是获取每个特性——upload photo 和 trackpupload photo——并使用未签名的设置创建未签名的替代项。这是一种廉价的方法，这意味着如果我以后需要使用它们，我不会破坏已签名的特性。目的是当我确信这是可行的时候，我会向最初的 elm-cloudinary 作者提交一个 pull 请求。

我已经尝试过联合类型或其他结构是否可以提供原始的函数集，只是改变设置。如果不中断对 API 的更改，我还无法做到这一点。很想知道是否有人对此有想法。

# 尝试跟踪进度

与此同时，我遇到的另一个障碍是 trackUploadPhoto 特性，或者说我创建了 trackUploadPhotoUnsigned。这是因为我们上传了一张模糊的图片，但是没有进度指示器。

elm-cloudinary 使用 elm 的 Http。进度和查看控制台中的网络响应显示，Progress.track 订阅仅响应它已完成，但没有发送“一些”通知。

对此，我发现了以下非常有用的 post 请求示例:[https://github . com/gdotdesign/elm-ui-examples/blob/master/file-upload-progress/source/main . elm](https://github.com/gdotdesign/elm-ui-examples/blob/master/file-upload-progress/source/Main.elm)

我试着实现了这一点，甚至提出了自己的定制请求，长话短说，我发现如果我将文件发送到一个像“[https://httpbin.org/post](https://httpbin.org/post)”这样的死站，而不是 Cloudinary 的 url，那么我会得到一些进度通知。

进一步在网上寻求帮助，到目前为止，我得出的结论是，这是在 Cloudinary 的情况下，请求在传输编码中使用“chunking ”,这导致在请求响应中不返回 content-length。没有这一点，就无法确定进展。

Cloudinary 在其 JS 小部件中提供了进度功能，我问他们是否有其他方法来获取这些信息。欢迎这方面的任何建议，因为我显然还在学习 http 请求和响应的复杂性。