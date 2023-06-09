# 如何在 Ubuntu 上安装 electrode-native 进行 Android 开发

> 原文：<https://dev.to/jadolg/how-to-install-electrode-native-on-ubuntu-to-develop-for-android-1m2a>

**注意:**这些说明已经过时，可能不再是正确的方法。我不再使用这项技术，而且近期也不打算这么做，因此这篇文章不会更新。

嗨@大家好。几天前，我开始尝试让 [electrode-native](http://www.electrode.io/site/native.html) 启动并运行，由于我未能按照他们的入门指南这样做，我决定制作自己的并与想走相同道路的开发人员分享。这篇文章只是我在 Kubuntu 17.10 操作系统环境下实现它的一系列步骤。这就是我所做的一切。

## 安装 gnome-终端

出于某种原因，电极原生依赖于 gnome 终端，所以如果你没有运行基于 Gnome 的系统，那么`apt-get install gnome-terminal`也是如此。

## 安装 git

`apt-get install git`

## 安装 Java 开发包

`apt-get install openjdk-8-jdk`

## 获取 Android SDK 并安装

1.  下载最新版本的 [AndroidStudio](https://developer.android.com/studio/index.html) 并安装 SDK
2.  导出变量 **ANDROID_HOME** 和 **ANDROID_SDK** 指向 Android SDK 目录
3.  将*工具*和*平台工具*文件夹添加到**路径**
4.  下载 Android 系统映像来运行您的应用程序，或者只是使用您的 Android 手机来测试

## 安装 Gradle

1.  下载最新的 [Gradle](https://gradle.org/releases) 版本
2.  解压到你系统的某个地方

```
mkdir /opt/gradle
unzip -d /opt/gradle gradle-4.6-bin.zip 
```

1.  将 **GRADLE_HOME** 导出到您提取的文件夹中
2.  将 **GRADLE_HOME/bin** 添加到**路径**
3.  **注意:**电极可能会下载自己的 Gradle 版本

## 安装 nodejs 和 npm

```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install -y npm 
```

## 安装电极-原生

1.  从*NPM*T0】安装*电极-原生*
2.  安装纱线`npm install -g yarn`
3.  首次运行 **ern** 完成安装
4.  可能需要安装 react-native 工具`npm install -g create-react-native-app`

## 启动迷你 app

要开始构建您的第一个迷你应用程序，请运行`ern create-miniapp MyGreatApp`并在它完成后运行`ern run-android`来启动应用程序。您可以在模拟器或物理设备上运行。

## 启用重装

您正在使用连接到计算机的设备工作吗？

1.  确保亚行的准入。当您运行`adb devices`时，您的设备应该显示在列表中，如下所示:

```
~$ adb devices
List of devices attached
b91feec4        device 
```

如果您在列表中看不到您的设备，或者它显示除设备之外的任何内容，请尝试断开连接并再次连接，并检查您是否激活了 USB 调试。

1.  运行从本地主机到设备的隧道，如下所示:`adb reverse tcp:8081 tcp:8081`

## 烦恼？

### 格雷尔失败了

```
An error occurred: Command failed: ./gradlew lib:uploadArchives
Exception in thread "main" java.lang.RuntimeException: java.util.zip.ZipException: error in opening zip file
        at org.gradle.wrapper.ExclusiveFileAccessManager.access(ExclusiveFileAccessManager.java:78)
        at org.gradle.wrapper.Install.createDist(Install.java:44)
        at org.gradle.wrapper.WrapperExecutor.execute(WrapperExecutor.java:126)
        at org.gradle.wrapper.GradleWrapperMain.main(GradleWrapperMain.java:58)
Caused by: java.util.zip.ZipException: error in opening zip file
        at java.util.zip.ZipFile.open(Native Method)
        at java.util.zip.ZipFile.<init>(ZipFile.java:225)
        at java.util.zip.ZipFile.<init>(ZipFile.java:155)
        at java.util.zip.ZipFile.<init>(ZipFile.java:169)
        at org.gradle.wrapper.Install.unzip(Install.java:157)
        at org.gradle.wrapper.Install.access$400(Install.java:26)
        at org.gradle.wrapper.Install$1.call(Install.java:67)
        at org.gradle.wrapper.Install$1.call(Install.java:44)
        at org.gradle.wrapper.ExclusiveFileAccessManager.access(ExclusiveFileAccessManager.java:65)
        ... 3 more 
```

解决方案:可能是 Gradle 下载不正确。删除`/home/$USER/.gradle/wrapper/dists/gradle-4.1-all`并尝试再次运行项目。

### 还有其他与 Gradle 相关的问题吗？

试着删除你的 Gradle 缓存(通常在/home/$USER/。gradle)并试图再次运行

我要感谢 Benoit Lemaire 在我和 Gradle 的问题上帮了我。
非常感谢您的阅读。我希望你喜欢这篇文章，并找到帮助。