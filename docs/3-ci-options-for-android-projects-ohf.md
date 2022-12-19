# Android 项目的三个持续集成选项

> 原文：<https://dev.to/levimoreira/3-ci-options-for-android-projects-ohf>

大家好:)

自从我们上次谈话以来，我已经插手了一些相当好的事情。其中之一是 Android 项目的持续集成选项。因此，我确定了三个可以用于不同级别的设置难度，使用价格和集成，我想出了一个适合我的选项列表。请注意，我只在 Django 项目中有过 CI 经验，而且我根本不是 CI 专家。

#### 快速提醒:持续集成

当考虑一个开发团队时，持续集成是帮助项目安全发展的伟大原则。这意味着开发人员可以集成他们不断编写的代码，这些代码会定期“检查”错误(通过开发人员自己编写的测试)，有助于及早发现错误，同时让每个人都了解最新情况。通常 CI 实践包括建立一个 CI 服务器，负责检查开发人员编写的代码，以便检测 bug。你可能会问自己这是怎么做到的？这涉及到服务器的设置以及使用该工具的开发人员的心态:

*   我们将代码保存在远程存储库中(Github、Gitlab、Bitbucket...).嗯，每个人都已经这样做了...
*   CI 服务器被配置为“知道”如何构建和测试代码。这通常是一个脚本，模仿我们在本地使用的命令来做同样的事情。例如，在 Android 中，当我们想要运行单元测试时，我们通常会做:`./gradlew test`。因此我们的 CI 服务器“知道”如何运行该命令。
*   CI 服务器还知道如何构建，有时还知道如何部署构建产生的工件(Android 中的 apk)。
*   我们通常将代码提交给 repo 和 CI 服务器，后者知道推送事件，将提取代码并运行一些预定义的步骤来运行和构建代码。
*   我们应该经常推送代码(以保持集成的“连续”部分流动)
*   我们不应该推送未经测试的代码，这意味着我们至少应该在本地运行现有的测试，看看我们的更改是否没有破坏它们。理想情况下，您需要在开发时添加测试套装(TDD，对吗？).
*   我们不应该推送不完整的、半成品的或者没有构建好的代码。

现在有了选择

### 选项 1:圈出 CI

我想说的第一个选项是 Circle CI。由于它与 Github 的良好集成，它被大肆宣传。你可以免费得到一个能够运行 1 个“并行”作业的容器。这对于测试和小型项目来说已经足够了，所以让我们深入研究一下。

您将看到，使用这样的工具，设置非常相似，我们需要将一个配置文件添加到我们项目的基本文件夹中，所有 CI 设置都在该文件中完成。

在 Circle CI 的情况下，该文件称为`./circleci/config.yml`。该结构也在某种程度上类似于其他选项，你可以在这里看到完整的文件。
基本上，该文件告诉 Circle CI 以下内容:

1-定义一个名为“构建”的作业步骤

```
jobs:
  build:
    working_directory: ~/code 
```

Enter fullscreen mode Exit fullscreen mode

2-使用 docker 镜像 circleci/android:api-28-alpha，它包含了运行命令行构建/测试/部署的所有 Android SDK 选项。

```
 docker:
      - image: circleci/android:api-28-alpha 
```

Enter fullscreen mode Exit fullscreen mode

3 -定义一系列步骤，包括使 gradlew 可运行、下载项目依赖项、运行 linters、构建和单元测试。

```
 - run:
         name: Chmod permissions
         command: sudo chmod +x ./gradlew
      - run:
          name: Download Dependencies
          command: ./gradlew androidDependencies
      - run:
          name: Run Unit Tests
          command: ./gradlew test
      - run:
          name: Run Linters
          command: ./gradlew check
      - run:
          name: Build
          command: ./gradlew assemble 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这个配置文件是一个简化版本。我们可以在这个构建中添加许多步骤，包括集成和 UI 测试、打包甚至部署。这只是一个简单的例子。

一旦您添加了配置文件，您需要做的就是进入 Circle CI 页面，添加一个指向您的 Github repo 的新项目。这一部分非常简单，所以这里有一张构建在服务器上执行时的图片:

[![alt](../Images/8e38ceb2759c746c6636b9383f0b7090.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9TT6URJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xsm54jn71vk96glkky9s.png)

整合 Circle CI 后，您可以将其作为一个依赖项添加到您的 PR 工作流中，这样，在您可以合并之前，测试需要通过 CI 服务器。它会像这样显示在你的 PR 中:

[![alt](../Images/e45330f2360bf597eee8bbd7bbe619de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GgGUH-69--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5vfklzx0xmb43ud79v1z.png)

上图是我们下一个选择的一个伟大的赛格威。

### 备选案文 2 : Gitlab

下一个选项是 Gitlab。您可以在他们的站点上托管您的完整项目并使用 CI 系统，或者您可以使用他们与 Github 的集成并仅使用 CI 选项。如果你选择后者(这是我在 Android 上测试过的)，设置包括授权 Gitlab 克隆你的回购，它会添加一个 webhook(就像 Circle CI)来监控原始回购中的推送事件。为了完成我们的设置，我们将添加一个配置文件，就像我们对 Circle CI 所做的那样，但是这次我们的文件名为`.gitlab-ci.yml`。这里是的完整文件。

这个配置文件的灵感来自于这个[帖子](https://medium.com/evenbit/build-and-test-android-with-gitlab-continuous-integration-e51aba6ba53e)中的那个。可以看到和我用的圈 CI 有点像。想法是一样的:
1 -定义两个阶段，构建和测试。另外，告诉 Gitlab 使用 java docker 镜像:

```
image: openjdk:8-jdk

stages:
  - build
  - test 
```

Enter fullscreen mode Exit fullscreen mode

2-`before_script`用于在每个步骤的主要目的之前进行配置。在构建步骤中，我们使用 build.gradle 文件来标识我们需要使用的编译 sdk 版本。我们还定义了一个名为 ANDROID_COMPILE_SDK 的环境变量，它将用于下载 SDK 文件(这可以很容易地添加到 Gitlab 的 CI 配置菜单中的“variables”选项下)。变量 ANDROID_SDK_TOOLS 用于下载其他 SDK 文件。在这个脚本中，我们还设置了一个名为 ANDROID_HOME 的环境变量，它指向我们安装 sdk 工具的文件夹。我们将文件夹添加到容器路径中，这样我们就可以在其他地方运行它的实用程序。最后我们让 gradlew 可以跑了。

```
before_script:
    - export ANDROID_COMPILE_SDK=`egrep '^[[:blank:]]+compileSdkVersion'  app/build.gradle | awk '{print $2}'`
    - echo $ANDROID_SDK_TOOLS
    - echo $ANDROID_COMPILE_SDK
    - wget --quiet --output-document=/tmp/sdk-tools-linux.zip https://dl.google.com/android/repository/sdk-tools-linux-${ANDROID_SDK_TOOLS}.zip
    - unzip /tmp/sdk-tools-linux.zip -d .android
    - export ANDROID_HOME=$PWD/.android
    - export PATH=$PATH:$PWD/.android/platform-tools/
    - echo y | .android/tools/bin/sdkmanager "platforms;android-${ANDROID_COMPILE_SDK}"
    - chmod +x ./gradlew 
```

Enter fullscreen mode Exit fullscreen mode

3 -对于任何分支，除了 master，我会调用命令`./gradlew assembleDebug`。如果推送事件发生在主分支，我们称之为`./gradlew assembleRelease`。

```
 script:
    - ./gradlew assembleDebug

  only:
    - master
  script:
    - ./gradlew assembleRelease 
```

Enter fullscreen mode Exit fullscreen mode

4 -对于测试，如果推送事件发生在任何分支，我们将调用`./gradlew test`。

```
unit_test:
  stage: test

  before_script:
    - export ANDROID_HOME=$PWD/.android
    - export PATH=$PATH:$PWD/.android/platform-tools/

  script:
    - ./gradlew test 
```

Enter fullscreen mode Exit fullscreen mode

请注意，因为我们已经将构建阶段的工件设置为仅在 4 小时后到期，所以测试阶段的`before_script`不需要重新设置 SDK。

### 选项三:詹金斯

Circle CI 和 Gitlab 是“简单”的选择，但这是有代价的。对于完整的专业项目，免费资源可能不够，然后我们需要进入一个付费计划。为此，我提出了第三个选择:詹金斯。

Jenkins 是开源的，它的运行限制是由你托管它的服务器设定的。奥恩，是的。它是“免费”的，因为你不需要支付使用它的许可费，但是你需要在某个地方托管它，而这不是免费的。我一直在使用一个 t2-small EC2 实例，它工作得很好。

我找到的关于这方面的大部分教程都有点过时了，但是基本步骤列在下面:

1 -在你的实例中安装 JDK，这通常是一个简单的命令，如下所示。此外，安装 unzip，因为您稍后会需要它，并安装 git，以便在 CI 步骤中使用 git 命令。

```
sudo apt-get install java-8-openjdk
sudo apt-get install unzip
sudo apt-get install git 
```

Enter fullscreen mode Exit fullscreen mode

2 -安装[詹金斯](https://jenkins.io/doc/book/installing/)。
3 -下载 Android 的命令行工具，你可以在这里 :
查看哪个是最新的

```
sudo -iu jenkins wget https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip 
```

Enter fullscreen mode Exit fullscreen mode

4 -创建一个文件夹来存放 android sdk，并将 sdk 解压缩到其中:

```
sudo -iu jenkins mkdir sdk
sudo -iu jenkins unzip sdk-tools-linux-4333796.zip -d sdk 
```

Enter fullscreen mode Exit fullscreen mode

5 -接受许可:

```
yes |sudo -iu jenkins sdk/tools/bin/sdkmanager --licenses 
```

Enter fullscreen mode Exit fullscreen mode

6 -安装平台工具:

```
sudo -iu jenkins sdk/tools/bin/sdkmanager 'platforms;android-27' 
```

Enter fullscreen mode Exit fullscreen mode

完成这些步骤后，您可以开始在 Jenkins 仪表盘中设置工作。首先，我们需要告诉 Jenkins 我们在哪里安装了 SDK，这可以通过进入 Jenkins 仪表板中的系统配置来完成:

[![alt](../Images/78cf053cf9cbc5a0e46b4ba2c20878b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--09sZSgw4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3pxualvmp42jwzmw9gey.png)

设置 sdk 文件夹后，我们需要创建一组凭证，这样 Jenkins 就可以从我们的存储库中提取代码。你可以进入凭证菜单，在 Jenkins 命名空间下点击添加凭证:
[![alt](../Images/80de12575c24636dfc7397121d89e805.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nBzIMzMi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vz430cafihtk9bhk6pmf.png)

对于用户名，你可以使用你自己的 Github 用户名，密码有点不同。你需要进入你的 Github 账户，在设置->开发者设置选项下选择个人令牌。您需要生成一个令牌，供 jenkins 用作凭证的密码。

设置凭据后，您可以创建作业。进入你的詹金斯仪表板的新项目，并选择自由式项目。在以下屏幕中，确保您设置了:

1- Github 项目

[![alt](../Images/9af0e0f69dd050c25da2dc2ac01fd8a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3mumf2Iu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d9y123ym18d7zz6uwx58.png)

2 - Git repo、凭证和分支来监视推送事件。

[![alt](../Images/aa61a25d3a809fbb18a4a97cb7cdad20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--42xeOp6c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdgx9npzmyfwm6zja9it.png)

3 -构建触发器

构建触发器决定了我们的作业将如何开始运行，对于我们的例子，我们希望检测来自 Github 的推送事件，所以我们选择“Github hook trigger for GITScm polling”。

[![alt](../Images/77e9fa5a6aef1e2a58b800986e3493fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g200cTUd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4o03dco6car0wqsa41zc.png)

要做到这一点，你需要进入你的回购设置，并添加一个新的网络挂钩。只需在有效载荷 URL 选项中填入:`http://<JENKINS_HOST_ADDRESS>/github-webhook/`。

[![alt](../Images/01cb9b0429714d41a0ad2bc4409c5fa2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_O2lZXVr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v22kna5wptxcq9xim3ew.png)

4 -构建步骤

这里我们添加两个步骤(通过点击添加 Gradle 脚本)。选中使用 gradlew wrapper 并添加任务`assembleRelease`和`test`。不要忘记选中复选框以使 gradlew 可运行。

[![alt](../Images/f1f9c16ec46c69bb4294c68c27280126.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dVDWsrjM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c8q0hyyynozcm78z3azu.png)

汪汪。要配置的东西太多了，不是吗？我猜这就是为什么他们想出了蓝色海洋...之后，您的 jenkins 设置就可以接收推送事件并自动构建/测试了。

所以，总结一下:

Circle CI 与 Github 的集成非常棒，从我在文档中看到的来看，它非常通用，具有广泛的选项，可以覆盖大多数 CI 场景。Gitlab 也很好，但是如果你想将它与 Github 集成，不知何故会有开销，感觉不如 Circle CI 自然，但仍然非常实用。我相信如果你用他们托管你的代码，那么它会工作得很好(我一直在 Gitlab 中托管代码的项目中工作，我们几乎无缝地使用 CI 选项)。Jenkins 非常适合定制，它提供了最大的灵活性，但需要一个学习曲线来理解它是如何工作的，并且界面不如其他两个直观。

我猜就是这样:)这些只是我从致力于探索这些工具的少量时间中得出的观点。从这三个方面来说，我只是定期与 Gitlab 合作，所以我可以保证这一点，另外两个，嗯，你已经读过我的意见了...此外，这些并不是唯一的选择，其他著名的选择是 Travis CI。