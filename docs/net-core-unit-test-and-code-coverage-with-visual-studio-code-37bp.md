# 。Net 核心单元测试和 Visual Studio 代码的代码覆盖率

> 原文：<https://dev.to/equiman/net-core-unit-test-and-code-coverage-with-visual-studio-code-37bp>

有一种不好的观念认为你不能在。Mac 上的 Net Core，因为 OpenCover 工具只在 Windows 上工作。

但是我发现了一个新的 NuGet 包，叫做 Coverlet，是一个跨平台的代码覆盖库。网芯。最后，我们可以使用相同的命令进行代码覆盖，无论您是在 Mac、Linux 还是 Windows 上工作。

# 床罩

用于跨平台代码覆盖的工具。网络核心

与 OpenCover 相比，它的易用性令人难以置信。将这个 NuGet 包添加到您的**测试**项目:

```
dotnet add package coverlet.msbuild 
```

Enter fullscreen mode Exit fullscreen mode

现在添加一些运行测试项目时的参数:

```
dotnet test /p:CollectCoverage=true 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。但是如果你想根据你的需要个性化它:

```
dotnet test Project.Tests/Project.Tests.csproj /p:CollectCoverage=true /p:CoverletOutputFormat=\"opencover,lcov\" /p:CoverletOutput=../lcov 
```

Enter fullscreen mode Exit fullscreen mode

我需要一份`opencover`和`lcov`格式的代码覆盖率报告。第一个是上传到声纳云，第二个是用于覆盖 Gutters Visual Studio 代码扩展。

# Visual Studio 代码扩展

如果小智是程序员，肯定会说: **Visual Studio 代码...**

[![Alt I Choose You!](img/16884c3d13c6981dae4e5ae24d984c59.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--07-tclIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j0gssgbhtbylpixd320l.gif)

它可以根据您的需要进行扩展。它是完美的，因为这两个扩展给了你单元测试的超能力:

*   [Coverage Gutters-Visual Studio market place](https://marketplace.visualstudio.com/items?itemName=ryanluker.vscode-coverage-gutters)
*   [。NET 核心测试资源管理器- Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=formulahendry.dotnet-test-explorer)

**Coverage Gutter** 在屏幕上用颜色显示覆盖结果，您可以激活或禁用它。并且**测试资源管理器**给你一个可视化的资源管理器面板，当你可以运行测试时:所有的，上下文中的一个组或者单独的测试。更好的是在每次测试中点亮 code lens style，你可以看到他的结果。

# 单元测试框架

您可以在中找到许多关于单元测试框架(xUnit、nUnit 和 MSTest)的文献。Net，实际上，无论您选择什么，它们之间都没有显著的差异。

我为什么选择 xUnit？因为它是。Net 基础，我喜欢他的 syntaxis 和工作像一个测试浏览器插件的魅力。

关于 xUnit 有一个很好的解释。网芯。如果你正在制作婴儿浸泡液，我强烈推荐你阅读:

[单元测试中的 C#代码。NET Core 使用 dotnet test 和 xUnit](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test)

# 声纳云

我无法想象在一个项目中不使用 SonarQube 这样的工具的礼物。我创建了两个脚本来运行 Sonar Scanner 并上传结果，包括代码覆盖率。

为 MSBuild 安装[sonar scanner](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+MSBuild):

```
dotnet tool install --global dotnet-sonarscanner 
```

Enter fullscreen mode Exit fullscreen mode

在`Profile > Security > Generate Token`的 [SonarCloud](https://sonarcloud.io/) 上创建一个声纳登录令牌。保存在 **`sonar.txt`** 文件中，并将该文件添加到`.gitignore`中。现在可以局部使用，不能透露给好奇的眼睛。

在 Windows 上使用这个(Bat):

<figure>

```
@echo off
set /p token=<sonar.txt
dotnet sonarscanner begin /k:"company:project" /n:"Project" /v:"#.#.#" /o:"companyname" /d:sonar.host.url="https://sonarcloud.io" /d:sonar.login="%token%" /d:sonar.language="cs" /d:sonar.exclusions="**/bin/**/*,**/obj/**/*" /d:sonar.coverage.exclusions="Project.Tests/**,**/*Tests.cs" /d:sonar.cs.opencover.reportsPaths="%cd%\lcov.opencover.xml"
dotnet restore
dotnet build
dotnet test Project.Tests/Project.Tests.csproj /p:CollectCoverage=true /p:CoverletOutputFormat=\"opencover,lcov\" /p:CoverletOutput=../lcov
dotnet sonarscanner end /d:sonar.login="%token%" 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>sonar.bat</figcaption>

</figure>

在 Mac/Linux (bash)上使用它，并记住给予执行权限:

<figure>

```
#!/bin/bash
token="$(cat sonar.txt)"
dir="$(pwd)"
dotnet sonarscanner begin /k:"company:project" /n:"Project" /v:"#.#.#" /o:"company" /d:sonar.host.url="https://sonarcloud.io" /d:sonar.login="${token}" /d:sonar.language="cs" /d:sonar.exclusions="**/bin/**/*,**/obj/**/*" /d:sonar.cs.opencover.reportsPaths="${dir}/lcov.opencover.xml"
dotnet restore
dotnet build
dotnet test Project.Tests/Project.Tests.csproj /p:CollectCoverage=true /p:CoverletOutputFormat=\"opencover,lcov\" /p:CoverletOutput=../lcov
dotnet sonarscanner end /d:sonar.login="${token}" 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>sonar.sh</figcaption>

</figure>

> 变更**公司**、**项目**、 **#。#.#** 和**项目**用你的。

[分析参数-sonar cube 文档-Doc sonar cube](https://docs.sonarqube.org/display/SONAR/Analysis+Parameters)

# 奖金追踪

## 任务

Visual Studio 代码的另一个受欢迎的特性是，您可以自动执行和创建任务，并且可以轻松启动。

那是我的配置 task.json 文件。我有自动化的构建、测试、发布、打包和声纳任务。

<figure>

```
{  "version":  "2.0.0",  "tasks":  [  {  "label":  "build",  "command":  "dotnet",  "type":  "process",  "group":  {  "kind":  "build",  "isDefault":  true  },  "args":  [  "build",  "${workspaceFolder}/Project/Project.csproj"  ],  "problemMatcher":  "$msCompile"  },  {  "label":  "build tests",  "command":  "dotnet",  "type":  "process",  "group":  "build",  "args":  [  "build",  "${workspaceFolder}/Project.Tests/Project.Tests.csproj"  ],  "problemMatcher":  "$msCompile"  },  {  "label":  "test",  "command":  "dotnet",  "type":  "process",  "group":  {  "kind":  "test",  "isDefault":  true  },  "args":  [  "test",  "${workspaceFolder}/Project.Tests/Project.Tests.csproj",  "/p:CollectCoverage=true",  "/p:CoverletOutputFormat=\"opencover,lcov\"",  "/p:CoverletOutput=../lcov"  ],  "problemMatcher":  "$msCompile"  },  {  "label":  "publish win",  "command":  "dotnet",  "group":  "none",  "args":  [  "publish",  "${workspaceRoot}/Project/Project.csproj",  "-o",  "${workspaceRoot}/Library/win/",  "-c",  "release",  "-r",  "win10-x64"  ],  "problemMatcher":  "$msCompile"  },  {  "label":  "publish mac",  "command":  "dotnet",  "args":  [  "publish",  "${workspaceRoot}/Project/Project.csproj",  "-o",  "${workspaceRoot}/Library/mac/",  "-c",  "release",  "-r",  "osx.10.12-x64"  ],  "problemMatcher":  "$msCompile"  },  {  "label":  "pack",  "command":  "dotnet",  "args":  [  "pack",  "${workspaceRoot}/Project/Project.csproj",  "/p:NuspecFile=${workspaceRoot}/Project/Project.nuspec",  "-o",  "${workspaceRoot}/Package/",  "-c",  "release"  ],  "problemMatcher":  "$msCompile"  },  {  "label":  "permissions",  "type":  "shell",  "osx":  {  "command":  "chmod +x ${workspaceRoot}/sonar.sh"  },  "presentation":  {  "reveal":  "always",  "panel":  "new"  },  "problemMatcher":  []  },  {  "label":  "sonar",  "type":  "shell",  "windows":  {  "command":  "${workspaceRoot}\\sonar.bat"  },  "osx":  {  "command":  "${workspaceRoot}/sonar.sh"  },  "presentation":  {  "reveal":  "always",  "panel":  "new"  },  "problemMatcher":  []  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>tasks.json</figcaption>

</figure>

> 变更**公司**、**项目**、 **#。#.#** 和**项目**用你的。

## 特拉维斯词

对特拉维斯进行声纳扫描有点问题。诀窍是在安装时导出路径。

为了对 sonar 键保密，在 Travis 上创建一个环境变量作为`SONAR_KEY`。

```
language: csharp
mono: none
dotnet: 3.1.201

solution: Project/Project.csproj

install:
  - dotnet tool install --global dotnet-sonarscanner
  - dotnet restore Project/Project.csproj
  - dotnet restore Project.Tests/Project.Tests.csproj

before_script:
  - export PATH="$PATH:$HOME/.dotnet/tools"

script:
  - dotnet sonarscanner begin /k:"company:project" /n:"Project" /v:"#.#.#" /o:"companyname" /d:sonar.host.url="https://sonarcloud.io" /d:sonar.login="$SONAR_KEY" /d:sonar.language="cs" /d:sonar.exclusions="**/bin/**/*,**/obj/**/*" /d:sonar.cs.opencover.reportsPaths="lcov.opencover.xml" || true
  - dotnet build Project/Project.csproj
  - dotnet test Project.Tests/Project.Tests.csproj /p:CollectCoverage=true /p:CoverletOutputFormat=opencover /p:CoverletOutput=../lcov
  - dotnet sonarscanner end /d:sonar.login="$SONAR_KEY" || true

cache:
  directories:
    - '$HOME/.nuget/packages'
    - '$HOME/.local/share/NuGet/Cache'
    - '$HOME/.sonar/cache' 
```

Enter fullscreen mode Exit fullscreen mode

> 变更**公司**、**项目**、 **#。#.#** 和**项目**用你的。

## GitHub 动作

为了对声纳键保密，在`Settings -> Secrets`上创建一个名为`SONAR_KEY`的环境变量。

在您的项目:
上创建这个文件`/.github/workflows/build.yml`

```
name: build

on:
  push:
    branches: [master]
  pull_request:
    branches: [master]

env:
  dotnet: 3.1.201
  version: #.#.#
  key: company:project
  organization: company:project
  name: Project

jobs:
  build:
    runs-on: ${{ matrix.platform }}
    strategy:
      matrix:
        platform: [ubuntu-latest, macos-latest, windows-latest]
    name: build on ${{ matrix.platform }}
    steps:
      - uses: actions/checkout@v2
      - name: setup .Net Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.dotnet }}
      - name: restore
        run: dotnet restore Project/Project.csproj
      - name: build
        run: dotnet build Project/Project.csproj --no-restore
  test:
    runs-on: ubuntu-latest
    name: test
    steps:
      - uses: actions/checkout@v2
      - name: setup .Net Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.dotnet }}
      - name: install sonar-scanner
        run: dotnet tool install --global dotnet-sonarscanner
      - name: restore
        run: dotnet restore Project/Project.csproj
      - name: build
        run: dotnet build Project/Project.csproj --no-restore
      - name: restore test
        run: dotnet restore Project.Tests/Project.Tests.csproj
      - name: build test
        run: dotnet build Project.Tests/Project.Tests.csproj --no-restore
      - name: scanner begin
        run: dotnet sonarscanner begin /k:"${{ env.key }}" /n:"${{ env.name }}" /v:"${{ env.version }}" /o:"${{ env.organization }}" /d:sonar.host.url="https://sonarcloud.io" /d:sonar.login="${{ secrets.SONAR_TOKEN }}" /d:sonar.language="cs" /d:sonar.exclusions="**/bin/**/*,**/obj/**/*" /d:sonar.cs.opencover.reportsPaths="lcov.opencover.xml"
      - name: scanner build
        run: dotnet build Project/Project.csproj
      - name: scanner test
        run: dotnet test Project.Tests/Project.Tests.csproj /p:CollectCoverage=true /p:CoverletOutputFormat=opencover /p:CoverletOutput=../lcov
      - name: scanner end
        run: dotnet sonarscanner end /d:sonar.login="${{ secrets.SONAR_TOKEN }}"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} 
```

Enter fullscreen mode Exit fullscreen mode

> 变更**公司**、**项目**、 **#。#.#** 和**项目**用你的。

提交并将文件推送到`master`分支，并在`Actions`选项卡上看到这个脚本正在运行。

* * *

## 测试用例

如果你想看到所有这些在一个项目中一起工作，看看这个 GitHub repo [Kata: TDD 阿拉伯数字到罗马数字的 C#](https://github.com/equiman/tdd_roman) 。

* * *

**都是乡亲们！**
**快乐编码** 🖖

[![beer](img/192892baef71a32ea4a5e98a4927b05e.png)T2】](https://github.com/sponsors/deinsoftware)