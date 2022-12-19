# 开发方法:在 Java 企业环境中集成 React 前端

> 原文：<https://dev.to/tzehe/dev-recipes-integrate-react-frontend-in-a-java-corporate-environment-2e43>

## 问题

你发现自己处于一个 Java 环境中，想用 React 写一个前端或测试页？您想用 create-react-app 引导您的应用程序，但不知道如何将它集成到当前的构建步骤中？那这是给你的！

## 要求

*   Maven 前端插件([https://github.com/eirslett/frontend-maven-plugin](https://github.com/eirslett/frontend-maven-plugin))
*   maven3
*   react 应用程序

## 将 frontend-maven-plugin 添加到 pom.xml 中

pom.xml 示例

```
 <?xml version="1.0" encoding="UTF-8"?>
<project  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
 <modelVersion>4.0.0</modelVersion>

 <groupId>com.zehethomas</groupId>
 <artifactId>react-spring-example</artifactId>
 <version>0.0.1-SNAPSHOT</version>

 <packaging>jar</packaging>

 <parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.0.4.RELEASE</version>
  <relativePath/> <!-- lookup parent from repository -->
 </parent>

 <properties>
  <java.version>1.8</java.version>
 </properties>

 <dependencies>
  <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
 </dependencies>

 <build>
  <plugins>
   <plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
   </plugin>

   <!--
      1\. used for local installation of node and npm
      2\. to install dependencies with npm install
      3\. building the application
    -->
   <plugin>
    <groupId>com.github.eirslett</groupId>
    <artifactId>frontend-maven-plugin</artifactId>
    <version>1.6</version>
    <configuration>
     <nodeVersion>v10.0.0</nodeVersion>
     <npmVersion>6.1.0</npmVersion>
     <workingDirectory>src/main/js/frontend</workingDirectory>
    </configuration>
    <executions>
     <execution>
      <id>Install node and npm locally to the project</id>
      <goals>
       <goal>install-node-and-npm</goal>
      </goals>
     </execution>
     <execution>
      <id>npm install</id>
      <goals>
       <goal>npm</goal>
      </goals>
     </execution>
     <execution>
      <id>Build frontend</id>
      <goals>
       <goal>npm</goal>
      </goals>
      <configuration>
       <arguments>run build</arguments>
      </configuration>
     </execution>
    </executions>
   </plugin>
   <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-resources-plugin</artifactId>
    <executions>
     <execution>
      <id>Copy frontend build to target</id>
      <phase>process-resources</phase>
      <goals>
       <goal>copy-resources</goal>
      </goals>
      <configuration>
       <outputDirectory>${basedir}/target/classes/resources</outputDirectory>
       <resources>
        <resource>
         <directory>${basedir}/src/main/js/frontend/build</directory>
         <filtering>true</filtering>
        </resource>
       </resources>
      </configuration>
     </execution>
    </executions>
   </plugin>
  </plugins>
 </build>
</project> 
```

Enter fullscreen mode Exit fullscreen mode

使用 maven-frontend-plugin 在本地安装节点和 npm。然后运行`npm install`来加载所有依赖项，并用`npm run build`构建应用程序

maven-resources-plugin 用于将前端构建复制到目标。
重要的是把`<directory>${basedir}/build</directory>`
改成你的前端代码所在的位置。

## 连接 React 应用和后端

如果您遇到错误，这可能是因为同源策略[同源策略](https://en.wikipedia.org/wiki/Same-origin_policy)。

您可以通过将`"proxy": "http://localhost:8080/"`添加到 package.json 文件来轻松解决这个问题，该文件代理到您后端的路由。

示例 package.json

```
{
  "name": "frontend",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "react": "^16.5.2",
    "react-dom": "^16.5.2",
    "react-scripts": "1.1.5"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  },
  "proxy": "http://localhost:8080/"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个. npmrc 文件

如果您的公司使用私有 npm 注册表来防止在获取依赖项时出现 npm 404 错误，这是必要的

## 举例。npmrc

```
registry = http://<your.local.registry>/ 
@babel:registry = https://registry.npmjs.org/
@types:registry = https://registry.npmjs.org/ 
```

Enter fullscreen mode Exit fullscreen mode

## 构建并运行应用程序

现在可以运行`maven clean package`来构建驻留在目标文件夹中的`react-spring-example-0.0.1-SNAPSHOT.jar`。

运行`java -jar react-spring-example-0.0.1-SNAPSHOT.jar`启动您的应用程序。

转到 localhost:8080，然后喊一声，就大功告成了！:>

## 源代码

你可以在 github 上找到一个示例项目 [react-spring-example](https://github.com/tzehe/react-spring-example.git) 。

谢谢大家！

来源:
启发我写这篇文章。
[使用 create-react-app 和 Spring Boot 创建新的 web 应用](http://matejsprogblog.blogspot.com/2017/06/creating-new-web-app-using-create-react.html)