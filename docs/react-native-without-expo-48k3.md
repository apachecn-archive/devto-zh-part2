# 反应本土而不世博

> 原文：<https://dev.to/fcpauldiaz/react-native-without-expo-48k3>

# [T1】简介](#intro)

我拿了一个很久以前开始的旧的 react native 项目来最终完成它。我用`create-react-native-app`创建了一个全新的 react 原生项目。我发现现在 Expo 是创建默认应用程序时的默认选项。我做了一些改变，集成了 [NativeBase](https://docs.nativebase.io) 和 Redux(使用一些样板文件),我想测试一下编译过程。我发现现在 Expo 可以在他们的云中编译你的代码，只需下载编译好的文件就可以在手机上测试了。我认为这是伟大的，直到我看到文件大小:25 mb😱我了解到 iOS 大约是 30 mb。我简直不敢相信。在阅读了一些文档后，Expo 声称它们包括多个库，因此当需要新的库时，它已经在用户的手机中，只有 JS 代码被推送到云上。我负担不起，它对我正在做的事情来说有太多的空间，所以我决定我需要分离🤷。

# ExpoKit

有两种分离方式:ExpoKit 和 React Native。所以，我首先测试了 ExpoKit。为了测试它，我复制了文件夹，因为你不能在分离它之后返回。该命令创建了一个 android 和 ios 文件夹。我不知道 ios，所以我只测试了 Android，所以我创建了调试 APK，大小为 30mb😱甚至比世博云编译还大。因此，经过多次优化，包括`minify`、`shrink`、`separateCPUbuilds`、`proGuard`，我删除了其中的大部分依赖项，只把它减少到 15 mb😕。这花了我两天时间，我有很多错误，因为我的 java 版本，我不得不升级到最新的版本，增加了一些库，我感到失望。

# 反应原生

我没有放弃，又创建了一个文件夹，把 app 弹出来纯 React Native。起初，我在 Gradle 上遇到了同样的问题，我不得不更新它，清理它并重新构建它。世博会的部分不再起作用了。我有一个闪屏，应用程序图标和字体/图标与博览会不兼容，所以这将是更多的工作。我让闪屏挂起，并修复字体/图标安装[反应-本地-图标](https://github.com/oblador/react-native-vector-icons)。在那之后，我编写了《APK 》,你猜怎么着？仅启用`proGuard`选项时为 7.5 mb🔥。用`SeparteCPUBuilds`可以减的更多。

# 结论

Expo 已经集成了非常酷(但不一定)的 API，Mac 不需要编译到 iOS，可以更新 JS 代码，无需从商店下载 app 但在我看来，尺寸太大的缺点。我还是要探索 iOS 部分。