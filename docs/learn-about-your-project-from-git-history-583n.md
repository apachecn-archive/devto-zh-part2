# 从 git 历史中了解您的项目

> 原文：<https://dev.to/piczmar_0/learn-about-your-project-from-git-history-583n>

当我进入一个新项目时，除了学习如何构建和运行它，我还喜欢检查需要注意什么。
像这样的事情:

*   代码是否干净且结构良好？
*   编写代码的开发人员还在吗？
*   哪些部位是最头疼的源头？

你可以从静态代码分析工具中找到这些信息(例如，对于 Java: [FindBugs](http://findbugs.sourceforge.net/) ， [PMD](https://github.com/pmd/pmd) ， [CheckStyle](https://github.com/pmd/pmd) ，或者称为 [SonarLint](https://plugins.jetbrains.com/plugin/7973-sonarlint) 的 IntelliJ Idea 插件)

这些都分析了源代码的当前状态，但是看起来你可以从项目的历史中获得很多有价值的信息。

您可能会得到这样的信息:

*   开发的密集程度，例如，有多少人在代码上工作，在特定的时间范围内生产多少行代码。
*   编写了多少 bugs vs. features 相关的代码(假设每个提交消息对于每个特性和 bug 修复都有不同的标记)
*   在 bug 的上下文中，哪些文件更新得最频繁
*   哪些文件通常一起更新

已经有一些工具可以做到这一点(参见下面的参考资料部分)，但是自己进行这种分析相对简单。

[这里的](https://github.com/piczmar/git-code-stats)是一个用 Java 编写的简单应用程序，给定一个 git 存储库路径，它打印前 10 个最常提交的文件的列表以及提交的数量。

我用的是 [JGit](https://www.eclipse.org/jgit/) 库，这是 Git 在 Java 中的一个实现。

它的工作方式是我得到一个存储库的提交列表。

```
 public static Stream<RevCommit> getCommits(Git git) throws GitAPIException {
        return StreamSupport.stream(git.log().call().spliterator(), false);
    } 
```

然后，对于每次提交，我都引用修订树，它保存了这个修订中所有文件的信息。

```
public static Stream<ObjectId> getRevTrees(Stream<RevCommit> commitsStream) {
        return commitsStream
                .map(rev -> rev.getTree().getId());
    } 
```

对于每一棵这样的树，我将它与来自先前提交的树进行比较，并得到一个差异。

```
 public static List<DiffEntry> diff(Git git, ObjectId newCommit, ObjectId oldCommit) throws IOException {
        DiffFormatter df = new DiffFormatter(new ByteArrayOutputStream());
        df.setRepository(git.getRepository());
        return df.scan(newCommit, oldCommit);
    } 
```

我收集了所有提交中所有已更改文件的列表，最后按照文件路径对它们进行分组，并对出现的次数进行计数。最后，我将它们按照最常出现的路径进行分类。

例如，当你在流行的 [spring-framework](https://github.com/spring-projects/spring-framework) master 分支上运行应用程序时，你会得到如下结果:

```
...

build.gradle - 1374
src/asciidoc/index.adoc - 444
spring-core/src/main/java/org/springframework/core/annotation/AnnotationUtils.java - 375
spring-beans/src/main/java/org/springframework/beans/factory/support/DefaultListableBeanFactory.java - 374
spring-context/src/main/java/org/springframework/context/annotation/ConfigurationClassParser.java - 362
spring-webmvc/src/main/java/org/springframework/web/servlet/config/annotation/WebMvcConfigurationSupport.java - 332
spring-webmvc/src/main/java/org/springframework/web/servlet/mvc/method/annotation/RequestMappingHandlerAdapter.java - 323
spring-web/src/main/java/org/springframework/http/HttpHeaders.java - 323
spring-web/src/main/java/org/springframework/web/client/RestTemplate.java - 320
spring-beans/src/main/java/org/springframework/beans/factory/support/AbstractAutowireCapableBeanFactory.java - 320 
```

基于此，您可以想象哪些文件是首先要查看的文件。然后，您可能想检查它们在项目中用于向下钻取铲斗的位置。

参考资料:

*   [https://jqassistant . org/shadows-of-the-past-analysis-of-git-repositories/](https://jqassistant.org/shadows-of-the-past-analysis-of-git-repositories/)
*   [https://github.com/ngeor/git-analyze](https://github.com/ngeor/git-analyze)
*   [https://github.com/ejwa/gitinspector](https://github.com/ejwa/gitinspector)
*   [http://gitstats.sourceforge.net](http://gitstats.sourceforge.net)