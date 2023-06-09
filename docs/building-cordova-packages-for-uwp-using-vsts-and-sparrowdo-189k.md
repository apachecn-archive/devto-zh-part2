# 使用 VSTS 和 Sparrowdo 为 UWP 构建科尔多瓦包

> 原文：<https://dev.to/melezhik/building-cordova-packages-for-uwp-using-vsts-and-sparrowdo-189k>

# 科尔多瓦和 UWP

Cordova 是一个移动应用开发框架。它是一个跨平台的工具，你可以为各种平台(Windows/Android/Apple)创建包。让我们为 cordova 应用程序创建 [UWP](https://docs.microsoft.com/en-us/windows/uwp/get-started/universal-application-platform-guide) 包，这真的很简单。

# VSTS 和斯帕罗多

我们将使用 [VSTS](https://visualstudio.microsoft.com/team-services/) 作为 CICD 框架，使用 [Sparrowdo](https://github.com/melezhik/sparrowdo) 作为我们构建的代码生成器。

我们将使用所谓的 [YAML 构建定义](https://docs.microsoft.com/en-us/vsts/pipelines/build/yaml?view=vsts)来创建构建场景。这种方法允许我们在源代码中保留构建逻辑，并将构建视为代码。

我们使用 [Sparrowdo](https://github.com/melezhik/sparrowdo) 作为高级 Perl6 API 来生成 YAML 构建定义，而不是手动创建那些定义。

## 生成构建定义

首先让我们安装 Sparrowdo 和相关的 Sparrowdo::VSTS::YAML::*依赖项，这些是 Perl6 模块:

```
 $ zef install Sparrowdo Sparrowdo::VSTS::YAML::Build Sparrowdo::VSTS::YAML::Cordova VSTS::YAML::Artifact 
```

Enter fullscreen mode Exit fullscreen mode

然后让创建 Sparrowdo 场景来构建构建定义:

```
$ nano cicd/Builds/uwp/sparrowfile 
```

Enter fullscreen mode Exit fullscreen mode

```
#!perl6

module_run "VSTS::YAML::Build", %(
  build-dir => "cicd/Builds/uwp",
  queue => "Q-one",
  timeout => 20,
);

module_run "VSTS::YAML::Cordova", %(
  build-dir => "cicd/Builds/uwp",
  build-configuration => "debug",
);

module_run "VSTS::YAML::Artifact", %(
  build-dir => "cicd/Builds/uwp",
  artifact-name => "drop",
  path => "platforms/windows/AppPackages",
  publish-location => "Container"
); 
```

Enter fullscreen mode Exit fullscreen mode

这个高级代码生成所有必要的构建基础设施，我们都设置好了:

```
$ sparrowdo --localhost --no_sudo --sparrowfile=builds/uwp/sparrowfile 
```

Enter fullscreen mode Exit fullscreen mode

```
running sparrow tasks on 127.0.0.1 ...
target OS is - centos7
enter module <VSTS::YAML::Build> ...
push [task] delete directory cicd/Builds/uwp/files OK
push [task] delete directory cicd/Builds/uwp/.cache OK
push [task] create directory cicd/Builds/uwp/.cache OK
push [task] create directory cicd/Builds/uwp/files OK
push [task] create template cicd/Builds/uwp/build.yaml OK
enter module <VSTS::YAML::Cordova> ...
push [task] create directory cicd/Builds/uwp/.cache OK
push [task] create directory cicd/Builds/uwp/files OK
push [task] create file cicd/Builds/uwp/files/npm-install.cmd OK
push [task] create file cicd/Builds/uwp/files/npm-install-cordova.cmd OK
push [task] create file cicd/Builds/uwp/files/platform-add.cmd OK
push [task] create file cicd/Builds/uwp/files/set-version.pl OK
push [task] create file cicd/Builds/uwp/files/prepare.cmd OK
push [task] create file cicd/Builds/uwp/files/clean-build-dir.cmd OK
push [task] create template cicd/Builds/uwp/files/build.cmd OK
push [task] create template cicd/Builds/uwp/.cache/build.yaml.sample OK
push [task] run bash: cat cicd/Builds/uwp/.cache/build.yaml.sample >> ci ... OK
enter module <VSTS::YAML::Artifact> ...
push [task] create directory cicd/Builds/uwp/.cache OK
push [task] create directory cicd/Builds/uwp/files OK
push [task] create template cicd/Builds/uwp/.cache/build.yaml.sample OK
push [task] run bash: cat cicd/Builds/uwp/.cache/build.yaml.sample >> ci ... OK
SPL file /opt/sparrow/sparrow.list is empty
get index updates from SparrowHub ... OK
set up task box file - /home/gimy/.sparrowdo//opt/sparrow/task-box.json - OK
public@directory is uptodate (0.1.6)
public@templater is uptodate (0.0.13)
Installing modules using /opt/sparrow/plugins/public/templater/cpanfile
Complete! Modules were installed into /opt/sparrow/plugins/public/templater/local
public@file is uptodate (0.0.6)
public@bash is uptodate (0.1.8)
running task box from /opt/sparrow/sparrow-cache/task-box.json ...
2018-08-13 20:48:23 : [task] delete directory cicd/Builds/uwp/files [path] modules/delete/
2018-08-13 20:48:23 : [task] delete directory cicd/Builds/uwp/.cache [path] modules/delete/
2018-08-13 20:48:24 : [task] create directory cicd/Builds/uwp/.cache [path] modules/create/
2018-08-13 20:48:24 : [task] create directory cicd/Builds/uwp/files [path] modules/create/
2018-08-13 20:48:24 : [task] create template cicd/Builds/uwp/build.yaml [path] modules/generate-content/
2018-08-13 20:48:24 : [task] create template cicd/Builds/uwp/build.yaml [path] / [msg] updated ok
2018-08-13 20:48:25 : [task] create directory cicd/Builds/uwp/.cache [path] modules/create/
2018-08-13 20:48:25 : [task] create directory cicd/Builds/uwp/files [path] modules/create/
2018-08-13 20:48:25 : [task] create file cicd/Builds/uwp/files/npm-install.cmd [path] /
2018-08-13 20:48:25 : [task] create file cicd/Builds/uwp/files/npm-install-cordova.cmd [path] /
2018-08-13 20:48:26 : [task] create file cicd/Builds/uwp/files/platform-add.cmd [path] /
2018-08-13 20:48:26 : [task] create file cicd/Builds/uwp/files/set-version.pl [path] /
2018-08-13 20:48:27 : [task] create file cicd/Builds/uwp/files/prepare.cmd [path] /
2018-08-13 20:48:27 : [task] create file cicd/Builds/uwp/files/clean-build-dir.cmd [path] /
2018-08-13 20:48:27 : [task] create template cicd/Builds/uwp/files/build.cmd [path] modules/generate-content/
2018-08-13 20:48:28 : [task] create template cicd/Builds/uwp/files/build.cmd [path] / [msg] updated ok
2018-08-13 20:48:28 : [task] create template cicd/Builds/uwp/.cache/build.yaml.sample [path] modules/generate-content/
2018-08-13 20:48:28 : [task] create template cicd/Builds/uwp/.cache/build.yaml.sample [path] / [msg] updated ok
2018-08-13 20:48:28 : [task] run bash: cat cicd/Builds/uwp/.cache/build.yaml.sample >> ci ... [path] modules/bash-command/ [params] envvars:
2018-08-13 20:48:29 : [task] create directory cicd/Builds/uwp/.cache [path] modules/create/
2018-08-13 20:48:29 : [task] create directory cicd/Builds/uwp/files [path] modules/create/
2018-08-13 20:48:30 : [task] create template cicd/Builds/uwp/.cache/build.yaml.sample [path] modules/generate-content/
2018-08-13 20:48:30 : [task] create template cicd/Builds/uwp/.cache/build.yaml.sample [path] / [msg] updated ok
2018-08-13 20:48:30 : [task] run bash: cat cicd/Builds/uwp/.cache/build.yaml.sample >> ci ... [path] modules/bash-command/ [params] envvars: 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需提交更改并运行 VSTS 构建:

```
$ echo .cache >> .gitignore
$ git add cicd
$ git commit -a -m "my UWP build"
$ git push 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

VSTS 提供了被称为 YAML 构建场景的编程方法来编纂构建逻辑。但是在 YAML 发展是乏味的。我们没有手动创建 YAML 构建定义，而是使用 Sparrowdo task runner，使用强大的 [Perl6](https://perl6.org/) 语言以更高级、更灵活的方式表达构建逻辑。

有关该主题的更多详细信息，请阅读 Sparrowdo::VSTS::YAML::科尔多瓦文档。

谢谢

阿列克谢