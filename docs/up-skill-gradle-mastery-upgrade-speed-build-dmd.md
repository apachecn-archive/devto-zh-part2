# 升级技能等级掌握和升级速度建设

> 原文：<https://dev.to/ookbee/up-skill-gradle-mastery-upgrade-speed-build-dmd>

在这几年的工作中，我们的天衣无缝地成长为一朵蘑菇，这使得构建代码持续了很长的时间。越多的人和广告人在一起就越好。library。

//插入图像如此长的建筑

开什么玩笑这是巴克比克但数字还在休眠状态

build time 的问题是浪费资源，所以我们想出了一个解决方案

> 1.移除未使用的资源
> 
> 2.修复最小 SDK，修复资源并禁用多 CPU 构建
> 
> 3 级高速缓存
> 
> 4.瞬间奔跑
> 
> 5.修复警告注释并删除未使用的导入类

#### **#未使用资源**

不确定资源删除了什么？什么文件不需要删除，就把它删除，APK size，巨大的 install，浪费时间，没人用任何东西删除，扔掉它，走吧，有点提醒

或者让 Android Studio 帮个忙，Refector -> Remove 不依赖资源。

#### **#** 修复最小 SDK，修复资源并禁用多 CPU 构建

这是一个很酷的点子，因为它不用去扫那些旧的 SDK，给我们的应用程式，但是要小心一些方法的问题，它是有效的。

debugFlavor，release，alpha，internal，或者你可以被命名为你的 you，调情，tester

例〔t0〕字

```
debugFlavor{
 minSdkVersion 27
 applicationId 'com.ookbee.joyapp.android'
 targetSdkVersion 27
 resConfigs (“en”, “xxhdpi”)
} 
```

将这个放在 App 的 Gradle 设置为 min SDK 的{}构建类型中。它等于我们要构建的机器，并且把它设置为 en 与 DPI xxhdpi 单独连接的构建。

> 其他的 config，搜索，自己找，有人教得很满

但问题还没有解决。人们把代码分为模块化的 ProductFlavor。它会被很多人跟踪，或者在 net 上做很多 ProductFlavor。有人告诉它，它会让构建变得很慢。

没关系，我们有另一种方法，不需要产品闪存盘，把构建类型分开，然后编写一个 Gradle 的可变量，而不是像这样。
230。

```
def computeMinSdkVersionGloble() {
 return project.hasProperty('devBuild') ? project.ext.currentMinSDK : 16
}

allprojects {
 project.ext{
 currentMinSDK = 24
 flavorGlobleMinSDK = computeMinSdkVersionGloble()
 }
} 
```

把它建在建筑里项目本身的 gradle

根据代码，我们创建了一个名为 computeMinSdkVersionGloble()的函数，它基于“devBuild”属性，如果它包含，它将是一个 devbuild。

app 部件->构建。格雷德让它这样写 t0㎡

```
defaultConfig {
 ...
 minSdkVersion project.ext.flavorGlobleMinSDK
 targetSdkVersion 27
 ...
 } 
```

是 Gradle 本身，它可以生成翻译，也可以编写功能，也可以编写一个类

#### #缓存等级

把这个放在格劳里.项目的属性〔t0〕г

```
org.gradle.deamon=true
org.gradle.parallel=true
org.gradle.caching=true 
```

像以前一样，其他人去读，拿其他的博客，写得相当多。

#### #瞬间运行

这是个很好的版本

#### #修复警告注释并移除未使用的导入类

如果您希望将来使用，这些功能属于源代码管理，不再使用代码，或者不使用的类导入器，comment or ununsed function commment

或者右键单击项目->选项导入

在完成下面的命令之前，先看看它能不能知道它运行了多长时间[t0 }任务。

```
./gradlew assembleDebug --profile 
```

我们认为这个问题相当大。假设我们一个人做，所以把构建时间从 4 分钟缩短到 1 分钟。你可以按构建 10 次。你节省了 30 分钟。

> 最后，找个时间来优化我们爱的项目吧。

呃，但有些人说没时间工作，事实上是赶不上了。是的，Developer 的工作对我来说，我想你是对的，然后我就一个人坐了过来。