# 詹金斯管道，简洁的介绍

> 原文：<https://dev.to/juunp/jenkins-pipeline-succinct-presentation-1g1n>

要访问管道的可能性，您需要创建一个具有管道默认类型的项目。

流水线代码必须由`Pipeline {}`
开始进入这个，我们可以这样定义阶段:`stage('NameOfStage') {}`
进入这些阶段，我们可以这样定义多个步骤:`steps {}`
命令进入这些步骤。在阶段之前，您应该添加一个[代理](https://jenkins.io/doc/book/pipeline/syntax/#agent)来让 Jenkins 知道在哪里运行以及其他配置，如 [post](https://jenkins.io/doc/book/pipeline/syntax/#post) (定义步骤或阶段之后的其他操作)和 [tools](https://jenkins.io/doc/book/pipeline/syntax/#tools) (定义工具并将其放在`PATH`上)。

这与声明性管道匹配；

还有脚本化管道，它以:`node {}`
开始，然后，您可以向它添加阶段，但不能添加步骤。代码驻留在阶段中。

在运行任何与脚本相反的东西之前，描述性管道会自己检查源代码管理(git)。

在描述性管道中，您可以在步骤中添加一个`script {}`来执行一些代码，否则，您将被迫使用预先格式化的命令，如`sh mvn --version`、`echo Hello World!`等。

我们必须使用 [Groovy 语言](http://groovy-lang.org/)来编写脚本管道(或脚本块)。不同的 Java 库和[插件](https://plugins.jenkins.io/)可以根据需要添加到 Jenkins 中。
例如，要使用双类，就应该加上。我不知道，所以我试图用它来构建项目，一旦构建失败，我就查看控制台日志，看到一个添加类的链接。当您可以添加或删除库时，该链接会指向一个表单。您可以在“管理 Jenkins”=>“进行中的脚本批准”中找到此表格。

一些 [Jenkins 环境变量](https://wiki.jenkins.io/display/JENKINS/Building+a+software+project)也可以用在管道中:例如`env.BUILD_NUMBER`。

此外，管道的代码可以存储在 Jenkinsfile 中，该文件将提交到您的项目存储库中，将持续交付过程定义为项目的一部分。