# 简化用户体验，为您的 Android 应用评分

> 原文：<https://dev.to/varunbarad/simplifying-user-experience-for-rating-your-android-app-jo9>

谁不希望自己的用户在 Play Store 上为自己的应用留下评分呢？但它并不是用户心目中的头等大事(除非他们真的被你的应用惹恼了)。即使他们想给你的应用评分，对他们来说这也是一个相当麻烦的过程:

1.  打开游戏商店(暂时放弃他们正在做的事情)
2.  搜索您的应用程序
3.  在一堆克隆体中找到真正属于你的那个([它是其他克隆体的实际度量单位](https://en.wiktionary.org/wiki/buttload)
4.  给你的应用评分

## 简化用户流程

你可能已经在许多应用程序中看到，它们提供了一个按钮或类似的东西，将它们直接重定向到特定应用程序的 Play Store 列表。这缩短了上述流程中的前三个步骤，为用户提供了一个更短的应用评分流程。

提供这样一个选项主要需要开发者的两个步骤:

1.  向用户显示一个选项来评价应用程序(显示按钮，对话框或其他东西)
2.  点击上述选项，启动该应用的播放商店列表

## 1。向用户显示“自我评价”选项

有许多选项可以向用户显示对你的应用程序进行评级的选项。为此，我将使用一个简单的按钮。

```
<Button
    android:id="@+id/button_rateMe"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Rate Me!"
    android:onClick="rateApp" /> 
```

您还有许多其他选择，例如:

*   对话
*   菜单项
*   应用程序“关于”屏幕中的浮动操作按钮(FAB)

## 2。推出 Play Store 列表

现在，当用户点击我们上面显示的任何内容时，我们将启动应用程序的商店列表。继续上面的例子，在我的活动中有下面的代码，上面的按钮显示在其中。

```
// Java
public void rateApp(View rateMeButton) {
    String packageName = this.getPackageName();
    String playStoreAppUri = "market://details?id=" + packageName;
    String playStoreSiteUri = "https://play.google.com/store/apps/details?id=" + packageName;

    try {
        Intent playStoreAppIntent = new Intent(Intent.ACTION_VIEW, Uri.parse(playStoreAppUri));
        startActivity(playStoreAppIntent);
    } catch (ActivityNotFoundException e) {
        Intent playStoreBrowserIntent = new Intent(Intent.ACTION_VIEW, Uri.parse(playStoreSiteUri));
        startActivity(playStoreBrowserIntent);
    }
} 
```

```
// Kotlin
public fun rateApp(rateMeButton: View): Unit {
    val packageName = this.packageName
    val playStoreAppUri = "market://details?id=$packageName"
    val playStoreSiteUri = "https://play.google.com/store/apps/details?id=$packageName"

    try {
        val playStoreAppIntent = Intent(Intent.ACTION_VIEW, Uri.parse(playStoreAppUri))
        startActivity(playStoreAppIntent)
    } catch (e: ActivityNotFoundException) {
        val playStoreBrowserIntent = Intent(Intent.ACTION_VIEW, Uri.parse(playStoreSiteUri))
        startActivity(playStoreBrowserIntent)
    }
} 
```

让我解释一下上面的代码是做什么的。

如果你曾经关注过 Play Store 上任何应用程序的商店列表的 URL，那么你会注意到应用程序的包名被用作它在商店上的唯一标识符。结构是这样的:

`https://play.google.com/store/apps/details?id=<package-name-of-your-app>`

URI 的 Play Store 应用程序也遵循类似的结构:

`market://details?id=<package-name-of-your-app>`

因此，我们首先尝试在设备上启动 Play Store 应用程序，使用应用程序的 URI(即`market://...`应用程序)。如果设备上有 Play Store 应用程序，则会启动该应用程序，并打开包含我们的应用程序列表的页面。

如果由于某些条件，Play Store 应用程序不存在(这可能是一些定制 rom 的情况)，那么`startActivity`将抛出`ActivityNotFoundException`异常，然后我们回退到使用浏览器在 Play Store 的 web 版本上打开我们的应用程序列表。

### 这就是所有的乡亲

如果你知道更好的方法，[联系我](https://varunbarad.com/contact)或者发推特给我 [@varun_barad](https://twitter.com/varun_barad) 。你甚至可以把你想知道的任何其他话题的想法发给我。