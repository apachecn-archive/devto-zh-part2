# 让我们让 SpringBoot 应用程序启动更快

> 原文：<https://dev.to/bufferings/lets-make-springboot-app-start-faster-k9m>

## “春天有多快？”

是 Spring One Platform 2018 的一个环节。我看了视频，自己试了一下。所以我在这里介绍我所做的和结果。

如果你还没有看过，我建议你看一下这个视频。太有意思了。

[https://springone platform . io/2018/sessions/how-fast-is-spring-](https://springoneplatform.io/2018/sessions/how-fast-is-spring-)

## 今日源代码

[https://github.com/bufferings/spring-boot-startup-mybench](https://github.com/bufferings/spring-boot-startup-mybench)

↓我用的是 OpenJDK 11。

```
❯ java --version
openjdk 11.0.1 2018-10-16
OpenJDK Runtime Environment 18.9 (build 11.0.1+13)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.1+13, mixed mode) 
```

Enter fullscreen mode Exit fullscreen mode

↓可以这样运行所有的基准测试。这将需要一段时间，因为它运行所有的基准。

```
❯ ./mvnw clean package
❯ (cd benchmarks/; java -jar target/benchmarks.jar) 
```

Enter fullscreen mode Exit fullscreen mode

## 1。通量基线

↓我用 SpringInitializr 只用 Reactive Web 创建了一个项目。然后，我写了一个 WebMVC 风格的微型控制器。

```
@SpringBootApplication
@RestController
public class DemoApplication {

  @GetMapping("/")
  public String home() {
    return "Hello";
  }

  public static void main(String[] args) {
    SpringApplication.run(DemoApplication.class, args);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

↓Spring Boot 的版本是 2.1.0.RELEASE.

```
 <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent> 
```

Enter fullscreen mode Exit fullscreen mode

↓启动用了 2.938±0.287s/op。

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了检查启动时间的基线。让我们从这里开始。

## 2。web VC(web VC)

↓我就纳闷了 WebMVC 呢，不是 WebFlux？所以我试了一下。可能只是指 Tomcat 和 Netty 的比较吧？

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case02_Web                               ss   10  3.281 ± 0.342   s/op 
```

Enter fullscreen mode Exit fullscreen mode

WebFlux 是不是快了一点？

## 3。spring 上下文索引器

接下来，我尝试了 spring-context-indexer，它似乎可以创建组件索引。

```
 <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-indexer</artifactId>
            <optional>true</optional>
        </dependency> 
```

Enter fullscreen mode Exit fullscreen mode

↓嗯...慢一点？

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case03_WithContextIndexer                ss   10  3.063 ± 0.102   s/op 
```

Enter fullscreen mode Exit fullscreen mode

↓我查了一下`spring.components`，发现里面只有 1 个成分。我明白了...我应该尝试一个更大的项目，以了解效果。

```
#
#Sun Nov 04 18:42:59 JST 2018
com.example.DemoApplication=org.springframework.stereotype.Component 
```

Enter fullscreen mode Exit fullscreen mode

## 4。惰性初始化

尝试了惰性初始化。

```
@Configuration
public class LazyInitBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
  @Override
  public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
    for (String beanName : beanFactory.getBeanDefinitionNames()) {
      beanFactory.getBeanDefinition(beanName).setLazyInit(true);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

↓下面是结果。它变得稍微快了一点。

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case04_WithLazyInit                      ss   10  2.844 ± 0.129   s/op 
```

Enter fullscreen mode Exit fullscreen mode

## 5。NoVerify

用`-noverify` :
跑

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case05_WithNoVerifyOption                ss   10  2.582 ± 0.060   s/op 
```

Enter fullscreen mode Exit fullscreen mode

它变得快了一点。我不知道这是什么意思，所以我需要以后检查一下。

## 6。TieredStopAtLevel

用`-XX:TieredStopAtLevel=1` :
跑

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case06_WithTieredStopAtLevel1Option      ss   10  1.980 ± 0.037   s/op 
```

Enter fullscreen mode Exit fullscreen mode

呃，快多了！用了不到 2 秒。但我也不认识这面旗。所以我过会儿会检查它。

## 7。请显式指定 SpringConfigLocation

用`-Dspring.config.location=classpath:/application.properties` :
跑

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case07_WithSpringConfigLocationOption    ss   10  3.026 ± 0.139   s/op 
```

Enter fullscreen mode Exit fullscreen mode

嗯，变得更慢了。

## 8。关掉 JMX

用`-Dspring.jmx.enabled=false` :
跑

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case08_WithJmxDisabledOption             ss   10  2.877 ± 0.097   s/op 
```

Enter fullscreen mode Exit fullscreen mode

它变得稍微快了一点。

## 9。排除回溯

从这里开始，我尝试排除库。首先，不包括回退:

```
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webflux</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-logging</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-jdk14</artifactId>
        </dependency> 
```

Enter fullscreen mode Exit fullscreen mode

下面是结果:

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case09_WithoutLogback                    ss   10  2.904 ± 0.096   s/op 
```

Enter fullscreen mode Exit fullscreen mode

毫米...略有改善？

## 10。排除杰克逊

接下来是杰克逊

```
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webflux</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-json</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
        </dependency> 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case10_WithoutJackson                    ss   10  2.789 ± 0.093   s/op 
```

Enter fullscreen mode Exit fullscreen mode

它变得稍微快了一点。

## 11。排除 HibernateValidator

```
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webflux</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>hibernate-validator</artifactId>
                    <groupId>org.hibernate.validator</groupId>
                </exclusion>
            </exclusions>
        </dependency> 
```

Enter fullscreen mode Exit fullscreen mode

下面是结果:

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case11_WithoutHibernateValidator         ss   10  2.857 ± 0.084   s/op 
```

Enter fullscreen mode Exit fullscreen mode

也略有改善。

这是图书馆排斥的终结。

## 12。AppCDS

AppCDS(应用程序类数据共享)作为一项商业特性包含在 Oracle JDK 中。但是它从 OpenJDK 10 开始可用。

似乎 AppCDS 将信息转储到一个共享的归档中，因此启动时间变得更短。

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case12_WithAppCds                        ss   10  2.957 ± 0.079   s/op 
```

Enter fullscreen mode Exit fullscreen mode

毫米...并没有更快...然后我查了关于光盘的文章，找到了原因。

有了 SpringBoot FatJAR，库就超出了 CDS 的范围。

## 13。具有薄发射器的焊剂

呃，对不起，但是基准名称“爆炸”是错误的。有一次我试着去爆那个肥罐子，但是毕竟不能用爆了罐子的 CD。所以我改用瘦发射架。请将基准名称“爆炸”作为“瘦发射器”。

在使用光盘之前，我想检查一下用瘦启动器打包的 JAR 文件的速度。

```
 <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <dependencies>
                    <dependency>
                        <groupId>org.springframework.boot.experimental</groupId>
                        <artifactId>spring-boot-thin-layout</artifactId>
                        <version>1.0.15.RELEASE</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins> 
```

Enter fullscreen mode Exit fullscreen mode

虽然我用 Thin Launcher 打包 app，但是我没有用 Thin Launcher 的 launch 类，而是指定 Main 类，让启动时间尽可能快。

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case13_Exploded                          ss   10  2.476 ± 0.091   s/op 
```

Enter fullscreen mode Exit fullscreen mode

哼，快了一点，不是吗？

## 14。瘦启动器+光盘

现在，我想将 AppCDS 应用于它。

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case14_ExplodedWithAppCds                ss   10  1.535 ± 0.036   s/op 
```

Enter fullscreen mode Exit fullscreen mode

哇！它变得快多了！

## 15。全部应用

最后，我应用了所有的东西。

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case15_AllApplied                        ss   10  0.801 ± 0.037   s/op 
```

Enter fullscreen mode Exit fullscreen mode

不到 1 秒！(∩ ∀`)∩yay

## 还差一步

在 Dave 的会议中，他提到了“功能 Bean 定义”，尝试在没有 SpringBoot 的情况下对 Spring 进行改进，应用程序变得更快了。我需要学习更多来理解他们。

## 结果列表

```
Benchmark                                          Mode  Cnt  Score   Error  Units
MyBenchmark.case01_FluxBaseline                      ss   10  2.938 ± 0.287   s/op
MyBenchmark.case02_Web                               ss   10  3.281 ± 0.342   s/op
MyBenchmark.case03_WithContextIndexer                ss   10  3.063 ± 0.102   s/op
MyBenchmark.case04_WithLazyInit                      ss   10  2.844 ± 0.129   s/op
MyBenchmark.case05_WithNoVerifyOption                ss   10  2.582 ± 0.060   s/op
MyBenchmark.case06_WithTieredStopAtLevel1Option      ss   10  1.980 ± 0.037   s/op
MyBenchmark.case07_WithSpringConfigLocationOption    ss   10  3.026 ± 0.139   s/op
MyBenchmark.case08_WithJmxDisabledOption             ss   10  2.877 ± 0.097   s/op
MyBenchmark.case09_WithoutLogback                    ss   10  2.904 ± 0.096   s/op
MyBenchmark.case10_WithoutJackson                    ss   10  2.789 ± 0.093   s/op
MyBenchmark.case11_WithoutHibernateValidator         ss   10  2.857 ± 0.084   s/op
MyBenchmark.case12_WithAppCds                        ss   10  2.957 ± 0.079   s/op
MyBenchmark.case13_Exploded                          ss   10  2.476 ± 0.091   s/op
MyBenchmark.case14_ExplodedWithAppCds                ss   10  1.535 ± 0.036   s/op
MyBenchmark.case15_AllApplied                        ss   10  0.801 ± 0.037   s/op 
```

Enter fullscreen mode Exit fullscreen mode

真的很有趣。谢谢大家！