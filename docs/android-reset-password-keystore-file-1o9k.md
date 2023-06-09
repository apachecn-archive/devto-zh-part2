# Android:重置密码密钥库文件

> 原文：<https://dev.to/ptkdev/android-reset-password-keystore-file-1o9k>

**解决方案 2018** :如果您丢失密码或 jks 文件，请使用新的密钥库文件对 app 进行签名。

**1)使用命令行(不是 android studio 构建菜单)创建新的 keystore.jks 文件**

```
keytool -genkeypair -alias upload -keyalg RSA -keysize 2048 -validity 9125 -keystore keystore.jks 
```

Enter fullscreen mode Exit fullscreen mode

Windows 示例:
`"C:\Program Files\Android\Android Studio\jre\bin\keytool.exe" -genkeypair -alias upload -keyalg RSA -keysize 2048 -validity 9125 -keystore "C:\keystore_new.jks"`

**2)从新密钥库生成一个. pem 文件**

```
keytool -export -rfc -alias upload -file upload_certificate.pem -keystore keystore.jks 
```

Enter fullscreen mode Exit fullscreen mode

Windows 示例:
`"C:\Program Files\Android\Android Studio\jre\bin\keytool.exe" -export -rfc -alias upload -file "C:\upload_cert.pem" -keystore "C:\keystore_new.jks"`

**3)使用此支持表，设置“密钥库问题”并以附件添加。pem 文件:**[https://support . Google . com/Google play/Android-developer/contact/other bugs](https://support.google.com/googleplay/android-developer/contact/otherbugs)

4) **12-48h 你新的密钥库被启用。使用与新密钥库:D 签署的新 apk 更新 playstore 上的应用程序**