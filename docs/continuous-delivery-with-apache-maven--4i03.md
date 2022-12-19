# 使用 Apache Maven 进行连续交付

> 原文：<https://dev.to/khmarbaise/continuous-delivery-with-apache-maven--4i03>

基于今天的需求，需要通过你的主(Git)/主干(SVN)上的每一个变更来持续交付你的工件。问题是你需要什么来用 Maven 实现这个？

通常你使用的是所谓的`SNAPSHOT`版本，这意味着你的版本看起来像`1.2.3-SNAPSHOT`并且表明你目前正致力于发布版本`1.2.3`。之后，您继续使用下一个快照版本，如`1.2.4-SNAPSHOT`等等。
根据您的环境，这一点已经改变，这意味着您在 master (Git)上所做的每个更改都需要生成一个新的发布版本，这意味着`1.2.3`，下一个将是`1.2.4`。开发将在可能需要快照版本的分支上进行。

让我们来看一看单个模块的 pom 文件，它看起来像这样:

```
<project
  xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.soebes.smpp</groupId>
    <artifactId>smpp</artifactId>
    <version>4.0.1</version>
  </parent>

  <groupId>com.soebes.examples.j2ee</groupId>
  <artifactId>domain</artifactId>
  <version>1.2.3-SNAPSHOT</version>
...
</project> 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在调用:
，则版本用`1.2.3-SNAPSHOT`定义

```
mvn clean package 
```

Enter fullscreen mode Exit fullscreen mode

你会制造出看起来像`domain-1.2.3-SNAPSHOT.jar`的神器。不幸的是，基于 Maven 哲学，这种工件会随着时间的推移而改变。这意味着如果你现在对你的代码做了一个改变，然后再做一次`mvn clean package`，它将创建相同的`domain-1.2.3-SNAPSHOT.jar`文件。如果你做一个`mvn clean install`来代替，并没有真正的区别。这只会将创建的工件安装到您的本地缓存`$HOME/.m2/repository`中。此外，如果你做了`mvn clean deploy`，区别仅仅是每次你在你的存储库管理器上做这个的时候，命名模式会有一点不同，这意味着工件看起来会像`domain-1.2.3-20180821.202345-1.jar`。所以最终藏物包含了一个时间戳。但是最重要的是`SNAPSHOT`版本不是不可变的，正如你可能已经意识到的那样。

因此，创建真正不可变版本的唯一方法是从 Maven 的角度创建发布。这意味着你必须使用没有`-SNAPSHOT`的版本。因此，您有一个 pom 文件，如下所示:

```
<project
  xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.soebes.smpp</groupId>
    <artifactId>smpp</artifactId>
    <version>4.0.1</version>
  </parent>

  <groupId>com.soebes.examples.j2ee</groupId>
  <artifactId>domain</artifactId>
  <version>1.2.3</version>
...
</project> 
```

Enter fullscreen mode Exit fullscreen mode

所以你可以做一个`mvn clean deploy`，并且你已经创建了一个不可变的发布工件`domain-1.2.3.jar`。如果你第二次尝试这样做，你的存储库经理会告诉你这是不可行的，因为根据定义发布是不可变的，这意味着你不能覆盖它们。因此，如果您对项目进行了更改/错误/修正，那么您必须更改版本号。好的，不是很复杂，只是把版本改成`1.2.4`提交，再做一个`mvn clean deploy`？目前为止还不错。

有些人不喜欢只修改一个版本的提交。所以问题是:有没有更便捷的方式来处理这件事？

从 [Maven 3.5.0+](https://maven.apache.org/docs/3.5.0/release-notes.html) (我推荐使用最新版本)开始，有一个更方便的方法来处理这个问题。你可以像下面这样写你的 pom 文件:

```
<project
  xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.soebes.smpp</groupId>
    <artifactId>smpp</artifactId>
    <version>4.0.1</version>
  </parent>

  <groupId>com.soebes.examples.j2ee</groupId>
  <artifactId>domain</artifactId>
  <version>${revision}</version>

...
</project> 
```

Enter fullscreen mode Exit fullscreen mode

所以现在您可以通过使用下面的代码简单地创建发布工件:

```
mvn -Drevision=1.2.3 clean package 
```

Enter fullscreen mode Exit fullscreen mode

这只会在项目的`target`目录中创建工件。如果您想将它们部署到一个存储库中，那么需要进行[增强](https://www.mojohaus.org/flatten-maven-plugin/) :

```
<project
  xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.soebes.smpp</groupId>
    <artifactId>smpp</artifactId>
    <version>4.0.1</version>
  </parent>

  <groupId>com.soebes.examples.j2ee</groupId>
  <artifactId>domain</artifactId>
  <version>${revision}</version>

  <build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>flatten-maven-plugin</artifactId>
        <version>1.0.0</version>
        <configuration>
          <updatePomFile>true</updatePomFile>
        </configuration>
        <executions>
          <execution>
            <id>flatten</id>
            <phase>process-resources</phase>
            <goals>
              <goal>flatten</goal>
            </goals>
          </execution>
          <execution>
            <id>flatten.clean</id>
            <phase>clean</phase>
            <goals>
              <goal>clean</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
</project> 
```

Enter fullscreen mode Exit fullscreen mode

所以从现在开始，您可以使用下面的命令来创建一个发布版本:

```
mvn -Drevision=1.2.3 clean deploy 
```

Enter fullscreen mode Exit fullscreen mode

但是当然一个版本只能用一次。因此，如果你想制作另一个版本，你必须改变版本，但现在真的很容易:

```
mvn -Drevision=1.2.4 clean deploy 
```

Enter fullscreen mode Exit fullscreen mode

目前有三个支持的属性可以在版本标签中使用:

1.  修订本
2.  sha1
3.  变更列表

在版本标记中只能使用这三个属性。你当然可以像这样使用它们的组合:

```
<version>${revision}${sha1}${changelist}</version> 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用不同的属性，Maven 会给你一个警告。我强烈建议用户注意这些警告，并清理它们。

那么多模块构建呢？在那里你也可以使用它们。父项将如下所示:

```
<project
  xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.soebes.smpp</groupId>
    <artifactId>smpp</artifactId>
    <version>4.0.1</version>
  </parent>

  <groupId>com.soebes.examples.j2ee</groupId>
  <artifactId>parent</artifactId>
  <version>${revision}</version>
  <packaging>pom</packaging>
  ..
  [enhancements described earlier]
  ..
  <modules>
    <module>webgui</module>
    <module>app</module>
    <module>domain</module>
    <module>service</module>
    <module>service-client</module>
    <module>appasm</module>
    <module>assembly</module>
    <module>shade</module>
  </modules>
</project> 
```

Enter fullscreen mode Exit fullscreen mode

比如一个孩子`domain`看起来会是这样的:

```
<project
  xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.soebes.examples.j2ee</groupId>
    <artifactId>parent</artifactId>
    <version>${revision}</version>
  </parent>

  <artifactId>domain</artifactId>
  ..
</project> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用命令:
从根级别构建整个项目

```
mvn -Drevision=1.7.0-SNAPSHOT clean package 
```

Enter fullscreen mode Exit fullscreen mode

输出如下所示:

```
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO]
[INFO] parent                                                             [pom]
[INFO] domain                                                             [jar]
[INFO] service-client                                                     [jar]
[INFO] webgui                                                             [war]
[INFO] service                                                            [ejb]
[INFO] app                                                                [ear]
[INFO] appasm                                                             [pom]
[INFO] shade                                                              [jar]
[INFO] assembly                                                           [pom]
[INFO]
[INFO] ------------------< com.soebes.examples.j2ee:parent >-------------------
[INFO] Building parent 1.7.0-SNAPSHOT                                     [1/9]
[INFO] --------------------------------[ pom ]---------------------------------
[INFO]
....
[INFO]
[INFO] -----------------< com.soebes.examples.j2ee:assembly >------------------
[INFO] Building assembly 1.7.0-SNAPSHOT                                   [9/9]
[INFO] --------------------------------[ pom ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:3.1.0:clean (default-clean) @ assembly ---
[INFO] Deleting /Users/kama/ws-git/javaee/assembly/target
[INFO]
[INFO] --- flatten-maven-plugin:1.0.0:clean (flatten.clean) @ assembly ---
[INFO] Deleting /Users/kama/ws-git/javaee/assembly/.flattened-pom.xml
[INFO]
[INFO] --- jacoco-maven-plugin:0.8.1:prepare-agent (default) @ assembly ---
[INFO] argLine set to -javaagent:/Users/kama/.m2/repository/org/jacoco/org.jacoco.agent/0.8.1/org.jacoco.agent-0.8.1-runtime.jar=destfile=/Users/kama/ws-git/javaee/assembly/target/jacoco.exec
[INFO]
[INFO] --- flatten-maven-plugin:1.0.0:flatten (flatten) @ assembly ---
[INFO] Generating flattened POM of project com.soebes.examples.j2ee:assembly:pom:1.7.0-SNAPSHOT...
[INFO]
[INFO] --- maven-site-plugin:3.7.1:attach-descriptor (attach-descriptor) @ assembly ---
[INFO] No site descriptor found: nothing to attach.
[INFO]
[INFO] --- maven-assembly-plugin:3.0.0:single (assemblies) @ assembly ---
[INFO] Reading assembly descriptor: jar-with-prod.xml
[INFO] Reading assembly descriptor: assembly.xml
[INFO] Reading assembly descriptor: jar-with-dev.xml
[INFO] Building jar: /Users/kama/ws-git/javaee/assembly/target/assembly-1.7.0-SNAPSHOT-prod.jar
[INFO] Building zip: /Users/kama/ws-git/javaee/assembly/target/assembly-1.7.0-SNAPSHOT-archive.zip
[INFO] Building jar: /Users/kama/ws-git/javaee/assembly/target/assembly-1.7.0-SNAPSHOT-dev.jar
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO]
[INFO] parent 1.7.0-SNAPSHOT .............................. SUCCESS [  1.535 s]
[INFO] domain ............................................. SUCCESS [  0.876 s]
[INFO] service-client ..................................... SUCCESS [  0.111 s]
[INFO] webgui ............................................. SUCCESS [  0.474 s]
[INFO] service ............................................ SUCCESS [  0.454 s]
[INFO] app ................................................ SUCCESS [  0.282 s]
[INFO] appasm ............................................. SUCCESS [  0.237 s]
[INFO] shade .............................................. SUCCESS [  0.443 s]
[INFO] assembly 1.7.0-SNAPSHOT ............................ SUCCESS [  1.238 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 6.292 s
[INFO] Finished at: 2018-08-26T15:57:12+02:00
[INFO] ------------------------------------------------------------------------ 
```

Enter fullscreen mode Exit fullscreen mode

所以现在通过使用下面的命令从整个项目中创建一个发布非常容易:

```
mvn -Drevision=1.7.0 clean deploy 
```

Enter fullscreen mode Exit fullscreen mode

这将导致发布部署到存储库管理器。现在让我们来看看如何在像 Jenkins 这样的持续集成解决方案中使用它。在 Jenkins 上，您经常会遇到在分支基础上生产工件的情况(也许是基于快照，以便能够将它们部署到测试环境中)。所以可以这样组合属性:

```
<project
  xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.soebes.smpp</groupId>
    <artifactId>smpp</artifactId>
    <version>4.0.1</version>
  </parent>

  <groupId>com.soebes.examples.j2ee</groupId>
  <artifactId>domain</artifactId>
  <version>${revision}${sha1}${changelist}</version>

  <properties>
    <revision>1.0.0</revision>
    <sha1/>
    <changelist/>
  </properties>
...
</project> 
```

Enter fullscreen mode Exit fullscreen mode

在您的 jenkins 管道中，您可以提取分支名称，并将其插入到构建调用中，如下所示:

```
mvn -Dsha1=-BRANCHNAME -Dchangelist=-SNAPSHOT clean deploy 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的优点是，在每个分支上，您使用不同的版本，这些版本通过`-BRANCHNAME`来区分，并且这些版本是快照。这很方便，因为所有存储库管理器都支持在一段时间后自动删除快照版本。