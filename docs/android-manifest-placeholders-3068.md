# Android 清单占位符

> 原文：<https://dev.to/brightdevs/android-manifest-placeholders-3068>

# 它们是什么？

Android 清单占位符允许你将变量放入完全静态的清单中。为什么需要这样的功能？其实要看你的项目。当您有多个具有不同清单配置的[构建变体](https://developer.android.com/studio/build/build-variants.html)时，这可能是最有用的。

# 多个舱单

当然，为每个构建变体配置 Manifest 的最简单方法是在特定于变体的源目录中放置一个单独的`AndroidManifest.xml`文件。例如，假设我们有一个带有[风格维度](https://developer.android.com/studio/build/build-variants.html#flavor-dimensions)的`app`模块，名为`features`，带有两种风格:`paid`和`free`。在 Gradle 文件中，它可能是这样的:

```
android {
    ...
    buildTypes {
        release {...}
        debug {...}
    }

    flavorDimensions "features"

    productFlavors {
        paid {
            dimension "features"
            ...
        }
        free {
            dimension "features"
            ...
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这使我们能够使用不同的源集，包括清单文件。如果我们想要分离`paid`和`free`配置，我们可以在以下项目目录中放置不同的清单:

*   `app/src/paid/AndroidManifest.xml`
*   `app/src/free/AndroidManifest.xml`

我们也可以使用完全合格的构建变体(结合产品风格和构建类型):

*   `app/src/paidDebug/AndroidManifest.xml`
*   `app/src/paidRelease/AndroidManifest.xml`
*   `app/src/freeDebug/AndroidManifest.xml`
*   `app/src/freeRelease/AndroidManifest.xml`

*注#1:源集不会自动创建。在 Android Studio 中创建新文件或目录时，您可以手动或使用**源集**下拉菜单来创建它们。*

*注意#2:如果你想确定如何组织源集，你可以运行 Gradle `sourceSets`任务，例如使用`./gradlew sourceSets`或 Android Studio Gradle 菜单。*

虽然使用多个清单文件提供了最大的灵活性(您几乎可以更改任何内容)，但是维护可能会很麻烦，原因有几个，例如:

*   改变任何东西都需要编辑每个文件，
*   比较多个文件并寻找差异是不方便的。

[![Placeholder](img/f8e18cb091deeb874d339c9ce81e7486.png) img/android-manifest-placeholders/placeholder.png) {:。中央图像}

# 使用占位符

所以我总是努力使用一些变量，而不是使用多个文件。为了在清单中使用一个变量，我们必须在 Gradle 的`manifestPlaceholders`属性中指定它。我们可以在几个地方做到这一点，例如:

*   默认配置

```
android {
    ...
    defaultConfig {
        manifestPlaceholders.screenOrientation = "unspecified"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   产品风味

```
android {
    ...
    flavorDimensions "features"

    productFlavors {
        paid {
            dimension "features"
            manifestPlaceholders.hostName = "www.paid-example.com"
        }
        free {
            dimension "features"
            manifestPlaceholders.hostName = "www.free-example.com"
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   构建类型

```
android {
    ...
    buildTypes {
        release {
            ...
            manifestPlaceholders.screenOrientation = "portrait"
        }
        debug {...}
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意#1: `manifestPlaceholders`对象只是一个`Map<String, Object>`，所以你也可以使用它的其他方法，如`containsKey()`等。*

*注意#2:你也可以通过分配一个像这样的映射一次指定所有的值:`manifestPlaceholders = [...]`*

然后，我们可以在清单中使用变量，只需将变量名放在花括号中，并使用一个美元符号，如下所示:

```
<activity
    android:name=".MyActivity"
    android:screenOrientation="${screenOrientation}" /> 
```

Enter fullscreen mode Exit fullscreen mode

# 申请

我遇到了占位符的一些常见用法，例如:

*   启用/禁用[应用程序组件](https://developer.android.com/guide/components/index.html)和元数据，包括应用程序依赖项附带的组件

```
<service
    android:name=".firebase.FcmIdService"
    android:enabled="${pushNotifications}">
    ...
</service>

<provider
    android:name="android.support.v4.content.FileProvider"
    android:authorities="${fileProvider}"
    ... >
    ...
</provider> 
```

Enter fullscreen mode Exit fullscreen mode

*   在纵向应用中覆盖屏幕方向，以便您可以在调试版本中旋转它，这在查找与生命周期相关的内存泄漏时可能很有用

```
<activity
    android:name=".MyActivity"
    android:screenOrientation="${screenOrientation}" /> 
```

Enter fullscreen mode Exit fullscreen mode

*   使用不同的深层链接配置

```
<intent-filter>
    <action android:name="android.intent.action.VIEW"/>
    <category android:name="android.intent.category.DEFAULT"/>
    <category android:name="android.intent.category.BROWSABLE"/>
    <data android:scheme="app" android:host="${deepLinkHost}" />
</intent-filter> 
```

Enter fullscreen mode Exit fullscreen mode

*   从 React 基于本机的发布版本中移除`SYSTEM_ALERT_WINDOW`权限(虽然这看起来很糟糕；你可以在这里找到最初的问题[(链接)](https://stackoverflow.com/questions/45170025/cant-use-manifest-placeholders-to-remove-a-permission)

```
<uses-permission android:name="${excludeDebugPermissionName}" tools:node="remove" /> 
```

Enter fullscreen mode Exit fullscreen mode

```
buildTypes {
    release {
        ...
        manifestPlaceholders.excludeDebugPermissionName = "android.permission.SYSTEM_ALERT_WINDOW"
    }
    debug {
        ...
        manifestPlaceholders.excludeDebugPermissionName = "fake.name"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是还有更多的可能性，例如，我可以想到一个使用不同启动器活动的应用程序。

你在使用占位符时有什么有趣的经历吗？欢迎在评论中与我分享:-)

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Andrzej Zabost，光明发明
[博客](https://azabost.com/)，[电子邮件](//andrzej.zabost@brightinventions.pl)的安卓开发者