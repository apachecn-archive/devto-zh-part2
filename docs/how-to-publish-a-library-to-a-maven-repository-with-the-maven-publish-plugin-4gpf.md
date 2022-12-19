# 如何使用 maven-publish 插件将库发布到 Maven 存储库中

> 原文：<https://dev.to/brightdevs/how-to-publish-a-library-to-a-maven-repository-with-the-maven-publish-plugin-4gpf>

一个经验丰富的开发人员偶尔会创建一段代码，他或她希望在不同的项目中*重用*。当这种时候，知道如何发布一个库是很有用的，这样它就可以很容易地合并到一个不同的项目中。在这篇文章中，我将描述如何用`maven-publish`和`com.jfrog.bintray` Gradle 插件发布一个 Kotlin 库到 [JCenter](https://bintray.com/bintray/jcenter) 。

# 度胃塞

第一步是应用 [Maven 插件](https://docs.gradle.org/current/userguide/maven_plugin.html)。该插件增加了对将工件部署到 Maven 仓库的支持。注意，在多项目构建的情况下，例如 [ShouldKO](https://github.com/bright/shouldko) ，Maven 插件应该应用于每个定义了要发布的工件的项目。您可以使用`allprojects`来消除重复，例如:

```
allprojects {
    repositories {
        jcenter()
    }

    apply plugin: 'kotlin'
    apply plugin: 'maven'

    group "pl.miensol.shouldko"
} 
```

Enter fullscreen mode Exit fullscreen mode

对于稍后使用的 [`com.jfrog.bintray`](https://github.com/bintray/gradle-bintray-plugin) 插件，为了更好地处理 Maven 工件，我们需要应用额外的 Gradle 插件。这个附加部分是 [`maven-publish`](https://docs.gradle.org/current/userguide/publishing_maven.html) 插件，它提供了以 Maven 格式发布工件的能力。我们需要做的就是在主项目中加入`apply plugin: 'maven-publish'`。

# 定义 Maven 发布

[`com.jfrog.bintray`](https://github.com/bintray/gradle-bintray-plugin#step-7-define-artifacts-to-be-uploaded-to-bintray) 插件依赖于正确定义的 [Maven 出版物](https://docs.gradle.org/current/userguide/publishing_maven.html)。Gradle DSL 允许我们根据项目属性轻松定义它们，例如

```
publishing {
    publications {
        hamcrest(MavenPublication) {
            def project = project(':hamcrest')
            from project.components.java
            artifact project.sourcesJar { // not required, includes sourcesJar with correct classifer
                classifier "sources"
            }
            groupId group
            artifactId project.name
            version project.version
        }

        core(MavenPublication) {
            def project = project(':core')
            from project.components.java
            artifact project.sourcesJar {
                classifier "sources"
            }
            groupId group
            artifactId project.name
            version project.version
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上述 Maven 出版物包括 sources 工件。为工件发布额外的分类器是很重要的，因为[允许 IDE 显示文档弹出窗口或者通过库源代码进行调试](https://stackoverflow.com/a/20909695/155213)。然而，我们需要首先定义它，因为当应用`java`或`kotlin`插件到 Gradle 项目时，默认情况下它不包含在内。这很容易做到，如下:

```
allprojects {
    task sourcesJar(type: Jar, dependsOn: classes) {
        from sourceSets.main.allSource
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 项目版本化

正如你在上面看到的，我们使用了`project.version`来表示`MavenPublication`的版本。软件版本化有多种策略，但是对于库来说,[语义版本化](https://semver.org/)方案被广泛接受为标准。如果你想使用它，那么 Gradle 有插件可以简化维护预发布和补丁版本的日常任务。我喜欢来自 [`ajoberstar`](https://github.com/ajoberstar/gradle-git/wiki) 的一套插件，它提供了一种基于 git 标签给你的项目版本化的自以为是的方法。应用它们很容易:

```
plugins {
    id "org.ajoberstar.grgit" version "1.7.2"
    id "org.ajoberstar.release-opinion" version "1.7.2"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当你发出例如`gradle build`命令时，插件将[根据你的 git 库状态](https://github.com/ajoberstar/gradle-git/wiki/Release%20Plugins#how-do-i-use-the-opinion-plugin) :
推断出下一个版本

```
> Configure project : 
Inferred project: shouldko, version: 0.1.5-dev.0.uncommitted+4f71d34 
```

Enter fullscreen mode Exit fullscreen mode

# Bintray 上传

最后，当我们准备好上传我们的库并让每个人都可以使用时，我们需要设置一个 [Bintray 帐户](https://bintray.com/signup/oss)。一旦我们有了它，在[配置文件](https://bintray.com/profile/edit)页面上，我们可以访问配置[二进制托盘](https://github.com/bintray/gradle-bintray-plugin) Gradle 插件所需的 API 密钥。

```
bintray {
    user = project.hasProperty('bintrayUser') ? project.property('bintrayUser') : System.getenv('BINTRAY_USER')
    key = project.hasProperty('bintrayApiKey') ? project.property('bintrayApiKey') : System.getenv('BINTRAY_API_KEY')
    publications = ['core', 'hamcrest']
    pkg {
        repo = 'maven'
        name = 'shouldko'
        desc = 'Adds source line to tests assertion messages'
        userOrg = 'brightinventions'
        licenses = ['MIT']
        vcsUrl = 'https://github.com/bright/shouldko.git'
        labels = ['tests', 'hamcrest', 'junit']
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**bin tray API 密匙应该是私有的，决不能包含在源代码库中。**
我们可以通过查看项目属性来配置`user`和`key`，如果不可用，可以使用环境变量。这样就没有必要公开暴露他们。

```
gradle build bintrayUpload -PbintrayUser=<apiUser> -PbintrayApiKey=<apikKey> 
```

Enter fullscreen mode Exit fullscreen mode

`repo`是 Bintray 存储库的名称。您可以使用同一个 Bintray 存储库来托管多个项目。

Bintray 插件非常沉默，因此我喜欢添加一些日志消息来查看`bintrayUpload`何时完成:

```
afterEvaluate {
    tasks.bintrayUpload.doLast {
        logger.lifecycle("Uploaded artifacts to bintray at version $version")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 特拉维斯打造

每个项目都应该至少有某种形式的[持续集成](https://en.wikipedia.org/wiki/Continuous_integration)。对于开源软件，至少有几个免费的构建服务器可用。[特拉维斯](https://travis-ci.org)可能是最受欢迎的一个。对于 gradle 项目，Travis 将默认调用`build`。如果您想在构建成功完成时将构建工件上传到 Bintray，您需要在`.travis.yml`的`script`部分添加一行，如下所示:

```
script:
  - ./gradlew build
  - ./gradlew bintrayUpload 
```

Enter fullscreen mode Exit fullscreen mode

显然，还需要配置 Bintray 凭证，这可以通过项目配置页面来完成:

[![TravisCI environment configuration](../Images/91023481fde9135eef5d20daa1259f1a.png)T2】](/images/publish-library/travis-configure.png)

现在，Gradle git 插件将创建一个开发版本，并在每次 Travis 构建时将其发布到 Bintray。

# 标记释放

每当你想发布一个新版本的库，你现在可以简单地标记一个特定的版本，例如

```
git tag 0.1.4
git push origin 0.1.4 
```

Enter fullscreen mode Exit fullscreen mode

在本地或连续集成构建完成后，您应该会在 Bintray web 应用程序中看到一个新版本。从那里你需要[来发布版本](https://bintray.com/docs/usermanual/starting/starting_tutorial2uploading.html)。

# 使用新库

一旦发布了一个版本，您就可以轻松地从 maven 或 gradle 项目中使用它。在您[将您的包链接到 JCenter](https://bintray.com/bintray/jcenter) 之前，您需要通知您的构建系统一个新的 maven 资源库的位置，例如:

```
repositories {
    jcenter()
    maven { url 'https://dl.bintray.com/brightinventions/maven' }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`brightinventions`是组织用户名，`maven`是上面提到的存储库名称。你现在终于可以使用你的图书馆了🎉:

```
compile 'pl.miensol.shouldko:hamcrest:0.1.4' 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)