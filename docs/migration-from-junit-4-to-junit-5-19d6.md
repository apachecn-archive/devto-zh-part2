# 从 Junit 4 迁移到 Junit 5

> 原文：<https://dev.to/vga/migration-from-junit-4-to-junit-5-19d6>

在我从事当前项目的时候，我花了一些时间从 [JUnit 4](https://junit.org/junit4/) 迁移到 [JUnit 5](https://junit.org/junit5/) 。
由于 JUnit 5 于 2017 年 9 月发布，现在是时候来看看它了。

我的应用程序是一个 java 8 maven 项目，分为 7 个 maven 模块，每个模块都有自己的集成和单元测试。然而，其中一个模块是专门用于测试的。它包含所有测试所需的依赖项，并作为范围测试注入到其他模块中。我们的测试依赖在 Java 项目中是最常见的。我们使用 JUnit 4、 [AssertJ](https://joel-costigliola.github.io/assertj/) 、 [Mockito](http://site.mockito.org/) 、 [DbUnit](http://dbunit.sourceforge.net/) 和 [Spring Test](https://docs.spring.io/spring/docs/current/spring-framework-reference/testing.html) 。

最后，我们还有一个专门的项目来运行基于 [Selenium](https://www.seleniumhq.org/) 、 [Fluentlenium](http://fluentLenium.org/) 和 [JGiven](http://jgiven.org/) 的端到端测试。
遗憾的是，JGiven 并不完全支持 JUnit 5。它目前处于[实验状态](http://jgiven.org/userguide/#_junit_5_experimental)，所以我还没有开始这个迁移。

## 依赖关系

让我们从添加新的 JUnit 依赖项开始:

```
<dependency>
   <groupId>org.junit.jupiter</groupId>
   <artifactId>junit-jupiter-engine</artifactId>
   <version>${junit.version}</version>
</dependency>
<dependency>
   <groupId>org.junit.vintage</groupId>
   <artifactId>junit-vintage-engine</artifactId>
   <version>${junit.version}</version>
</dependency>
<dependency>
   <groupId>org.junit.platform</groupId>
   <artifactId>junit-platform-launcher</artifactId>
   <version>${junit.platform.version}</version>
</dependency>
<dependency>
   <groupId>org.junit.platform</groupId>
   <artifactId>junit-platform-runner</artifactId>
   <version>${junit.platform.version}</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是`junit-vintage-engine`的导入。它提供了毫无困难地同时运行 JUnit 4 测试和 JUnit 5 测试的能力。

## 单元测试

下一步是用最新的注释替换所有旧的 JUnit 注释。

```
import org.junit.Test 
```

Enter fullscreen mode Exit fullscreen mode

变成

```
import org.junit.jupiter.api.Test; 
```

Enter fullscreen mode Exit fullscreen mode

下面是每个注释的映射:

| 6 月 4 日 | Junit 5 |
| --- | --- |
| 之前 | org.junit .木星. api.BeforeEach |
| org.junit.After | org.junit .木星. api.After |
| org.junit.BeforeClass | org . JUnit . Jupiter . API . before all |
| org.junit.AfterClass | org.junit.jupiter.api.AfterAll |
| org.junit.Ignore | 已禁用 |

因为我们对所有断言都使用 AssertJ，所以我不需要迁移 JUnit 4 断言。

## 规则

一个最大的变化是取消了[规则](http://www.codeaffine.com/2012/09/24/junit-rules/)的概念，取而代之的是[扩展模型](https://junit.org/junit5/docs/current/user-guide/#extensions)。扩展的目的是扩展测试类或方法的行为，它取代了 JUnit runner 和 JUnit 规则。

我们都用过的一个规则是`ExpectedException`，它很容易被 JUnit `assertThrows` :
所取代

```
 @Test
    void exceptionTesting() {
        Throwable exception = assertThrows(IllegalArgumentException.class, () -> {
            throw new IllegalArgumentException("a message");
        });
        assertEquals("a message", exception.getMessage());
    } 
```

Enter fullscreen mode Exit fullscreen mode

另一个众所周知的迁移规则是`TemporaryFolder`。不幸的是，JUnit 5 还没有提供替代品。Github 中有一个公开的[问题](http://github.com/junit-team/junit5/issues/1247)。

# [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) 介绍一个临时文件夹扩展 #1247](https://github.com/junit-team/junit5/issues/1247) 

[![sbrannen avatar](img/98bc9cb1e14cf24921373bcd5908e2a0.png)](https://github.com/sbrannen) **[sbrannen](https://github.com/sbrannen)** posted on [<time datetime="2018-01-18T14:44:07Z">Jan 18, 2018</time>](https://github.com/junit-team/junit5/issues/1247)

## 概述

参见在[https://github.com/junit-team/junit5-samples/issues/4](https://github.com/junit-team/junit5-samples/issues/4)的讨论。

### 相关问题

*   #219

## 交付成果

*   [X] ~~为 JUnit Jupiter 引入一个官方的`TemporaryFolder`扩展，类似于 JUnit 4 中的规则支持。~~

[View on GitHub](https://github.com/junit-team/junit5/issues/1247)

那么我们能做些什么来让它工作呢？

首先，多亏了`junit-vintage-engine`，可以在 JUnit 4 中使用这些规则进行测试。

另一个解决方案是通过添加依赖关系`junit-jupiter-migrationsupport`来继续使用 JUnit 4 `TemporaryFolder`规则。

```
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-migrationsupport</artifactId>
    <version>${junit.version}</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

这个模块支持使用规则运行 JUnit 5 测试。例如:

```
@EnableRuleMigrationSupport
public class JUnit4TemporaryFolderTest {

    @Rule
    public TemporaryFolder temporaryFolder = new TemporaryFolder();

    @Test
    public void test() throws IOException {
        temporaryFolder.newFile("new_file");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，此功能仅支持:

*   扩展`org.junit.rules.ExternalResource`的规则
*   扩展`org.junit.rules.Verifier`的规则
*   规则`ExpectedException`目前被标记为[实验性的](https://junit.org/junit5/docs/current/user-guide/#migrating-from-junit4-rule-support)，所以使用它的风险由你自己承担。

最后，一个解决方案是基于 Junit 4 实现创建我们自己的 [`TemporaryFolderExtension`](https://gist.github.com/vgallet/8b52247c4cc57c6bc96923daff630fc1) 。

```
public class TemporaryFolderExtension implements BeforeEachCallback, AfterEachCallback {

   private final File parentFolder;
   private File folder;

   public TemporaryFolderExtension() {
       this(null);
   }

   public TemporaryFolderExtension(File parentFolder) {
       this.parentFolder = parentFolder;
   }

   @Override
   public void afterEach(ExtensionContext extensionContext) {
       if (folder != null) {
           recursiveDelete(folder);
       }
   }

   @Override
   public void beforeEach(ExtensionContext extensionContext) throws IOException {
       folder = File.createTempFile("junit", "", parentFolder);
       folder.delete();
       folder.mkdir();
   }

   public File newFile(String fileName) throws IOException {
       File file = new File(getRoot(), fileName);
       if (!file.createNewFile()) {
           throw new IOException("a file with the name \'" + fileName + "\' already exists in the test folder");
       }
       return file;
   }

   public File newFolder(String folderName) {
       File file = getRoot();
       file = new File(file, folderName);
       file.mkdir();
       return file;
   }

   private void recursiveDelete(File file) {
       File[] files = file.listFiles();
       if (files != null) {
           for (File each : files) {
               recursiveDelete(each);
           }
       }
       file.delete();
   }

   public File getRoot() {
       if (folder == null) {
           throw new IllegalStateException("the temporary folder has not yet been created");
       }
       return folder;
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

这个实现并不完全支持所有的扩展特性，比如[参数解析](https://junit.org/junit5/docs/current/user-guide/#extensions-parameter-resolution)，但是至少，它允许我们将测试完全迁移到 JUnit 5。
此外，可以通过使用 [`@RegisterExtension`](https://junit.org/junit5/docs/current/user-guide/#extensions-registration-programmatic)
注入扩展作为规则

```
@RegisterExtension
public TemporaryFolderExtension temporaryFolder = new TemporaryFolderExtension(); 
```

Enter fullscreen mode Exit fullscreen mode

这个注释使我们能够用参数构建一个扩展，并在测试执行期间访问 is。

## 自定义规则

在我的例子中，我只有一个定制规则需要迁移。它的目标是创建一个内存中的 SMTP 服务器来断言发送电子邮件。

```
public class SMTPServerRule extends ExternalResource {

   private GreenMail smtpServer;
   private String hostname;
   private int port;

   public SMTPServerRule() {
       this(25);
   }

   public SMTPServerRule(int port) {
       this("localhost", port);
   }

   public SMTPServerRule(String hostname, int port) {
       this.hostname = hostname;
       this.port = port;
   }

   @Override
   protected void before() throws Throwable {
       super.before();

       smtpServer = new GreenMail(new ServerSetup(port, hostname, "smtp"));
       smtpServer.start();
   }

   public List<ExpectedMail> getMessages() {
       return Lists.newArrayList(smtpServer.getReceivedMessages()).stream()
           .parallel()
           .map(mimeMessage -> ExpectedMail.transformMimeMessage(mimeMessage)).collect(Collectors.toList());
   }

   @Override
   protected void after() {
       super.after();
       smtpServer.stop();
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

要让它像 JUnit 扩展一样工作，它只需要实现`BeforeEachCallback`和`AfterEachCallback`接口，而不是从`ExternalResource`继承。主要的实现还是一样的。

```
public class SMTPServerExtension implements BeforeEachCallback, AfterEachCallback {

   private GreenMail smtpServer;
   private String hostname;
   private int port;

   public SMTPServerExtension() {
       this(25);
   }

   public SMTPServerExtension(int port) {
       this("localhost", port);
   }

   public SMTPServerExtension(String hostname, int port) {
       this.hostname = hostname;
       this.port = port;
   }

   public List<ExpectedMail> getMessages() {
       return Lists.newArrayList(smtpServer.getReceivedMessages()).stream()
           .parallel()
           .map(mimeMessage -> ExpectedMail.transformMimeMessage(mimeMessage)).collect(Collectors.toList());
   }

   @Override
   public void afterEach(ExtensionContext extensionContext) throws Exception {
       smtpServer.stop();
   }

   @Override
   public void beforeEach(ExtensionContext extensionContext) throws Exception {
       smtpServer = new GreenMail(new ServerSetup(port, hostname, "smtp"));
       smtpServer.start();
   } 
```

Enter fullscreen mode Exit fullscreen mode

## 集成测试

接下来，我必须更新 Spring 集成测试，这很容易，因为 Spring 5 中包含了类`SpringExtension`。

```
@RunWith(SpringJUnit4ClassRunner.class) 
```

Enter fullscreen mode Exit fullscreen mode

变成

```
@ExtendWith(SpringExtension.class) 
```

Enter fullscreen mode Exit fullscreen mode

## 莫克托测试

让我们继续使用 Mockito 的测试。就像我们对 Spring 集成测试所做的那样，我们必须注册一个扩展:

```
@RunWith(MockitoJUnitRunner.class) 
```

Enter fullscreen mode Exit fullscreen mode

变成

```
@ExtendWith(MockitoExtension.class) 
```

Enter fullscreen mode Exit fullscreen mode

事实上，Mockito 还没有提供类`MockitoExtension`,它将在 Mockito 3 中引入。
一个解决方案与`TemporaryFolderExtension`相同...那就是将我们的测试保留在 JUnit 4 中。然而，也可以创建我们自己的扩展，因此 Junit 团队在其示例中给出了一个 [`MockitoExtension`](https://github.com/junit-team/junit5-samples/blob/r5.1.1/junit5-mockito-extension/src/main/java/com/example/mockito/MockitoExtension.java) 的实现。我决定将它导入到我的项目中来完成我的迁移。

## 删除 JUnit 4

然后，为了确保我的所有测试都在 JUnit 5 下运行，我通过执行:
来检查是否存在任何 JUnit 4 依赖性

```
mvn dependency:tree 
```

Enter fullscreen mode Exit fullscreen mode

所以，我不得不排除其中的一些

```
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.dbunit</groupId>
            <artifactId>dbunit</artifactId>
            <version>${dbunit.version}</version>
            <exclusions>
                <exclusion>
                    <groupId>junit</groupId>
                    <artifactId>junit</artifactId>
                </exclusion>
            </exclusions>
        </dependency> 
```

Enter fullscreen mode Exit fullscreen mode

## [胃](#maven)

最后但同样重要的是，我需要更新 maven surefire 插件，使其能够与 JUnit 5 兼容。

```
<!--
        The Surefire Plugin is used during the test phase of the build lifecycle to execute the unit tests of an application.
        -->
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>${maven-surefire-plugin.version}</version>
            <dependencies>
                <dependency>
                    <groupId>org.junit.platform</groupId>
                    <artifactId>junit-platform-surefire-provider</artifactId>
                    <version>1.1.1</version>
                </dependency>
                <dependency>
                    <groupId>org.junit.jupiter</groupId>
                    <artifactId>junit-jupiter-engine</artifactId>
                    <version>${junit.version}</version>
                </dependency>
            </dependencies>
        </plugin> 
```

Enter fullscreen mode Exit fullscreen mode

小心你的 maven surefire 插件的版本，因为`2.20`有一个[内存泄漏](https://github.com/junit-team/junit5/issues/809)。 [JUnit 文档](https://junit.org/junit5/docs/current/user-guide/#running-tests-build-maven)建议版本`2.21`。

## 结论

这种迁移非常容易，但即便如此，JUnit 5 与 JUnit 4 完全不同。最后，我能够删除`junit-vintage-engine`的导入，因为我不再有 Junit 4 测试了。我唯一遗憾的是，我不得不创建自己的临时文件夹扩展和 Mockito 扩展。
最后，通过咨询[6 月 5 日-样本](https://github.com/junit-team/junit5-samples)，您有可能获得更多的迁移帮助。

非常感谢[索尼斯](https://www.linkedin.com/in/sonyth-huber-57854424/)、[米凯尔](https://twitter.com/TsubaR00ck)和[胡赛姆](https://twitter.com/7ouss3m)的时间和校对。