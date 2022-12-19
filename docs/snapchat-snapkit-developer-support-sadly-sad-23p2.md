# Snapchat Snapkit 开发者支持可悲可悲

> 原文：<https://dev.to/bbookman/snapchat-snapkit-developer-support-sadly-sad-23p2>

[媒体上的原始帖子](https://medium.com/adventures-in-ios-mobile-app-development/snapchat-snapkit-developer-support-sadly-sad-89d63011c6ad)

## 我尝试使用 Snapchat 的 Snapkit 的定期更新日记的第 1 部分

作为一名 iOS 开发者，我认为尝试将 Snapchat 的 Snapkit 功能添加到我正在开发的应用程序中会很有趣。

Snapchat 在保持其平台相关性的竞赛中奋起直追，并希望软件开发商接受其工具。脸书和 Twitter 很早就有了应用程序开发包，以至于用脸书和 Twitter 登录应用程序已经很平常了。发布到这两个网站可能是成千上万个应用程序的内置功能。
[![Snapkit options](../Images/44bb01c7d0af8fbf5b229bc835b1df2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C0EFyTZq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2ANNZQBVFov8g_H1pOWzQ7mw.png) 
在对这些产品略知皮毛之后，我对 Snapchat 的文档和支持并不满意。

### 对好文档的热爱哦

这些是 iOS API 参考的第一行

> 本文档假设您已经与我们联系过。Snap Kit 开发人员门户是添加应用程序、交换密钥以及使用适当的 API 凭据进行设置的地方。我一点也不清楚人们如何或为什么会“接触”Snapchat。我怀疑这份文件已经过时，而且是在 Snapchat 与合作伙伴合作时写的。据我所知，API 现在对任何感兴趣的人完全开放。所以我要么是错的，需要联系 Snapchat(怎么联系？)或者这第一段完全是误导和过时的。

接下来，我想我会集成 Snapchat 登录。我的应用程序已经通过了谷歌、推特和脸书的认证(你看到 Snapchat 了吗……你需要让我大吃一惊才能进入游戏)

下面是登录工具包文档中的一些说明
[![info.plist instructions](../Images/27813cce1aeaf69c05a71c49d1837823.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G7JuIfbD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AzPAVeRhM0t-JEAwS9qhhLA.png) 
这一行关于 SCSDKRedirectUrl 的内容令人难以置信

*   scsdkredicturl(string):将处理和完成登录请求的 URL 必须是形式为 **foo://bar 的有效 URL 如果没有 bar** ，重定向将会失败

不好意思，你说什么？哪个 URL 将完成并处理登录？还有不带杠的“foo://bar”是什么意思？如果它的意思和我想的一样，你为什么要写这样的东西？(我觉得意思是..someurl://')

我想作为一名开发人员，我应该知道 URL 的语法应该是什么样子。但是等等，Snapchat 有几个 [GitHub 库](https://github.com/Snap-Kit)的例子。

但是等等，只有一个例子是用 Swift 写的(记住我是为 iOS 开发的)。

因为我喜欢尝试解决自己的问题，所以我想这个示例应用程序可能会揭示 SCSDKRedirectURL 的奥秘。这个 URL 应该放在 info.plist 中。让我们看看 Snapchat 的示例应用程序在 info.plist 中有什么

```
<dict>      
      <key>CFBundleTypeRole</key>
      <string>Editor</string>
      <key>CFBundleURLSchemes</key>
      <array>
      <string><!-- Insert your redirect url scheme here -->    
      </string>
      </array>
</dict> 
```

Enter fullscreen mode Exit fullscreen mode

太好了，事情就清楚了。

由于我已经在 GitHub 中，并且已经在看 Snapchat 提供的示例应用程序，我想我应该将这种缺乏清晰度的情况作为示例回购的一个缺陷。这是问题#3 [重定向 URL—请在文档中澄清](https://github.com/Snap-Kit/bitmoji-sample/issues/3)

一个叫大卫的人清理了这些东西来回应这个 bug。他说

> 因此，如果您注册了 my-app://auth/snap，那么您的 Info.plist:

```
<key>SCSDKRedirectUrl</key>
<string>my-app://auth/snap</string>
as well as:
<key>CFBundleURLTypes</key>
 <array>
 <dict>
 <key>CFBundleTypeRole</key>
 <string>Editor</string>
 <key>CFBundleURLSchemes</key>
 <array>
 <string>my-app</string>
 </array>
 </dict>
 </array> 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，Snapchat 支持不知道如何处理 Snapkit 请求。当我问他们是否是应该得到帮助的人时，当我读到他们的回答时，我不禁咯咯笑了起来..“我们需要进一步调查此事”

是的，你会的！
[![twitter response from snapchat](../Images/b933c9ec8c859dca5f0bab5929b5c893.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--Bx2vUx-3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AgCTL9i4szIqePqvcSfRr2g.png)