# Kotlin 如何让编辑你的 Gradle 构建不那么令人沮丧

> 原文：<https://dev.to/jmfayard/how-kotlin-makes-editing-your-gradle-build-less-frustrating-232l>

朋友们好！我写这篇文章是因为你们与 Gradle 和一个 JetBrains IDEs(或 Android Studio)一起工作，但并不完全满意。

我想让你体验一下在你的 Gradle 建造中加入一点 Kotlin 是如何让它不那么令人沮丧的。

在此期间，我将介绍一种更好的方法来处理我的新项目的依赖关系管理:

## ![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png)[【jmfaard】](https://github.com/jmfayard)/[刷新版](https://github.com/jmfayard/refreshVersions)

### 生命太短暂，没有时间去谷歌依赖和版本

<article class="markdown-body entry-content container-lg" itemprop="text">

# 什么是 refreshVersions？

**refreshVersions** 帮助**grade**用户完成**繁琐的手工工作**通常涉及添加和更新**依赖项及其版本**。

[![](../Images/400e1cee45c783477900fdcdb386ff8d.png)T2】](http://www.youtube.com/watch?v=VhYERonB8co "Gradle refreshVersions")

## 证明文件

*   [从这里开始](https://jmfayard.github.io/refreshVersions/)
*   [设置刷新版本](https://jmfayard.github.io/refreshVersions/setup/)
*   [迁移您的项目](https://jmfayard.github.io/refreshVersions/migrate/)
*   [查找可用的依赖关系更新](https://jmfayard.github.io/refreshVersions/update-dependencies/)
*   [添加依赖关系](https://jmfayard.github.io/refreshVersions/add-dependencies/)
*   [探索内置依赖符号](https://jmfayard.github.io/refreshVersions/dependency-notations/)
*   [计划刷新版本机器人](https://jmfayard.github.io/refreshVersions/refreshversions-bot/)
*   [使用 buildSrc](https://jmfayard.github.io/refreshVersions/gradle-buildsrcversions/)
*   [变更日志](https://jmfayard.github.io/refreshVersions/CHANGELOG/)

[**参见 https://jmfayard.github.io/refreshVersions**T3 的文档](https://jmfayard.github.io/refreshVersions/)

## 设置

```
// settings.gradle(.kts)
plugins {
    // See https://jmfayard.github.io/refreshVersions
    id("de.fayard.refreshVersions") version "0.50.2"
}
refreshVersions { // Optional: configure the plugin
    // ...
}
```

Enter fullscreen mode Exit fullscreen mode

[阅读友好文档](https://jmfayard.github.io/refreshVersions/setup/)

## 使用

确保项目设置正确(见上文)。

**迁移项目:**

任务可以帮助你在几分钟或者更短的时间内迁移你的项目。

在版本 0.50.0 中，增加了对 Gradle 版本目录的支持([参见这里的讨论线程](https://github.com/jmfayard/refreshVersions/discussions/592))，所以现在需要一个`--mode`选项。

在没有它的情况下运行它来查看完整的列表和完整的描述…

</article>

[View on GitHub](https://github.com/jmfayard/refreshVersions)

*   [告白](#a-confession)
*   [如何迁移](#how-to-migrate)
*   [依赖关系管理](#dependencies-management)
*   [懒惰是程序员的一大美德](#laziness-as-a-great-virtue-of-the-programmer)
*   [链接](#links)

## 忏悔

了解我的人可能会感到惊讶，我即将写关于和赞扬格雷德尔。我经常表达我和格拉德的爱恨情仇。

Gradle 性能(实际上是 Android 构建工具的
)这个话题已经引起了很多关注。那里也做了很多工作。参见[https://guides.gradle.org/performance/](https://guides.gradle.org/performance/)和[https://developer . Android . com/studio/build/optimize-your-build](https://developer.android.com/studio/build/optimize-your-build)。

但是我想在这里集中讨论问题的后半部分。

> 这些 build.gradle 文件的维护可能会变得非常繁琐…

Gradle 的优点是它为表带来了强大的功能和灵活性，构建文件具有很强的声明性，易于阅读。

> 但是随着强大的能力而来的是对强大工具的需求……或者是令人沮丧的用户体验。

这就是格雷尔的阴暗面。虽然`build.gradle`文件易于**读取**，但是**写入**却相当困难。当你需要定制它们的时候，大部分都是靠自己。通常一段时间都不行，你也不知道为什么。然后在某个时候，你复制粘贴了正确的解决方案，它起作用了，但是你也不知道为什么。

实际情况是，Groovy 的动态特性与 Gradle 中完成的所有元编程结合得很差，给了我们很差的工具支持。

好了，这就是 [Gradle 的 kotlin-dsl 项目](https://github.com/gradle/kotlin-dsl)的背景，
Gradle 和 JetBrains 一直在悄悄地进行这个项目。快要到成熟期了，现在是关注话题的好时机。

## 如何迁移

[将构建逻辑从 Groovy 迁移到 Kotlin](https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/) 提到了两种可能的策略

1.  *将你构建的现有语法一点一点地移植到 Kotlin，同时保留结构——我们称之为机械移植*
2.  *朝着 Gradle 最佳实践的方向重构您的构建逻辑，并作为这一努力的一部分切换到 kot Lin DSL*

对于我们现有的项目，后者通常更有意义。

有一个故事说，很多 C 程序员就是从这一小步开始学 C++的。

```
// before.c
int main() {
   /* I was doing C programming */
   printf("Hello, World!");
   return 0;
}

// after.cpp
int main() {
   // Now I'm now doing c++
   printf("Hello, World!");
   return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

本着这种精神，我将把重点放在你现在可以与 Gradle & Kotlin 一起迈出的实际的第一步。

## 依赖关系管理

Gradle 允许我们以非常简洁的方式声明依赖关系

```
dependencies {
  implementation("com.squareup.okio:okio:2.0.0")
  implementation("io.reactivex.rxjava2:rxjava:2.2.0")
  implementation("de.mpicbg.scicomp:krangl:0.10.3")
} 
```

Enter fullscreen mode Exit fullscreen mode

我对这段代码有一些问题:

*   编辑魔术字符串是容易出错的。
*   如何在多模块项目中集中依赖关系？
*   什么是克朗格？
*   有新版本的 Okio 和 RxJava 吗？

使用 Groovy，我们可以用 [dependencies.gradle 模式](https://gist.github.com/gabrielemariotti/ad6672902464ee2392d0)解决集中化问题。

用 Kotlin 做同样的事情很容易:

```
// buildSrc/build.gradle.kts
plugins {
    `kotlin-dsl`
}

// buildSrc/src/main/kotlin/Libs.kt
object Libs {
    val okio = "com.squareup.okio:okio:2.0.0"
}

// some-module/build.gradle       or
// some-module/build.gradle.kts
dependencies {
   implementation(Libs.okio)
} 
```

Enter fullscreen mode Exit fullscreen mode

来自 [buildSrc 文件夹](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources)的代码自动对所有的`build.gradle`或`build.gradle.kts`文件可用。

## 懒惰是程序员的一大美德

我已经为几个项目这样做了，一开始我很高兴。在我的第三个项目中，提取所有这些字符串变得很乏味。

此外，我们还没有解决问题 3) `What is Krangl?`和 4) `What new versions are available?`

Ben Manes 有一个非常好的插件， [`gradle-versions-plugin`](https://github.com/ben-manes/gradle-versions-plugin) ，曾经应用于你的 Gradle 项目回答了这个问题。所以作为一个懒惰的程序员，我想知道:我能扩展它来精确地生成我需要的 Kotlin 代码吗？

事实证明这是可能的，所以让我介绍我的新 Gradle 插件！

# 包含 buildSrcVersions 插件

在您的 top `build.gradle`文件中，添加:

[![pluginVersion](../Images/18b705bb6aa7169ae3e612a1b8dc6b97.png)](https://plugins.gradle.org/plugin/de.fayard.buildSrcVersions)T3】

```
plugins {
  id("de.fayard.buildSrcVersions") version "$VERSION"
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在[https://plugins . grad le . org/plugin/de . fayard . buildsrcversions](https://plugins.gradle.org/plugin/de.fayard.buildSrcVersions)找到`$VERSION`。

这个新的声明性插件块是声明插件的首选方式，而不是提供更差的 Kotlin DSL 用户体验的`buildscript {...}`语法。

# `$ ./gradlew buildSrcVersions`

这是您第一次运行的命令，每次您添加一个新的依赖项或者只是想检查有哪些可用的版本。

它首先执行连接到互联网的任务`:dependencyUpdates`，可能需要一段时间。这个任务生成一个 [JSON 报告](https://github.com/jmfayard/sample-synclibs/blob/dcfd10777dbdf6b218b9d8c9af3de6afbdf35698/report.json)，其中包含关于您所有依赖项的元数据， [Kotlinpoet](https://github.com/square/kotlinpoet) 帮助将这些元数据转换成下面的代码。

```
// Folder buildSrc/src/main/kotlin

object Libs {

    /** [krangl website](https://github.com/holgerbrandl/krangl) */
    const val krangl: String = "de.mpicbg.scicomp:krangl:" + Versions.krangl

    /** [okio website](https://github.com/square/okio/) */
    const val okio: String = "com.squareup.okio:okio:" + Versions.okio

    /** [rxjava website](https://github.com/ReactiveX/RxJava) */
    const val rxjava: String = "io.reactivex.rxjava2:rxjava:" + Versions.rxjava
}
object Versions {

    const val krangl: String = "0.10.3" // up-to-date
    const val okio: String = "2.0.0" // available: milestone=2.1.0
    const val rxjava: String = "2.2.0" // available: milestone=2.2.2

} 
```

Enter fullscreen mode Exit fullscreen mode

# IDE 集成

这些生成的代码是我们体验由 Gradle 的`kotlin-dsl`在`IntelliJ IDEA`或`Android Studio ≥ 3.2`中提供的更好的 IDE 集成所需要的。

[https://www . use loom . com/share/7 edceb 83 FD 594 f 319356240 fcce 304 D5](https://www.useloom.com/share/7edceb83fd594f319356240fcce304d5)

我们现在已经解决了我们想要解决的问题

*   不再有神奇的字符串，我们键入`Libs.<TAB>`，自动完成剩下的工作。
*   我们可以在所有的`build.gradle`和`build.gradle.kts`文件中使用它们。
*   要了解什么是 Krangl，我们可以点击快速文档并点击网站链接。
*   要知道哪些新版本可用，我们只需从 IDE 跳到`Libs.okio`，再从那里跳到`Versions.okio`。目前用的是 2.0.0，一个有用的评论告诉我们，2.1.0 可用。(插件从不自动应用依赖更新，那应该是开发者的工作！！).

# 试试看

Groovy `build.gradle`和 Kotlin `build.gradle.kts`文件一旦被编写，看起来几乎是相似的。完全不同的是它的编写体验，所以我会鼓励你火起来 IntelliJ IDEA 或者 Android Studio。

我开始承认我讨厌格雷尔的黑魔法。用科特林打开黑盒非常有用。我意识到 Gradle 只是一个很好的、有用的、有良好文档记录的 API。一个 API，您可以像使用任何其他 Java API 一样使用您的开发人员工具和技能。一个其优点对我们隐藏的 API。如果有足够的兴趣，我计划在下面的文章中更深入。

## 链接

My plugin

## ![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png)[【jmfaard】](https://github.com/jmfayard)/[刷新版](https://github.com/jmfayard/refreshVersions)

### 生命太短暂，没有时间去谷歌依赖和版本

<article class="markdown-body entry-content container-lg" itemprop="text">

# 什么是 refreshVersions？

**refreshVersions** 帮助**grade**用户完成**繁琐的手工工作**通常涉及添加和更新**依赖项及其版本**。

[![](../Images/400e1cee45c783477900fdcdb386ff8d.png)T2】](http://www.youtube.com/watch?v=VhYERonB8co "Gradle refreshVersions")

## 证明文件

*   [从这里开始](https://jmfayard.github.io/refreshVersions/)
*   [设置刷新版本](https://jmfayard.github.io/refreshVersions/setup/)
*   [迁移您的项目](https://jmfayard.github.io/refreshVersions/migrate/)
*   [查找可用的依赖关系更新](https://jmfayard.github.io/refreshVersions/update-dependencies/)
*   [添加依赖关系](https://jmfayard.github.io/refreshVersions/add-dependencies/)
*   [探索内置依赖符号](https://jmfayard.github.io/refreshVersions/dependency-notations/)
*   [计划刷新版本机器人](https://jmfayard.github.io/refreshVersions/refreshversions-bot/)
*   [使用 buildSrc](https://jmfayard.github.io/refreshVersions/gradle-buildsrcversions/)
*   [变更日志](https://jmfayard.github.io/refreshVersions/CHANGELOG/)

[**参见 https://jmfayard.github.io/refreshVersions**T3 的文档](https://jmfayard.github.io/refreshVersions/)

## 设置

```
// settings.gradle(.kts)
plugins {
    // See https://jmfayard.github.io/refreshVersions
    id("de.fayard.refreshVersions") version "0.50.2"
}
refreshVersions { // Optional: configure the plugin
    // ...
}
```

Enter fullscreen mode Exit fullscreen mode

[阅读友好文档](https://jmfayard.github.io/refreshVersions/setup/)

## 使用

确保项目设置正确(见上文)。

**迁移项目:**

任务可以帮助你在几分钟或者更短的时间内迁移你的项目。

在版本 0.50.0 中，增加了对 Gradle 版本目录的支持([参见这里的讨论线程](https://github.com/jmfayard/refreshVersions/discussions/592))，所以现在需要一个`--mode`选项。

在没有它的情况下运行它来查看完整的列表和完整的描述…

</article>

[View on GitHub](https://github.com/jmfayard/refreshVersions)

gradle/kotlin-dsl 示例

## ![GitHub logo](../Images/75095a8afc1e0f207cda715962e75c8d.png)/[科特林-dsl-samples](https://github.com/gradle/kotlin-dsl-samples)

### 使用 Gradle Kotlin DSL 的示例构建

<article class="markdown-body entry-content container-lg" itemprop="text">

# Gradle Kotlin DSL 示例

[![License](../Images/139bbd4a5153fcd312f09ee1e5ad90c6.png)T2】](http://www.apache.org/licenses/LICENSE-2.0)

欢迎光临！Gradle kot Lin DSL T1 支持使用 JetBrains 的 T4 语言编写 T2 构建脚本。它旨在为 Gradle 用户提供一种丰富、灵活和静态类型的方法，结合最好的 ide 和工具体验来开发构建逻辑。

## 入门指南

启动并运行基于 Kotlin 的 Gradle build 的最快方法是使用 [gradle init](https://docs.gradle.org/current/userguide/build_init_plugin.html)

```
gradle init --dsl kotlin 
```

或者，如果你还没有安装 Gradle，你可以在[https://gradle-initializr.cleverapps.io/](https://gradle-initializr.cleverapps.io/)在线生成 Gradle 版本。

Gradle Kotlin DSL 记录在 Gradle 用户手册的[专门章节](https://docs.gradle.org/current/userguide/kotlin_dsl.html)中。

此外，Gradle [用户手册](https://docs.gradle.org/current/userguide/userguide.html)和[指南](https://gradle.org/guides/)包含演示 Groovy DSL 和 Kotlin DSL 的构建脚本摘录。这是找到如何使用 Gradle Kotlin DSL 做这做那的最好地方；它涵盖了来自…的所有 Gradle 特性

</article>

[View on GitHub](https://github.com/gradle/kotlin-dsl-samples)