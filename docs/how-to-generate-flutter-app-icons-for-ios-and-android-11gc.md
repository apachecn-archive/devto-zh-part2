# 如何生成 iOS 和 Android 的 Flutter app 图标

> 原文：<https://dev.to/rkowase/how-to-generate-flutter-app-icons-for-ios-and-android-11gc>

## TL；速度三角形定位法(dead reckoning)

使用`flutter_launcher_icons`命令行工具。

## 环境

*   颤动 0.1.5
*   dart 2 . 0 . 0-开发版 28.0
*   颤振 _ 启动器 _ 图标 0.2.0

## 如何创建

### 1。添加`flutter_launcher_icons`插件

将`flutter_launcher_icons`添加到`pubspec.yaml`

```
dev_dependencies: 
  flutter_test:
    sdk: flutter

  flutter_launcher_icons: "^0.2.0"

flutter_icons:
  image_path: "icon/icon.png" 
  android: true
  ios: true 
```

Enter fullscreen mode Exit fullscreen mode

### 2。准备应用程序图标

为指定路径准备一个应用程序图标。
例如`icon/icon.png`

### 3。创建应用程序图标

在终端上执行。

```
$ flutter pub get
$ flutter pub pub run flutter_launcher_icons:main 
```

Enter fullscreen mode Exit fullscreen mode

## 文件列表

这些是命令执行后的文件列表。

```
+1 -1 M android/app/src/main/AndroidManifest.xml
+0 -0 M android/app/src/main/res/mipmap-hdpi/ic_launcher.png
+0 -0 M android/app/src/main/res/mipmap-mdpi/ic_launcher.png
+0 -0 M android/app/src/main/res/mipmap-xhdpi/ic_launcher.png
+0 -0 M android/app/src/main/res/mipmap-xxhdpi/ic_launcher.png
+0 -0 M android/app/src/main/res/mipmap-xxxhdpi/ic_launcher.png
+0 -0 A icon/ic_launcher.png
+71 -0 A ios/Podfile
+1 -1 M ios/Runner.xcodeproj/project.pbxproj
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-20x20@1x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-20x20@2x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-20x20@3x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-29x29@1x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-29x29@2x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-29x29@3x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-40x40@1x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-40x40@2x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-40x40@3x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-60x60@1x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-60x60@2x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-60x60@3x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-76x76@1x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-76x76@2x.png
+0 -0 A ios/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon-83.5x83.5@2x.png
+56 -0 M pubspec.lock
+5 -0 M pubspec.yaml 
```

Enter fullscreen mode Exit fullscreen mode

## 链接

颤振 _ 发射器 _ 图标|飞镖包
[https://pub.dartlang.org/packages/flutter_launcher_icons](https://pub.dartlang.org/packages/flutter_launcher_icons)

如何更改 Flutter 上的应用启动器图标？-堆栈溢出
[https://Stack Overflow . com/questions/43928702/how-to-change-the-application-launcher-icon-on-flutter](https://stackoverflow.com/questions/43928702/how-to-change-the-application-launcher-icon-on-flutter)