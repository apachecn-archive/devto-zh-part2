# 用 Sparrowdo 构建 VSTS YAML 定义

> 原文：<https://dev.to/melezhik/building-vsts-yaml-definitions-with-sparrowdo-3opd>

# YAML 建立定义

今天，VSTS 提供了 [YAML DSL](https://github.com/Microsoft/vsts-agent/blob/master/docs/preview/YAMLgettingstarted.md) 来创建构建定义，而不是在 Web UI 中创建。

我喜欢这个想法，尽管为多个项目维护 YAML 代码会很快变得乏味。微软在这条路上提供了一些助手，像[模板](https://github.com/Microsoft/vsts-agent/blob/master/docs/preview/YAMLgettingstarted.md#templates)来重用你的构建定义，但是让我展示合理的替代方案。

# 欢迎来到斯帕罗多 VSTS-*家族

Sparrowdo::VSTS 是 Sparrowdo 模块的一个子集，用于为您的 VSTS 构建定义启动/生成 YAML 代码。 [Sparrowdo](https://github.com/melezhik/sparrowdo) 本身是一个构建在 Perl5/Perl6 基础上的灵活而强大的任务运行器/配置管理工具。

# 将 Sparrowdo 转入任务运行器

最初 Sparrowdo 是作为配置管理工具编写的，用于通过 ssh 运行远程场景，但是 Sparrowdo 可以通过设置几个选项轻松地转换为本地任务运行器:

```
$ sparrowdo --local_mode --no_sudo 
```

Enter fullscreen mode Exit fullscreen mode

这使得 Sparrowdo 客户端以本地模式(在本地主机上)运行，并在执行命令时省略`sudo`，因为这对于代码生成任务是不需要的。

# 让编写代码创建一个代码

该方法的主要思想是为 YAML 构建定义创建代码生成器，而不是手动创建 YAML。使用高层 Perl6 抽象，可以生成 YAML 代码定义(`build.yaml`)，然后将更改提交到 Git 存储库，最后使用生成的构建定义文件运行构建。

以下是总体设计示意图:

```
 1\. Sparrowdo generator ( sparrowfile ):

+-----------------------------------------+
|                                         |
|  < build header >                       |
|      agent-info                         |
|      repo-info                          |
+-----------------------------------------+
|  < tasks >                              |
|                                         |
|      task1 <===> Sparrowdo Module Call  |
|      task2 <===> Sparrowdo Module Call  |
|      task3 <===> Sparrowdo Module Call  |
|      task4 <===> Sparrowdo Module Call  |
|      task5 <===> Sparrowdo Module Call  |
|      ... so on                          |
+-----------------------------------------+

2\. Outcome => build.yaml file ( build definition ) 
```

Enter fullscreen mode Exit fullscreen mode

# 建立表头

每个 YAML 构建定义都应该包含关于在哪里运行构建的基本信息，我们称之为构建头，他的 Sparrowdo 表示是这样的 Sparrowdo::VSTS::YAML::构建模块:

```
 $ nano sparrowfile

#!perl6

module_run "VSTS::YAML::Build", %(
  build-dir => "cicd/build",
  agent-name => "BuildServer01", # default value is not set
  queue => "Queue01",
  timeout => 10, # build timeout 10 minutes, this is default value
); 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了所有必需的和可选的参数，就像 Perl6 函数一样，其他步骤也可以用同样的方式表达。这里，我们希望在名为“Queue01”的队列中，在名为“BuildServer01”的代理上调整我们的构建，最长执行时间为 10 分钟。

# 构建任务

正如我所说的，每个 VSTS 任务只是一个适当的 Sparrowdo::VSTS::*模块调用，考虑一个简单的场景来构建 **dotnet** 项目并将工件发布到 VSTS 存储，这意味着以下步骤:

1.  检查源代码(由 VSTS 自己保证)
2.  安装 nuget 和 nuget 依赖项
3.  构建解决方案
4.  发布工件

以下是实施细节:

## 安装 nuget 和 nuget 依赖项

```
 $ nano sparrowfile

#!perl6

module_run "VSTS::YAML::Nuget", %(
  build-dir => "cicd/build",
  working-folder => ".", # path to project
  solution => "app.sln", # path to solution file, default value
); 
```

Enter fullscreen mode Exit fullscreen mode

## 构建解决方案

```
 $ nano sparrowfile

#!perl6

module_run "VSTS::YAML::Solution", %(
  vs-version  => '15.0', # visual  studio version, default value
  build-dir => "cicd/build",
  solution => "app.sln", # path to solution file, default is "**\*.sln"
  platform => "x86",
  configuration => "debug",
  restore-solution => "app.sln", # path to NugetRestore solution file
  test-assemblies => True, # run tests, default value is False
  publish-symbols => False, # publish symbols, this is default value
); 
```

Enter fullscreen mode Exit fullscreen mode

## 发布神器

```
 $ nano sparrowfile

#!perl6

module_run "VSTS::YAML::Artifact", %(
  build-dir => "cicd/build",
  artifact-name => "drop",
  path => "'\$(build.artifactstagingdirectory)'",
  publish-location => "Container" # default value
); 
```

Enter fullscreen mode Exit fullscreen mode

# 最终版本

下面是我们如何从头开始创建 YAML 构建定义:

```
 $ git clone $repo-url app && cd app # cloning source code of application to build

$ zef install Sparrowdo::VSTS::YAML::Build Sparrowdo::VSTS::YAML::Nuget Sparrowdo::VSTS::YAML::Solution Sparrowdo::VSTS::YAML::Artifact # Install proper Sparrowdo modules

$ nano sparrowfile # create build scenario mentioned in previous paragraphs

$ mkdir -p cicd/build # create directory to hold internal build files

$ sparrowdo --local_mode --no_sudo # generate build definition

$ echo ".cache" >> .gitingore # ask git to skip .cache files inside cicd/build dir

$ git add sparrowfile cicd/ .gitingore  && git commit -a -m "YAML build definition" # commit outcome

$ git push # push changes 
```

Enter fullscreen mode Exit fullscreen mode

你可以去 VSTS 的网络用户界面，创建基于 YAML 的管道指向`cicd/build/build.yaml`作为构建场景，我们都设置好了！