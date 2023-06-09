# 在刚刚安装了 MacOS Mojave 和 Xcode 10 之后，从 react-native ~0.56 升级到 0.57.1 和 Babel v7

> 原文：<https://dev.to/feargswalsh92/upgrading-from-react-native-056-to-0571-and-babel-v7-after-just-installing-macos-mojave-and-xcode-10--3o0l>

我用来安装它的命令序列。

更新至 MacOS Mojave

`rm-rf node_modules`

`npm install`

`react-native-git-upgrade`

得到了关于巴贝尔热重装机的错误，但忽略了它们。

冉`react-native run-ios`

react-native cli 出现构建错误

在 Xcode 中打开项目，更新了共享工作区的构建工具，这些工具仍然是最新的。

已清理项目

找到了这个

[https://stack overflow . com/questions/52452472/xcode-10-error-multiple-commands-produce-react-native/52452590](https://stackoverflow.com/questions/52452472/xcode-10-error-multiple-commands-produce-react-native/52452590)

这让我想到了这个

[https://stack overflow . com/questions/13751147/remove-or-uninstall-library-previous-added-cocoa pods](https://stackoverflow.com/questions/13751147/remove-or-uninstall-library-previously-added-cocoapods)

当我运行`pod install`引用 BoringSSL 时，它给了我错误。

决定改变轨道，因为我讨厌豆荚和更新巴别塔代替。

那很简单，我只是跟着这个走。

[https://babeljs.io/blog/2018/08/27/7.0.0#babel-upgrade](https://babeljs.io/blog/2018/08/27/7.0.0#babel-upgrade)

我在全球范围内安装了它，如果我可以再做一次，我不会这样做，没有理由，只是看起来它可能会引起问题。

App 最终在一点摩擦后运行，手动启动打包程序，但后来我得到了一些版本不匹配的错误。

运行`npm cache verify`并删除模拟器上的所有内容和设置以避开这些问题，但意识到我一直都有构建错误，所以我郁闷地跋涉回到

cd `./ios`

已意识到 BoringSSL 错误是由 Github 上的此文件更改名称引起的。

[https://github . com/grpc/grpc/blob/master/src/objective-c/boring SSL-grpc . pod spec](https://github.com/grpc/grpc/blob/master/src/objective-c/BoringSSL-GRPC.podspec)

在给了我一个错误之后，我也把 pod BoringSSL 的名字改成了 BoringSSL-GRPC。

后来似乎成功了。

杀死仍在运行的打包程序，从模拟器中删除应用程序并运行:

`react-native run-ios`

构建成功，一个短暂的喜悦瞬间，立即带着可爱的 post 构建成功错误回到现实。

一堆缓存清理和擦除所有内容和设置类型的东西得到修复。

安卓应该是小菜一碟吧？

可根据要求提供终端日志，后果自负。