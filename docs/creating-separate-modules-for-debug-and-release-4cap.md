# 为调试和发布创建单独的模块

> 原文：<https://dev.to/wajahatkarim/creating-separate-modules-for-debug-and-release-4cap>

当谈到用于 Android 的调试工具和库时，如[斯特索](http://facebook.github.io/stetho/)或[查克](https://github.com/jgilfelt/chuck)或杰克沃顿的伟大[泄漏金丝雀](https://github.com/square/leakcanary)，几乎有单独的模块用于`debug`和`release`构建。例如，当您在应用程序中添加*泄漏金丝雀*时，您将会添加如下依赖项:

```
 debugImplementation 'com.squareup.leakcanary:leakcanary-android:1.5.4'
  releaseImplementation 'com.squareup.leakcanary:leakcanary-android-no-op:1.5.4' 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`debug`和`release`有两个独立的模块或 AAR。通常将调试和检查工具的`release`变体称为*无操作*或*无操作*。这个`no-op`模块的目的是在代码中什么也不做。它只是一个空的`debug`模块包装器，以避免编译时错误，比如`Unresolved method name`之类的。

几天前，我正在为 [Hyperion](https://github.com/willowtreeapps/Hyperion-Android) 开发一个调试工具插件，名为 [DBFlow Manager Hyperion](https://github.com/wajahatkarim3/DBFlowManager-Hyperion-Plugin) 插件。我还不知道`no-op`，我只是创建了一个模块，它完成了 DBFlow 数据库检查的所有工作，并且与 Hyperion 配合得非常好。我使用`debugImplementation`将它添加到应用程序中，一切都很好，直到我为应用程序制作了一个`release` APK。

我现在无法编辑一个 APK。我得到的错误是`Unresolved object: DBFlowManager_Hyperion_Constants`。您可以在[这里](https://github.com/wajahatkarim3/DBFlowManager-Hyperion-Plugin/issues/4)跟踪整个问题(已修复和已关闭)。显然，应用程序在代码中使用了`DBFlowManager_Hyperion_Constants`，这个类只对`debug`版本可用，因为我们只包含了用于调试的插件。但是，因为代码使用了引用，所以`release` APK 找不到类引用，所以失败了。

经过简单的谷歌搜索，我设法了解了这一切是如何运作的。我创建了另一个`no-op`模块。你所要做的就是添加空的类和方法，然后简单地编译它。现在，当编译 apk 时，它会找到所有的引用，但是代码什么也不做。这只是一个占位符。

例如，`debug`模块中的`DBFlowManagerPlugin`类是这样的:

```
public class DBFlowManagerPlugin extends Plugin {

    @Nullable
    @Override
    public PluginModule createPluginModule() {
        return new DBFlowManagerModule(); // This is the functionality class
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

而`release`和`no-op`模块中的同一个类是空的，如下所示:

```
public class DBFlowManagerPlugin {

    //@Override
    public Object createPluginModule() {
        return null; // This is returning null. 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，只有类名和方法名是相同的。甚至方法的返回类型也更改为`Object`。都是一个空的无所事事的模块。

### 参考文献

[https://medium . com/@ orhanobut/no-op-versions-for-dev-tools-b0a 865934398](https://medium.com/@orhanobut/no-op-versions-for-dev-tools-b0a865934398)

[https://xrubio . com/2016/10/disabiling-removed-code-on-release-builds/](https://xrubio.com/2016/10/disabling-removing-code-on-release-builds/)