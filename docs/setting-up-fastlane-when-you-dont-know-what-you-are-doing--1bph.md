# 当你不知道自己在做什么时，建立快车道

> 原文：<https://dev.to/jessachandler/setting-up-fastlane-when-you-dont-know-what-you-are-doing--1bph>

*原载于我的 [100 天代码日志](http://jessachandler.com/blog/r2d90/)*

我在 app store 里看到过几个有很酷的截图和框架和标题的应用，我真的很想这么做。考虑到首先要花多长时间浏览模拟器并截图，这看起来像一个巨大的皮塔饼。当我看到一个关于漂亮的自动截图的帖子时，我想马上加入。诚然，这个帖子是旧的，但这些想法正是我所需要的，而且看起来工具是一样的，可能更好。

### 为什么是快车道？

浪子包括一些工具，可以帮助你自动生成和清理多种语言的截图，并适用于你的应用程序所针对的所有平台。不仅如此，它还自动化了管理版本号、供应和上传应用程序以进行测试和发布的流程。魔法，不是吗？

我想，如果你正在读这篇文章，你可能和我一样，昨天早上。你有一个 iOS 应用程序和一些使用 iTunes Connect 的经验。你以前至少上传过一些应用程序，因为快速通道的价值来自于在这些步骤中节省时间，你知道一旦你做了几次，这些步骤将永远需要花费时间。我将进一步假设您对终端 shell 和 git 很熟悉...你还需要*不要为安装一堆东西而抓狂*。如果您打算更改您的应用程序，制作另一个应用程序，或者为您的应用程序提供多种语言，您应该设置 fastlane。让我们花一点时间来嫉妒我们未来的自己，省下所有的时间。啊啊。

### 好的，我准备好了，怎么样？

文档是非常有用的，但是当我第一次尝试工作时，它并不总是对我有用。你当然可以像我一样，在浏览器的标签页中打开 fastlane webiste 的每一页，并尝试在终端窗口中查找所有在你身边飞过的东西，或者，你可以稍微放松一下，跟着这里走。首先，在浏览器中导航到[浪子文档](https://docs.fastlane.tools/getting-started/ios/setup/)进行参考。

#### 基本设置步骤

*   启动终端
*   如果您计划使用 fastlane 进行测试版构建或上传，请确保首先在 Xcode 项目中启用自动版本控制[苹果指令](https://developer.apple.com/library/content/qa/qa1827/_index.html)
*   安装 fastlane -我用的是自制软件`brew cask install fastlane`
*   导航到您的项目目录——这是包含您的`.xcworkspace`文件的外层目录。
*   初始化你的项目`fastlane init`(在这一点上，如果你没有 ruby，我想你需要做更多的安装)
*   试着通读你在终端上看到的所有东西，但不要紧张。浪子啰嗦且乐于助人。
*   当终端问你一个问题时，回答对你的项目最有意义的问题。如果你在这里犯了错误，你可以在以后修改它。
*   当一切停止时，你应该有一个快车道文件夹。
*   导航到该文件夹，并在您最喜欢的文本编辑器`atom .`中打开
*   用`source "https://rubygems.org" gem "fastlane"`在项目的根目录下创建一个./gem 文件

此时，您可以选择做许多事情，但我认为最好的第一步是确保您的文件有意义。浪子意外地认为我的应用捆绑包 id 是我的 pod 文件夹中的第一个 pod，我必须纠正这一点。让我们来看一下您(通过一个简单的应用程序)需要检查和更新的文件。你的应用可能需要不同的设置或其他调整，我在这里没有显示，请确保你的设置与你正在做的相匹配，特别是出口合规性和评级。如果您缺少一个文件，只需创建它。有几个地方我会说`YOUR_UITEST_SCHEME`——如果你不知道那是什么...我们会涵盖它，只是留下那个占位符，直到你有一个。

- **/fastlane/Appfile** -关于你的应用和你的 itunes connect 状态的基本信息

```
app_identifier "com.TEAM.YOURAPP" # The bundle identifier of your app
# You can find it in your xcode project if you are not sure
apple_id "" # Your Apple email address

itc_team_id "" # iTunes Connect Team ID
team_id "" # Developer Portal Team ID 
```

Enter fullscreen mode Exit fullscreen mode

- **/fastlane/Fastfile** -设置你要做的所有很酷的事情让我们暂时保持安全，不发布
你可以在这里看到我们有两条“车道”:测试版和截图。稍后会详细介绍。

```
default_platform(:ios)
  lane :beta do
      ensure_git_status_clean
      increment_build_number
      commit_version_bump
      add_git_tag
      build_app(
          workspace: "YOUR.xcworkspace",
          scheme: "YOURMAIN",
          clean: true
          )
      upload_to_testflight(
          app_identifier: "YOUR_BUNDLE_ID"
          )
  end

  lane :screenshots do
    capture_screenshots(workspace: "YOUR.xcworkspace", scheme: "YOUR_UITEST_SCHEME")
    frame_screenshots(white: true)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

-**/fast lane/deliver file**-这个让你的 iTunes connect 设置更简单一点

```
# The Deliverfile allows you to store various iTunes Connect metadata
# For more information, check out the docs
# https://docs.fastlane.tools/actions/deliver/

price_tier 0
app_review_information(
  first_name: "YOUR",
  last_name: "YOUR",
  phone_number: "YOUR",
  email_address: "YOUR",
  demo_user: "N/A",       # add the user if testers will need to log in
  demo_password: "N/A",   # add the password if testers need it
  notes: "ANY NOTES FOR TESTING"
)
# if you app has more complex structure, check out /fastlane/metadata/review_information folder

submission_information({
    export_compliance_encryption_updated: false,
    export_compliance_uses_encryption: false,
    content_rights_contains_third_party_content: false,
    add_id_info_uses_idfa: false
})

automatic_release false

app_icon './fastlane/metadata/AppIcon.png'

app_rating_config_path "./fastlane/metadata/itunes_rating_config.json" 
```

Enter fullscreen mode Exit fullscreen mode

- **/fastlane/Snapfile** -这个处理截图你不需要定制太多

```
# The app bundle
app_identifier "com.TEAM.YOURS"
# A list of devices you want to take the screenshots from
devices([
   "iPhone 6",
   "iPhone 8 Plus",
   "iPhone X"
])

languages([
   "en-US",
   "de-DE"
 ])

scheme "YOUR_UITEST_SCHEME"
output_directory "./screenshots"
clear_previous_screenshots true

# Arguments to pass to the app on launch. See https://docs.fastlane.tools/actions/snapshot/#launch-arguments
# launch_arguments(["-favColor red"])

# For more information about all available options run
# fastlane action snapshot 
```

Enter fullscreen mode Exit fullscreen mode

-**/fast lane/metadata/itunes _ rating _ config . JSON**-这个可能不在你的元数据中，但继续构建它并设置它以匹配你的应用

```
{
  "CARTOON_FANTASY_VIOLENCE": 0,
  "REALISTIC_VIOLENCE": 0,
  "PROLONGED_GRAPHIC_SADISTIC_REALISTIC_VIOLENCE": 0,
  "PROFANITY_CRUDE_HUMOR": 0,
  "MATURE_SUGGESTIVE": 0,
  "HORROR": 0,
  "MEDICAL_TREATMENT_INFO": 0,
  "ALCOHOL_TOBACCO_DRUGS": 0,
  "GAMBLING": 0,
  "SEXUAL_CONTENT_NUDITY": 0,
  "GRAPHIC_SEXUAL_CONTENT_NUDITY": 0,
  "UNRESTRICTED_WEB_ACCESS": 0,
  "GAMBLING_CONTESTS": 0
} 
```

Enter fullscreen mode Exit fullscreen mode

-**/fast lane/metadata/AppIcon . png**-直接去把你的 app icon 的 1024x 版本复制粘贴到这里。如果您将它称为其他名称，请更新 Deliverfile 中的路径

#### 设置 UI 测试截图

此时，您的 fastlane 文件夹和文件都已设置好，允许您自动截图。如果您想为它们添加有趣的标题，您必须在框架文件中添加您称为背景的任何图像文件。我不打算讨论标题，因为通过 [frameit 和 github 示例](https://docs.fastlane.tools/actions/frameit/#titles-and-background-optional)可以比我在这里做得更好。然而，我不得不自己学习背景图片可以是你想要的任何东西。我刚刚在 GIMP 中创建了它。玩吧。此外，如果您不想使用特殊的字体，也可以不使用字体语句。

浪子通过在用户界面测试中拍摄快照来自动截图。可悲的是，在我想做这件事之前，我从未设置过 UI 测试，因为我认为它们超级复杂。如果你以前做过，就和我一起呆在这里。您需要建立一个测试方案来完成这项工作，但是我们会逐步完成，这并不难:

*   在 Xcode 中打开您的项目
*   选择文件/新建/目标/..查找 Cocoa Touch UI 测试包
*   找到新的 Project_UI_Tests 文件夹
*   单击打开 Project_UI_Tests.swift
*   将此粘贴到函数设置中的{}之间

```
let app = XCUIApplication()
setupSnapshot(app)
app.launch() 
```

Enter fullscreen mode Exit fullscreen mode

*   将光标放在函数 testExample 中的{}之间
*   现在，点击底部的红色小按钮，会弹出一个模拟器，你可以浏览你的应用程序，做所有重要的事情(或者不，如果你想掌握它，它甚至可以只是机载的)
*   完成后，再次单击红色按钮
*   现在，你需要检查模拟器自动运行的内容，以确保它是正确的。可能会出现模拟器无法跳出文本字段或无法在选择器中选择选项的情况。
*   当一切正常时，在你想要截图的地方选择几个点，并添加像`snapshot("0Login")`或`snapshot("2ChangeDate")`这样的代码——这个想法是让它们按时间顺序排列(为了你的理智),并带有一个你可以在你的框架文件中使用的键，如果你以后决定制作精美的框架。
*   现在，为您的测试创建一个新的方案:
    *   方案-新方案
    *   选择您的测试
    *   命名它
    *   编辑它以确保它是共享的(有一个小复选框)

### 我们跑吧

所以现在，我们已经设置了项目来使用 fastlane，并且我们已经创建了 UI 测试来截图。我们都准备好了。我们先来试一组截图。注意:Xcode 非常敏感，这可能需要几次尝试，而且，你可能需要再次编辑 UITest 函数，直到所有正确的输入发生。我们会假设它有效，因为最终它会的。

#### 先试截图

*   保存您的项目工作(您现在实际上不需要打开 xcode)
*   导航回终端
*   提交您的所有更改
*   键入`bundle exec fastlane screenshots` -这将运行我们之前在 Fastfile 中设置的截图“通道”。
*   终端会工作一会儿。您可以尝试观察输出的变化，或者去做其他事情。本质上，它将进入您的项目，运行 UITests，并截取屏幕截图。然后，它会将它们组合成一个漂亮的 HTML 文件。
*   在任何地方使用你的截图。...也许去 checkout frameit 让它们更漂亮，更适合 iTunes Connect

#### 现在进行 Beta 测试

*   导航回终端
*   提交您所有的更改
*   类型`bundle exec fastlane beta`
*   终端将工作一段时间(花了我 24 分钟)。去做点别的吧。有人要深蹲吗？
*   当 Testflight 通知你要测试的新应用时，庆祝一下。

就是这样。现在，您有了更好的理解，并且可能可以使用这些文档来做更酷的事情。

### 你的故事很棒，但我需要不同的资源

太酷了。我明白。在我完成了所有的设置并让它在我的机器上运行之后，我开始寻找其他资源。RayWenderlich 上有一个关于使用 fastlane 的[神奇教程。它有点过时，但方法是合理的，如果你不想使用你的应用程序，他们甚至会给你一个假的应用程序来玩。或者，也许你在游戏中遥遥领先，还没有应用程序。你真棒。
如果教程对你没有帮助，请再次尝试文档，或者联系 github、stack overflow 甚至 twitter 上的社区。](https://www.raywenderlich.com/136168/fastlane-tutorial-getting-started-2)