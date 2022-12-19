# 如何使用 Spark 创建基本 REST API 应用程序

> [https://dev . to/alexruzenck/how-create-uma-app-rest-API-bsica-com-spark-3nkn](https://dev.to/alexruzenhack/como-criar-uma-aplicao-rest-api-bsica-com-spark-3nkn)

Spark 微架构可让您轻松建立微 web 服务、将服务封装在胖 jar 中，并在 dock 的微例项中播放。

### 1。创建 maven 项目

配置文件`pom.xml`具有以下依赖项:

```
<dependencies>
    <dependency>
        <groupId>com.sparkjava</groupId>
        <artifactId>spark-core</artifactId>
        <version>2.7.1</version>
    </dependency>

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.21</version>
    </dependency>
</dependencies> 
```

Enter fullscreen mode Exit fullscreen mode

### 2。配置 jar 组件

构建一个包含项目所有依赖项的胖 jar，以便您可以独立运行应用程序。

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <executions>
        <execution>
            <goals>
                <goal>attached</goal>
            </goals>
            <phase>package</phase>
            <configuration>
                <finalName>${project.name}</finalName>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
                <archive>
                    <manifest>
                        <mainClass>yourgroupid.sparkrest.HelloWorld</mainClass>
                    </manifest>
                </archive>
            </configuration>
        </execution>
    </executions>
</plugin> 
```

Enter fullscreen mode Exit fullscreen mode

### 3。为您的 REST API 创建一个端点

创建一个名为“`HelloWorld`”的类，在该类中，您将设置接收请求的端点`GET`，并返回简单文本`Hello World`。

```
import static spark.Spark.*;

public class HelloWorld {
    public static void main(String[] args) {
        get("/hello", (req, res) -> "Hello World");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 4。环绕您的应用程序

最后，运行此脚本编译您的应用程序

```
$> mvn dependency:resolver
$> mvn verify
$> mvn package 
```

Enter fullscreen mode Exit fullscreen mode

现在运行编译后的胖 jar

```
$> java -jar nomedoprojeto-jar-with-dependencies.jar 
```

Enter fullscreen mode Exit fullscreen mode

在网址`http://localhost:4567/hello`
上提出申请

```
// Resposta
Hello World 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

Spark 为具有阻塞请求的 rest API web 应用程序提供了一个微服务解决方案，也就是说，一个请求被困在 tread 中。因此，建议在此框架上构建的应用程序应简单。

要扩展使用此技术的应用程序，必须使用负载平衡器的微服务体系结构，并根据需要创建新实例。