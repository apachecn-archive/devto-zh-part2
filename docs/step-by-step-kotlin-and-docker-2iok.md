# 循序渐进:科特林和多克

> 原文：<https://dev.to/baens/step-by-step-kotlin-and-docker-2iok>

*本文最初发表于[我的个人博客](https://blog.baens.net/posts/step-by-step-kotlin/)*

这是一个关于如何让 Kotlin 在 Docker 容器中运行的分步指南。我们将在本地运行一个基本的 Kotlin 项目，然后演示如何在 Docker 容器中构建和运行相同的项目。

[配套库在这里](https://github.com/baens/blog-step-by-step-kotlin)。

## 第 0 步:先决条件

我假设您已经安装并运行了以下程序。在初始设置之后，我们将使用本地 gradle 包装器实例，但是您在初始创建时确实需要它。

另一个我要假设的是有一个正在运行的 docker 实例。

*   爪哇语(JDK 1.8.0_144)
*   Gradle(创建初始包装)
*   坞站(17.12.0-ce-mac49 (21995))

## [第一步:格拉德](https://github.com/baens/blog-step-by-step-kotlin/commit/6ed247244a23eae991693b4a512cda2521892c9c)

这一步的目标是启动并运行一个 gradle 包装器，这样我们就可以为使用这个库的每个人锁定 gradle 版本。

运行以下命令:

`> gradle wrapper --gradle-version 4.5`

通过运行
来验证它是否工作

```
> ./gradlew -v

-----------------------------------------------------------------
Gradle 4.5
-----------------------------------------------------------------

Build time:   2018-01-24 17:04:52 UTC
Revision:     77d0ec90636f43669dc794ca17ef80dd65457bec

Groovy:       2.4.12
Ant:          Apache Ant(TM) version 1.9.9 compiled on February 2 2017
JVM:          1.8.0_144 (Oracle Corporation 25.144-b01) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您可以仔细检查我与这一步关联提交，看看我为此向 git 存储库添加了什么，但这确实是一个简单的步骤。

## [第二步:科特林 Hello World 本地](https://github.com/baens/blog-step-by-step-kotlin/commit/46304a14b7d74554775695ed3edc91a1d197e3e7)

我们这一步的最终目标是让它从`./gradlew run`命令输出“Hello World”。

让我们首先设置我们的工具链。这将只是一个 gradle 文件与所有的插件和依赖，我们将需要的。

**build.gradle** :

```
/**
 * This section is for all of the plugins we need to make this work. 
 * Kotlin, the fat jar builder, and flag it 
 * as an application so `./gradlw run` will work
 */
plugins {
    id 'org.jetbrains.kotlin.jvm' version '1.2.21'
    id 'com.github.johnrengelman.shadow' version '2.0.2'
}

apply plugin: 'application'

/**
 * Standard dependency section for gradle. 
 * Define the kotlin standard libray for
 * Java 8.
 */
repositories {
    mavenCentral()
}

dependencies {
    compile 'org.jetbrains.kotlin:kotlin-stdlib-jre8'
}

/**
 * Personal preference. I hate having src/main/kotlin
 * be the root, so I change it that 'src'
 * is the root of my source directory.
 */ 
sourceSets {
    main.kotlin.srcDirs += 'src'
}

// Define the main startup class and jar name
mainClassName = 'MainKt'
archivesBaseName = 'step-by-step-kotlin'

// tell the jar which class to startup in.
jar {
    manifest {
        attributes 'Main-Class': 'MainKt'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们再看一遍这些线，画出发生了什么。

第 6 - 11 行:这些只是插件行。Gradle 有几种定义插件的方法。一个叫做`plugin DSL`，另一个叫做`script plugin`。我不打算在这里解释它们的区别，请阅读[插件文档](https://docs.gradle.org/current/userguide/plugins.html)以获得更好的想法。需要注意的一个插件是`shadow`插件。这一个是创建 Fat Jars，或者换句话说，包含运行所需的所有依赖项的 jar。我可以直接给你这个 jar 文件，它就会运行，而不必确保所有东西都在你的类路径上或者其他一些无意义的东西。这将使它更容易在 docker 内部运行。

第 24 行:这里我们定义了 Java 8 的 kotlin 标准。这是 Kotlin 1.1 中的新版本，你可以在这里[读到它。有几种方法可以做到这一点，但是这是 Java 8 所需要的。我在使用向后兼容的版本时遇到了一些问题，我尝试使用 Java 8 的版本，这样新的特性就可以正常工作了](https://kotlinlang.org/docs/reference/using-gradle.html#configuring-dependencies)

**第 32 - 34 行**:这完全是可选的，但这是我更喜欢的项目设置方式。我讨厌默认`src/main/java`之类的傻话。这是 3 个文件夹，我必须导航才能找到我的源。此外，我还必须向下浏览我的名称空间的文件夹。项目很少必须有所有这些不同类型的语言，所以你至少可以消除`java`文件夹。第二，PITA 必须维护一个`test`源文件夹和一个`main`源文件夹。我更喜欢把我的测试和源代码放在一起。这使得找到与每个文件相关联的测试变得更加容易。再说一次，这完全是个人的选择，但是我做这个已经够久了，现在对这个有强烈的感觉。给我一个文件夹，里面有我所有的源代码，不要让我把我不想要的东西也放进去！

**第 37 行**:这是一个简单的行，但我确实想喊出一些东西。`MainKt`是特殊的，因为它是以`kt`结尾的文件名。当我们到达那个源头时，我会解释得更多一点。但这是当文件中只有一个方法时 Kotlin 的惯例。它会自动将该方法包含在一个类中，供 JVM 使用。所以要小心那个`kt`。

现在是实际的，非常简单的，hello world 代码。

**src/main . kt:**T2】

```
fun main(args: Array<String>) {
    println("Hello World")
} 
```

Enter fullscreen mode Exit fullscreen mode

我想指出并祝贺科特林团队。如果你曾经做过 Java 编程，你知道你总是需要每个文件一个类。科特林有几个惯例来简化某些事情，我认为这很棒。我喜欢这样的东西，它能去除浮华和环境。当试图让新开发人员熟悉这门语言时，这尤其有用。每次我不得不教别人 Java 时，这个`public class Whatever`都是陌生的，甚至在我们运行任何东西之前，我们就不得不走进一个兔子洞。

您现在应该能够运行`./gradlew run`并看到这个:

```
>./gradlew run

> Task :run
Hello World

BUILD SUCCESSFUL in 5s
2 actionable tasks: 2 executed 
```

Enter fullscreen mode Exit fullscreen mode

## [第三步:科特林 hello world inside docker](https://github.com/baens/blog-step-by-step-kotlin/commit/8c781f7d5180ac25542fbf707e2d1746debcebc1)

**更新截止 2018-02-15**:Thammachart Chinvarapon 很大度地指出我用错了 Java docker 容器。我对此进行了更正，并将其移动到 openjdk 容器中。谢谢 Thammachart！

下一步将是在 docker 容器中运行它。虽然这相当简单，但我想演示如何将构建 docker 映像与运行 docker 映像分开。虽然这增加了一点复杂性，但这实际上更像是你真正使用这些东西的方式。

既然一切都正常，我们只需要创建并运行一个 docker 文件。

**Dockerfile**

```
ARG VERSION=8u151

FROM openjdk:${VERSION}-jdk as BUILD

COPY . /src
WORKDIR /src
RUN ./gradlew --no-daemon shadowJar

FROM openjdk:${VERSION}-jre

COPY --from=BUILD /src/build/libs/step-by-step-kotlin-all.jar /bin/runner/run.jar
WORKDIR /bin/runner

CMD ["java","-jar","run.jar"] 
```

Enter fullscreen mode Exit fullscreen mode

**第 3 - 9 行**:这是“构建器”部分。

第 9 - 14 行:这是实际运行的路段。

这是在一个 docker 文件中总结如何在其内部构建和创建图像，以及运行它的一种很棒的方式。我尝试做的是让你可以在任何地方创建这个 docker 图像。这个构建器映像包括构建东西所需的所有工具。因为我们有这个 gradle 包装器，工具链也在那里。

第二部分是实际运行的内容。`COPY --from=BUILD`是我们可以从最后一幅图像中获得的魔力(注意:`BUILD`来自第 3 行`as BUILD`)。

我不打算过多地讨论 docker 到底在做什么，但我想指出几件快速的事情。我们基本上做一个拷贝，设置什么是工作目录(想想`cd`如果你使用命令行)，然后工作或运行。

运行这个，映像将会构建并运行:

```
docker build -t kotlin . && docker run kotlin 
```

Enter fullscreen mode Exit fullscreen mode