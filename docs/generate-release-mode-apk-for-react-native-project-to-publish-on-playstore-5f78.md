# React Native Build APK 为 React-Native 项目生成发布模式 APK 以在 PlayStore 上发布

> 原文：<https://dev.to/zilurrane/generate-release-mode-apk-for-react-native-project-to-publish-on-playstore-5f78>

大家好！我希望你一切都好。

前几天，我和我的朋友在比较研究 ionic3 和 react-native。在此期间，我们努力为 React-Native 项目生成发布模式 APK。

最后，我们遇到了以下解决方案:

#### 创建并复制一个密钥库文件到 android/app

```
keytool -genkey -v -keystore mykeystore.keystore -alias mykeyalias -keyalg RSA -keysize 2048 -validity 10000 
```

#### 在 android/gradle.properties 中设置你的 gradle 变量

```
MYAPP_RELEASE_STORE_FILE=mykeystore.keystore
MYAPP_RELEASE_KEY_ALIAS=mykeyalias
MYAPP_RELEASE_STORE_PASSWORD=*****
MYAPP_RELEASE_KEY_PASSWORD=***** 
```

#### 添加签名配置到 android/app/build.gradle

```
android {
signingConfigs {
release {
storeFile file(MYAPP_RELEASE_STORE_FILE)
storePassword MYAPP_RELEASE_STORE_PASSWORD
keyAlias MYAPP_RELEASE_KEY_ALIAS
keyPassword MYAPP_RELEASE_KEY_PASSWORD
}
}
buildTypes {
release {
signingConfig signingConfigs.release
}
}
} 
```

#### 生成你的释放 APK:

```
cd android && ./gradlew assembleRelease 
```

您的 APK 将在:*Android/app/build/outputs/apk/app-release . apk*生成

特别感谢 Tyler Buchea:[http://blog . Tyler Buchea . com/react-native-publishing-an-Android-app/](http://blog.tylerbuchea.com/react-native-publishing-an-android-app/)