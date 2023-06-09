# 在微软应用中心签署 React 原生 Android APKs

> 原文：<https://dev.to/fabrik42/signing-react-native-android-apks-on-microsoft-app-center-33f6>

**TL；**博士，我希望我的 APK 签约如何进行:

*   存储库中没有凭据/密钥库。
*   作为一般构建过程的一部分，使用 keystore(未签入版本控制)和使用**环境变量**的配置在本地构建并签名它。
*   使用他们的[分支配置](https://docs.microsoft.com/en-us/appcenter/build/android/code-signing#setting-up-code-signing)在 **MS App Center** 上构建它，用于密钥库上传和配置。

我按照官方的 React 本机指令创建了一个 keystore 文件。

然而，我不想在我的主目录中管理一个全局 gradle 配置，而是使用普通的*环境变量*。

#### 会发生什么？

将敏感数据上传到存储库是不可能的，所以唯一的可能是使用[分支配置](https://docs.microsoft.com/en-us/appcenter/build/android/code-signing#setting-up-code-signing)的上传特性。

该设置将使您能够使用

`./gradlew assembleRelease`在本地构建签名的 apk。

如果没有在环境变量中设置密钥库路径，它将不会尝试对 APK 进行签名，而是打印一条警告。这一点很重要，因为在构建之后，MS App Center 将在第二步中使用您在分支配置中设置的密钥库和凭据对 APK 进行签名。

我真的没有得到描述如何在 App Center 上签署 APK 的不同方式的文档，我花了一段时间才弄明白这是如何工作的。

#### **设置环境变量**

假设您的密钥库文件位于 React 本地项目中:

`android/app/myapp-dev.keystore`

您需要设置以下环境变量:

```
ORG_GRADLE_PROJECT_MYAPP_RELEASE_STORE_FILE=myapp-dev.keystore
ORG_GRADLE_PROJECT_MYAPP_RELEASE_KEY_ALIAS=myapp-alias
ORG_GRADLE_PROJECT_MYAPP_RELEASE_STORE_PASSWORD=your-password
ORG_GRADLE_PROJECT_MYAPP_RELEASE_KEY_PASSWORD=your-password 
```

我通常用 direnv 来做这个。

`ORG_GRADLE_PROJECT_`是一个前缀，它将告诉 grade 将这些环境变量作为属性包括在内，该前缀将在此过程中被去除。

#### 配置梯度

编辑`android/app/build.gradle`以包含签名，如下所示:

```
signingConfigs {
    release {
      if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
        storeFile rootProject.file("app/" + project.findProperty('MYAPP_RELEASE_STORE_FILE') ?: "ANDROID_STORE_FILE_NOT_SET")
        storePassword project.findProperty('MYAPP_RELEASE_STORE_PASSWORD') ?: "ANDROID_STORE_PASSWORD_NOT_SET"
        keyAlias project.findProperty('MYAPP_RELEASE_KEY_ALIAS') ?: "ANDROID_KEY_ALIAS_NOT_SET"
        keyPassword project.findProperty('MYAPP_RELEASE_KEY_PASSWORD') ?: "ANDROID_KEY_PASSWORD_NOT_SET"
      }
    }
} 
```

以及发布构建类型中的调用:

```
buildTypes {
    release {
        //...

        if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
          signingConfig signingConfigs.release
        } else {
          println '-------------------------------------------------'
          println 'The MYAPP_RELEASE_STORE_FILE property was not set!'
          println 'This release will not be signed by gradle!'
          println '-------------------------------------------------'
        }
    }
} 
```

#### 配置应用中心

[![](img/99c52e6791ae98640355f61c8b4e7dbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--28pbZKBK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZ7uXMRe4vh4pq2FCV1P7Nw.png) 

<figcaption>上传您的密钥库并存储凭证</figcaption>

#### 就是这样！

[![](img/e1446f8b71e75bc9ba008dd2f10157d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z9k39fKP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/842/1%2A4q8GT3lWSbJnTnWUuj57zA.png) 

<figcaption>终于打造成功了！</figcaption>

非常感谢:[古斯塔夫](https://twitter.com/gkarlsson86)，[穆尼布](https://twitter.com/muneikh)和微软技术支持部门的友好技术支持，感谢他们的提示和耐心！🙏