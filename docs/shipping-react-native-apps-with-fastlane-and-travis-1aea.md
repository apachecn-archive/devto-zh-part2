# 与浪子和特拉维斯一起发布 React 本地应用

> 原文：<https://dev.to/carloscuesta/shipping-react-native-apps-with-fastlane-and-travis-1aea>

#### *原载于 [carloscuesta 的博客](https://carloscuesta.me/blog/react-native-meets-codepush)*

一年前[我写了一篇关于浪子如何帮助我们改善 **React 原生**应用发货流程的帖子](https://carloscuesta.me/blog/shipping-react-native-apps-with-fastlane/)。在那一刻，即使一切都是自动化的，**部署** **依靠**我们中的**一个人**带着**配备的机器**来发射火箭🚀。我们可以通过 **经由 **CI 机器**持续交付**我们的应用程序，从而**轻松地**改进 **处理**。这时，特拉维斯·CI 前来救援！👷🏻‍♂️****

### 流程

在解释问题所在之前，重要的是**理解**我们部署过程的**复杂性**。

简而言之，我们有**两个平台** : *iOS* 🍏，*安卓*🤖每个平台编译两个应用程序:Beta 测试应用程序，也称为**金丝雀**🐤和**生产**🚀一个。

基本上每个站台都会依次经过一条车道，就像这样👇

*   [代码标志设置](#codesignsetup) ✍️
*   [版本管理](#versionmanagement)🔖
*   [原生版本](#nativebuilds)📦
*   [Beta 测试分布图](#betatestingdistribution)🐤
*   [门店分布](#storesdistribution)🚀
*   [来源地图](#sourcemaps)🗺
*   [交流](#communication) 🗣

现在，让我们深入了解部署流程的每一步，以了解我们的工作。

#### 代码标志设置✍️

**签约**原生申请**吓人**😱，特别是当你来自 JavaScript 生态系统的时候。证书、预置描述文件、密钥...在开发团队中使用它们时，你必须非常有条理。

我们通过*采用了 [codesigning.guide 概念。基本上，这个想法是通过一个特定的 **git 库**到**存储**和**在整个开发团队中分发**证书来实现的。我们将 **iOS** 和 **Android** 代码签名文件存储在 GitHub 上的加密私有 git 存储库中。](https://codesigning.guide)*

 *然后，我们在每个部署上的 **CI** 机器**克隆**T4】仓库和**安装**解密后的证书。在 **iOS** 上，CI 会创建一个 [*OS X 钥匙串*](https://en.wikipedia.org/wiki/Keychain_(software)) 来安装证书。

#### 版本管理🔖

**原生构建**和商店**要求**代码**版本凸起**。

每个平台都有自己管理版本和构建号的方式。这两个版本的区别在于,**版本**应该被用作标识新版本的**公共存储编号**,而**版本编号**是一个递增的标识符，它会在每个版本中出现。

**安卓**🤖

*   公开版本号:`versionName`
*   构建编号:`VERSION_CODE`

**iOS** 🍏

*   公开版本号:`CFBundleShortVersionString`
*   构建编号:`CFBundleVersion`和`CURRENT_PROJECT_VERSION`

这些属性存储在`.plist`、`.pbxproj`、`.properties`和`.gradle`文件中。为了自动化和进行版本管理，我们使用[**package . JSON****version**number](https://docs.npmjs.com/files/package.json#version)作为我们**公共版本号**的**来源💯。这允许我们使用 [`npm version` cli 命令](https://docs.npmjs.com/cli/version)来管理凸点。**

#### 原生构建📦

我们需要**提供**两台**机器**来构建和编译我们的本地应用程序。

对于 **iOS** ，我们用 **Xcode** 设置了一个 **macOS 系统**，因为这是编译和签署应用程序的唯一方法。在 **Android** 上，我们提供了一个 **Linux** 系统，以及我们需要的所有 **Android Studio** 、包和工具。

那些机器是由我们的 **CI** 创造的，这意味着每一个构建都在一个新的**新鲜的**和**干净的**环境中运行💻。

#### Beta 测试分布🐤

为了**将**应用**分发给**测试人员**，我们在 **iOS** 上使用 [TestFlight](https://developer.apple.com/testflight/) ，在 **Android** 上使用 [HockeyApp](https://hockeyapp.net) 。我们尝试了 *Google Play 测试版*，但是与 HockeyApp 相比，它在应用推出上太慢了。**

#### 门店配送🚀

为了**将**应用**分发**到**商店**，我们将生产版本上传到 **iOS** 上的 [TestFlight](https://developer.apple.com/testflight/) 和[Android 上的](https://hockeyapp.net)[谷歌 Play 商店](https://hockeyapp.net)。释放由人手动完成。

#### 来源地图🗺

为了获得人类可读的关于崩溃和错误的信息，我们使用一个叫做 [Bugsnag](http://bugsnag.com) 的服务。每次我们部署一个新的构建，我们需要**上传** **调试符号** `.dSYM`和 **sourcemaps** 到 Bugsnag。

#### 沟通🗣

最后，当应用程序部署完毕，我们需要**通知**我们的**测试人员**、**发布经理**和**开发者**，我们有了新版本。我们将 [Slack](https://slack.com/) 与一个向某些频道发送警报的机器人一起使用。

### 问题

每次我们**想要** **到**做一个**释放**，我们不得不**手动开火**🔥**浪子**部署车道。这意味着**人的因素**是需要的。这是一个**耗时的**过程，经常由于代码符号、有偏见的环境、软件更新、本机平台依赖性而失败...

> #### **The machine** should **work** , and **people** should **think**

毫无疑问，我们决定通过**自动化所有事情**来**结束**那些**问题**！

### 解

解决方案是将这个**自动化流程**实施到一个系统中，由 **[连续不断地将](https://en.wikipedia.org/wiki/Continuous_delivery)** 我们的`master`分公司神奇地推送到商店🎉，让经理自由决定何时发布新版本。最后，我们可以忘记一切，快乐地生活！❤️

现在我们将**看一看**我们如何**集成**T4】特拉维斯和*浪子*到**自动化**我们应用的**部署**👏。

#### 浪子

我们有两条路线，一条针对 Android，一条针对 iOS。我稍微简化了一下车道，以便集中解释它的重要部分。首先我们部署 Android 平台，然后是 iOS。

**通道** **接收**来自`package.json`的**版本**号，正如我之前所说，这允许我们通过 npm 进行版本控制。

我们做的**第一件**事就是**撞****公** **版**号和 **build 号**。在 **iOS** 通道上，我们需要`setup_certificates`，将它们保存在钥匙链上，并能够对应用程序进行签名。

之后我们开始`canary`🐤和`production`🚀车道。这两个人就是**打造****原生 app** 的人。

*   `Canary` : Beta 测试建造，运送到*试飞*和*霍克亚普*。
*   `Production`:生产建造，运送到*试飞*和*谷歌 Play 商店*。

然后，我们**上传**所有的**源地图**和**调试**符号**文件**到 *Bugsnag* 。

接下来，我们创建一个 **git 分支**，在那里**版本将通过`commit_and_push_version_bump`通道**提交**。后来，在 iOS 通道上，我们使用`git_flow_merge`通道将创建的 **git 分支**合并到`master`之上。我们需要提交修改，以便在部署的同时维护版本。否则，商店应该抛出一个错误，即上传的版本已经存在！**

最后我们**到达**离开**懈怠**，到**传达**双方**部署**。

**安卓**🤖

```
lane :deployment do |version: version|
  bump_version_number(version: version)
  canary
  production
  sh 'npm run repositories:upload:android'
  commit_and_push_version_bump
  slack_notification(platform: 'Android', version: version)
end 
```

Enter fullscreen mode Exit fullscreen mode

**iOS** 🍏

```
lane :deployment do |version: version|
  setup_certificates
  bump_version_number(version: version)
  canary
  production
  sh 'npm run repositories:upload:ios'
  commit_and_push_version_bump
  git_flow_merge(version: version)
  slack_notification(platform: 'iOS', version: version)
end 
```

Enter fullscreen mode Exit fullscreen mode

因此，这是我们的 git 日志在合并到`master`的分支并进行部署后的样子🙌：

[![GitHub log](../Images/4e8d46fe2d7cd2111ebd2fd14070fd8d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--psuC_TUk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/carloscuesta/image/upload/ghlog.png)

#### 特拉维斯词

我们使用 **[构建阶段](https://docs.travis-ci.com/user/build-stages)** ，在**三个步骤****中依次运行我们的部署流程**。当我们的**测试通过** ✅.时，这允许我们**只在`master`分支上部署**我们的**应用**

 **让我们来看看构建阶段👇

[![travis-build-stages](../Images/7d1b0c0f8da87c0bacb75845ed9cfdd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qY-g1Uzf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/carloscuesta/image/upload/travis-build-stages.png)

每个**建造阶段**都有他**自己的** **预备**和**环境**。例如，`Deploy iOS`运行在装有 Xcode 和 Node.js 的 [macOS 机器](https://docs.travis-ci.com/user/reference/osx/)上，而`Deploy Android`使用装有 JDK、AndroidSDK 和 Node.js 的 [Ubuntu 机器](https://docs.travis-ci.com/user/languages/java/)

**测试阶段**

在**第一阶段**我们执行**棉绒**和**测试套件**。以确保一切按预期运行。如果这里出现故障，我们会自动停止部署。

```
- stage: Test and lint ✅
  language: node_js
  node_js: 8.5.0
  install: yarn
  script: npm run test:lint && npm run test:unit 
```

Enter fullscreen mode Exit fullscreen mode

**安卓阶段**🤖

Android 阶段**创建**一个**提供的**和 [Ubuntu 机器](https://docs.travis-ci.com/user/languages/java/)以及所有需要的软件和依赖。然后我们建造金丝雀🐤和生产🚀应用程序。之后，我们部署它们。大约 15 分钟后⏰我们的安卓应用上市了。👏

```
- stage: Deploy Android 🤖
  if: branch = master AND type = push
  language: android
  jdk: oraclejdk8
  android:
    components:
      - tools
      - platform-tools
      - android-26
      - extra-google-m2repository
      - extra-google-google_play_services
  before_install:
    - nvm install 8.5.0
    - gem install bundler
    - bundle install
  before_script:
    - ./internals/scripts/travis/gitconfig.sh
  install: yarn
  script: npm run deployment:android 
```

Enter fullscreen mode Exit fullscreen mode

**iOS 阶段**🍏

iOS 阶段**创建**一个**供应的** [macOS 机器](https://docs.travis-ci.com/user/reference/osx/)带有 Xcode 和所有需要的依赖项。然后我们建造金丝雀🐤和生产🚀应用程序。之后，我们部署它们。大约 20 分钟后⏰我们的 iOS 应用已经上市。👏

```
- stage: Deploy iOS 🍏
  if: branch = master AND type = push
  language: node_js
  node_js: 8.5.0
  os: osx
  osx_image: xcode9.2
  before_install: bundle install
  before_script:
    - ./internals/scripts/travis/gitconfig.sh
  install: yarn
  script: npm run deployment:ios 
```

Enter fullscreen mode Exit fullscreen mode

### 吸取教训

*   通过**将所有事情**自动化，尽可能避免人为因素！
*   原生生态系统是艰难的，有时有点令人沮丧，你应该接受这一点。这不是我们的专长，因为我们是 JS 开发人员，但是有很多人和文档可以提供帮助。
*   制作**流程**。

有什么问题吗？[在 twitter @crloscuesta 上为我呐喊](https://twitter.com/crloscuesta)***