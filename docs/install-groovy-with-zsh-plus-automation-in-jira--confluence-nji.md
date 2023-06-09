# 用 ZSH 安装 Groovy(加上吉拉和 Confluence 的自动化)

> 原文：<https://dev.to/preciselyalyss/install-groovy-with-zsh-plus-automation-in-jira--confluence-nji>

**完全披露**:我为亚特兰蒂斯人工作。因此，我花了大量时间创建、更新和阅读 Confluence 页面。虽然 Confluence server 和 cloud 都有可用于以编程方式更新/创建页面的 API，但 Adaptavist 创建了一个名为 [ScriptRunner](https://marketplace.atlassian.com/apps/1215215/scriptrunner-for-confluence) 的应用程序，它通过内置脚本和预写逻辑使这一过程变得更容易。我的最终目标是通过检查吉拉的更新或另一个 confluence 页面来自动化一些常见的、重复出现的任务。大多数 Atlassian 应用程序是用 Java 编写的，ScriptRunner 使用一种叫做 Groovy 的 Java。

## 第一步:在 OS X 上安装 Groovy

对于自制软件用户:

```
brew install groovy 
```

Enter fullscreen mode Exit fullscreen mode

对于 MacPorts 用户:

```
sudo port install groovy 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:设置 Groovy_home 环境变量

欢迎您完全在 zsh 中这样做，但是我手动将它添加到我的`~/.zshrc`文件中。另一个注意事项是我已经将 Sublime 设置为从命令行启动。如果您喜欢 vim、emacs 或其他文本编辑器，您将需要替换对`subl`的引用。

```
subl ~/.zshrc 
```

Enter fullscreen mode Exit fullscreen mode

在`~/.zshrc`中，我添加了以下变量导出和注释:

```
# Add Groovy Home path
export GROOVY_HOME="$(/usr/local/opt/groovy/libexec)" 
```

Enter fullscreen mode Exit fullscreen mode

保存此文件，完成后可以关闭它。

## 步骤 3:运行一个测试来检查你的 Groovy 安装

```
mkdir groovy-test && cd groovy-test
subl hello.groovy 
```

Enter fullscreen mode Exit fullscreen mode

现在，向您刚刚创建的空文件添加一个测试脚本:

```
//hello.groovy
println "hello, world"
for (arg in this.args ) {
    println "Argument:" + arg;
}
// this is a comment
/* a block comment, commenting out an alternative to above:
this.args.each{ arg -> println "hello, ${arg}"}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

保存此文件，完成后可以关闭它。

使用以下命令运行它:

```
groovy hello.groovy MyName yourName HisName 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步:在吉拉或 Confluence 中开始使用 Groovy 自动化任务

正如我之前提到的，Adaptavist 提供了一些内置脚本，但是我发现我的需求更加复杂。网上有一些很好的例子可以帮助你开始。

*   [创建合流页面](https://gist.github.com/jechlin/cf9ccb524918993872d5)
*   [融合云的 ScriptRunner 示例](http://scriptrunner-docs.connect.adaptavist.com/confluencecloud/script-listeners.html#_examples)
*   【Bitbucket、Bamboo、Confluence 和吉拉的 ScriptRunner 示例
*   [吉拉 ScriptRunner 行为的最佳实践](http://www.coyotecrk.com/best-practices-scriptrunner-behaviours/)