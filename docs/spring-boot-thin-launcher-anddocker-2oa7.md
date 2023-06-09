# Spring Boot 瘦发射和对接

> 原文：<https://dev.to/bufferings/spring-boot-thin-launcher-anddocker-2oa7>

## Spring Boot 瘦发射架

昨天，我玩了 Spring Boot 创业板凳。

[![bufferings](img/f4c1331756d4432bf2ec4bde4b939f7a.png)](/bufferings) [## 开始享受弹簧启动椅

### mitz Oct 1 ' 182 分钟读数

#springboot #java](/bufferings/started-enjoying-spring-boot-startup-bench-1ahj)

并注意到它使用 Spring Boot 薄发射架。所以今天，我玩了瘦启动器，它可以创建一个瘦罐子。

[https://github.com/dsyer/spring-boot-thin-launcher](https://github.com/dsyer/spring-boot-thin-launcher)

## 我的源代码

在这里:

[https://github.com/bufferings/spring-boot-thin-sandbox](https://github.com/bufferings/spring-boot-thin-sandbox)

有 3 个目录。

*   demo-fat:正常的 SpringBoot 应用程序。
*   演示-瘦:瘦应用程序。
*   带有 docker 的瘦应用程序。

## 演示-发

这只是一个简单的带有 web 的 spring boot 应用程序，虽然它没有页面。

```
❯ cd demo-fat
❯ ./mvnw clean package
❯ ls -ahl target/demo-fat-0.0.1-SNAPSHOT.jar
-rw-rw-r - 1 bufferings bufferings 16M Sep 30 22:16 target/demo-fat-0.0.1-SNAPSHOT.jar 
```

Enter fullscreen mode Exit fullscreen mode

大小为 16MB。

```
❯ java -jar target/demo-fat-0.0.1-SNAPSHOT.jar
…
2018–09–30 22:29:04.617 INFO 32639 - - [ main] com.example.demo.DemoApplication : Started DemoApplication in 3.852 seconds (JVM running for 4.464)
… 
```

Enter fullscreen mode Exit fullscreen mode

## 演示-瘦

这也是一个简单的 spring boot 应用程序，但带有 Spring Boot 瘦启动器。

```
❯ cd demo-thin
❯ ./mvnw clean package
❯ ls -ahl target/demo-thin-0.0.1-SNAPSHOT.jar
-rw-rw-r - 1 bufferings bufferings 11K Sep 30 22:24 target/demo-thin-0.0.1-SNAPSHOT.jar 
```

Enter fullscreen mode Exit fullscreen mode

才 11KB。它在启动时下载一个启动程序和库，但实际上它使用. m2 目录，所以不会花太长时间。

```
❯ java -jar target/demo-thin-0.0.1-SNAPSHOT.jar
…
2018–09–30 22:30:51.689 INFO 1303 - - [ main] com.example.demo.DemoApplication : Started DemoApplication in 3.012 seconds (JVM running for 4.987)
… 
```

Enter fullscreen mode Exit fullscreen mode

## 演示瘦坞站

这是一个带有 Docker 的瘦 app。

```
❯ cd demo-thin-docker
❯ ./mvnw clean package
❯ docker build -t demo-thin-docker . 
```

Enter fullscreen mode Exit fullscreen mode

下载所有依赖项需要时间。

```
❯ docker run -p 8080:8080 demo-thin-docker - thin.debug=true - thin.offline=true
…
2018–09–30 13:42:32.978 INFO 1 - - [ main] com.example.demo.DemoApplication : Started DemoApplication in 3.437 seconds (JVM running for 5.804)
… 
```

Enter fullscreen mode Exit fullscreen mode

## docker file

如下:

```
FROM openjdk:11-jdk-slim
COPY docker/spring-boot-thin-wrapper-1.0.15.RELEASE.jar /thin/wrapper.jar
COPY pom.xml /thin/pom.xml
WORKDIR /thin
RUN jar cvf pom.jar pom.xml
RUN java -jar wrapper.jar \
     --thin.archive=/thin/pom.jar \
     --thin.dryrun=true \
     --thin.debug=true FROM openjdk:11-jre-slim
COPY --from=0 /root/.m2 /root/.m2
COPY target/demo-thin-docker-0.0.1-SNAPSHOT.jar /app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"] 
```

Enter fullscreen mode Exit fullscreen mode

### 从

```
FROM openjdk:11-jdk-slim 
```

Enter fullscreen mode Exit fullscreen mode

没理由用 Java 11，可以用老版本:)

### 复制 wrapper.jar

```
COPY docker/spring-boot-thin-wrapper-1.0.15.RELEASE.jar /thin/wrapper.jar 
```

Enter fullscreen mode Exit fullscreen mode

我想为库创建一个 docker 层。所以我下载了 wrapper.jar 并把它放在存储库中。

它有一个模拟运行的特性，不运行主类，而只是将库下载到. m2 存储库中。

### 复制 pom.xml

```
COPY pom.xml /thin/pom.xml
WORKDIR /thin
RUN jar cvf pom.jar pom.xml 
```

Enter fullscreen mode Exit fullscreen mode

要创建 docker 层，请复制 pom.xml 项目。wrapper.jar 可以在 jar 文件中加载 pom.xml。所以我创建了一个 jar 文件，其中只有 pom 文件。

### 预演

```
RUN java -jar wrapper.jar \
     --thin.archive=/thin/pom.jar \
     --thin.dryrun=true \
     --thin.debug=true 
```

Enter fullscreen mode Exit fullscreen mode

然后，用 pom.jar 在模拟运行模式下执行 wrapper jar。如果想了解更多细节，可以设置`--thin.trace=true`。

然后 pom.xml 的所有依赖项都被下载到`/root/.m2`目录中。

### 复制. m2

```
FROM openjdk:11-jre-slim
COPY --from=0 /root/.m2 /root/.m2 
```

Enter fullscreen mode Exit fullscreen mode

我使用多阶段构建，因为除了. m2，我不需要其他文件。所以我把. m2 目录复制到新镜像中。

### 复制 app.jar 并运行

```
COPY target/demo-thin-docker-0.0.1-SNAPSHOT.jar /app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"] 
```

Enter fullscreen mode Exit fullscreen mode

然后像往常一样，复制 jar 并运行它。

### 离线模式

为了检查运行时是否没有下载，我使用了- thin.offline=true 以脱机模式运行 docker。

```
❯ docker run -p 8080:8080 demo-thin-docker - thin.debug=true - thin.offline=true 
```

Enter fullscreen mode Exit fullscreen mode

## 用 docker 把好零件

只要我们不改变依赖关系，库层就会被重用。所以构建很快就完成了。

今天的娱乐就到此为止。我在想什么样的用法适合瘦罐子和胖罐子。