# 科特林+龙卷风 FX + OpenCV =好玩！

> 原文：<https://dev.to/s1hofmann/kotlin--tornadofx--opencv--fun--39e5>

这是一篇交叉文章，最初发表在我的博客上

# [T1】简介](#intro)

几年前，我提出了一个有趣的项目想法，其中涉及一些 GUI 编程与一些计算机视觉算法的结合。不幸的是，当时我没有足够的时间开始这个项目，也就把它忘了。

我不能说我现在有更多的时间，但是我已经开始使用 Kotlin 并发现了 TornadoFX，这是一个用于 Kotlin 的 JavaFX 框架。由于可以编写与 JavaFX 集成的 OpenCV 应用程序，并且 Kotlin 与 Java 无缝集成，我一直渴望尝试用 OpenCV Java 和 TornadoFX 编写一些图像处理代码。

当我迈出第一步的时候，我突然想起了我以前的项目想法，并决定重新开始。

因为我还在学习 Kotlin 和 TornadoFX，
我会不止一次地尝试解决问题。
但如果你对 Kotlin、TornadoFX 和 OpenCV 感兴趣，请继续阅读。

# 设置

我正在使用一台相当旧的 MacBook Pro 和以下软件:

*   一点二十分
*   龙卷风 FX 1.7.14
*   OpenCV
*   Maven 3.5.2

虽然我将描述基于 macOS 的一切，但是你也可以在 Linux 或 Windows 上工作。
您唯一需要记住的是，OpenCV 需要为您的平台构建本地库。

下一节将讨论如何在 Maven 项目中包含 OpenCV。

# OpenCV 与 Maven

目前 OpenCV 还没有官方的 Maven 神器，但是有几个第三方的 repos。这些回购的问题在于，它们通常提供相当旧的版本，比如 2.4.9。

由于这个原因，以及我想看看如何建立自己的定制 repo 的事实，我创建了一个 GitHub 库来托管我的 OpenCV 依赖项。

## OpenCV Java 绑定

为了将 OpenCV 与 Java(或 Kotlin)一起使用，构建库时必须启用 Java 标志。在 macOS 上，最简单的方法就是使用[自制软件](https://brew.sh/)和编辑过的公式。
如果你在安装 OpenCV 时遇到问题，请看[这个回答](https://stackoverflow.com/questions/37326486/install-opencv-on-mac-with-or-without-homebrew/37327123#37327123)。

自制软件允许使用

`brew edit $formula`
自定义公式

我们必须改变的一件事是下面的构建标志:

```
-DBUILD_opencv_java=ON 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以通过
构建启用 Java 绑定的 OpenCV

```
brew install --build-from-source opencv 
```

Enter fullscreen mode Exit fullscreen mode

一旦构建完成，在 Java 中使用 OpenCV 所需的文件就在
中

```
/usr/local/Cellar/opencv/3.4.1_1/share/OpenCV/java 
```

Enter fullscreen mode Exit fullscreen mode

*   libopencv_java341.dylib
*   opencv-341.jar

编译时需要 JAR 文件，运行时需要本机库。

## GitHub 作为 Maven 资源库

在 GitHub 上托管工件非常容易:

1.  将工件安装到本地存储库中
2.  将本地存储库推送到 GitHub

将 JAR 安装到本地存储库可以这样实现(假设您在本地存储库文件夹中):

```
mvn install:install-file -Dfile=/path/to/JAR -DgroupId=$groupId -DartifactId=$artifactId -Dversion=$version -Dpackaging=jar -DlocalRepositoryPath=/path/to/local/repo 
```

Enter fullscreen mode Exit fullscreen mode

例如，对于我的本地设置:

```
mvn install:install-file -Dfile=/usr/local/Cellar/opencv/3.4.1_1/share/OpenCV/java/opencv-341.jar -DgroupId=org.s1h -DartifactId=opencv -Dversion=3.4.1 -Dpackaging=jar -DlocalRepositoryPath=. 
```

Enter fullscreen mode Exit fullscreen mode

在我们也可以通过 Maven 提供 OpenCV 原生库之前，
我们首先必须将它打包成 JAR:

```
jar cvf opencv-native-macos-3.4.1.jar libopencv_java341.dylib 
```

Enter fullscreen mode Exit fullscreen mode

## 通过 Maven 使用 GitHub 资源库

为了从我们的 GitHub 库中获得工件，我们必须在我们的

*pom.xml*

```
<repositories>
    <repository>
        <id>mvn-opencv</id>
        <url>https://raw.github.com/s1hofmann/MavenOpenCV/master/</url>
    </repository>
</repositories> 
```

Enter fullscreen mode Exit fullscreen mode

引用库，**引用主**引用分支。

现在可以将 OpenCV 声明为依赖项:

```
<dependency>
    <groupId>org.s1h</groupId>
    <artifactId>opencv</artifactId>
    <version>${opencv.version}</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

## 打包 OpenCV 原生库

由于我们已经将 OpenCV 原生库打包成 JAR，
我们也能够在 Maven 中使用它。

本机库不是编译时依赖项，
我们只需将它提供给我们的

```
java.library.path 
```

Enter fullscreen mode Exit fullscreen mode

在运行时，
所以在我们的构建中包含 JAR 就足够了，以便从中提取原生库。

我在用

```
maven-dependency-plugin 
```

Enter fullscreen mode Exit fullscreen mode

为此:

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
        <execution>
            <id>unpack</id>
            <phase>prepare-package</phase>
            <goals>
                <goal>unpack</goal>
            </goals>
            <configuration>
                <artifactItems>
                    <artifactItem>
                        <groupId>org.s1h</groupId>
                        <artifactId>opencv-native-macos</artifactId>
                        <version>${opencv.version}</version>
                        <includes>**\/*.dylib</includes>
                        <outputDirectory>${project.build.directory}/lib</outputDirectory>
                    </artifactItem>
                </artifactItems>
            </configuration>
        </execution>
    </executions>
</plugin> 
```

Enter fullscreen mode Exit fullscreen mode

本地库将从 JAR 中提取并复制到

```
/target/lib 
```

Enter fullscreen mode Exit fullscreen mode

文件夹，以便在运行时可用。

# 样本程序

为了测试我们的 OpenCV 设置是否有效，我们可以使用下面的小样本，
，它只创建了一个 5x5 的单位矩阵，并将其打印到标准输出:

```
package org.s1h

import org.opencv.core.Core
import org.opencv.core.CvType
import org.opencv.core.Mat

fun main(args: Array<String>) {
    System.loadLibrary(Core.NATIVE_LIBRARY_NAME)

    val mat = Mat.eye(5, 5, CvType.CV_8UC1)
    println("mat = ${mat.dump()}")
} 
```

Enter fullscreen mode Exit fullscreen mode

# 包括科特林标准库

为了运行 Kotlin 代码，我们还需要 Kotlin 标准库。
一种方法是在执行 Kotlin 程序时手动将其添加到类路径中，
一种更方便的方法是将我们的程序和 Kotlin 标准库打包成一个单独的 JAR。

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.1.0</version>
    <executions>
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
            <configuration>
                <archive>
                    <manifest>
                        <mainClass>${main.class}</mainClass>
                    </manifest>
                </archive>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
            </configuration>
        </execution>
    </executions>
</plugin> 
```

Enter fullscreen mode Exit fullscreen mode

# 把所有的东西放在一起

所有的部分都准备好了，我们就可以使用
来构建项目了

```
mvn clean package 
```

Enter fullscreen mode Exit fullscreen mode

这将下载所有依赖项，
将 OpenCV 本地库提取到

```
target/lib 
```

Enter fullscreen mode Exit fullscreen mode

文件夹并构建一个包含 Kotlin 标准库的 fat JAR。

要运行独立的 JAR，您必须指定本地库的路径:

```
java -Djava.library.path=target/lib -jar target/tornadoCV-1.0-SNAPSHOT-jar-with-dependencies.jar 
```

Enter fullscreen mode Exit fullscreen mode

# 链接

你可以在我的 [GitHub repo](https://github.com/s1hofmann/tornadoCV) 上看看我的项目设置。

我的 OpenCV repo 位于[这里](https://github.com/s1hofmann/MavenOpenCV)。

# 结论

1.  这是我有史以来最长的帖子！(耶！)
2.  Kotlin 和 OpenCV 配合得很好！
3.  如果您曾经使用过 Java / Maven，那么整个项目的结构/设置非常简单
4.  我很想继续下去！

我希望你喜欢这篇文章，在我的下一篇文章中，我将尝试使用 TornadoFX 和相机输入。

再见

西蒙