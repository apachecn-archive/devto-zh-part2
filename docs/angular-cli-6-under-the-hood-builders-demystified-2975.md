# 引擎盖下的角 CLI 6-建设者揭秘

> 原文：<https://dev.to/jeb/angular-cli-6-under-the-hood-builders-demystified-2975>

### *更新*:

***文章与 Angular 7.2.x 相关。
在 7.3.0 中，此 API 已被弃用(但仍受支持)，在 8.0.0 中，它将被新的 API 取代。
更新文章还在后面。***

嘿伙计们。希望今天阳光照耀着你。

在[之前的文章](https://dev.to/meltedspark/customizing-angular-cli-6-buildan-alternative-to-ng-eject-1oc4)中，我们讨论了在不退出底层 webpack 配置的情况下定制 Angular 6 构建配置。

提议的解决方案是使用现有的 [定制构建器](https://github.com/meltedspark/angular-builders/tree/master/packages/custom-webpack)*。*

今天，我们将看看引擎盖下，从头开始创建我们自己的自定义生成器。

## 角度 CLI 生成器

Angular CLI 6 带有一个新的架构，基本上是旧 CLI 的重写，旧 CLI 被分解成小块。

事实上，Angular CLI *本身*与你在 *angular.json* 中提供的配置无关，再也无关了。相反，它包装[角度开发套件](https://github.com/angular/angular-cli/tree/7.2.x/packages/angular_devkit)并触发*建筑师目标*。

简单来说:

*   Angular CLI 包包含预定义的命令、帮助和 CLI 相关内容。
*   [架构师包](https://github.com/angular/angular-cli/tree/7.2.x/packages/angular_devkit/architect)处理来自 *angular.json* 的配置。它负责将架构师目标映射到相关的构建器中，创建构建器，并使用在 *angular.json* 中为该构建器指定的配置来触发它。
*   建设者是做实际工作的人。因此， [BrowserBuilder](https://github.com/angular/angular-cli/tree/7.2.x/packages/angular_devkit/build_angular/src/browser) 为浏览器目标运行 webpack 构建， [KarmaBuilder](https://github.com/angular/angular-cli/tree/7.2.x/packages/angular_devkit/build_angular/src/karma) 启动 Karma 服务器并为单元测试运行 webpack 构建，等等。

## 角度 CLI 命令和架构目标

当您运行`ng build`或`ng test`或任何[预定义的 Angular CLI 命令](https://github.com/angular/angular-cli/wiki)时，会发生一些事情:

*   Angular CLI 命令被转换为相关的 architect 目标
*   创建一个相关的构建器
*   相关的构建器由相关的配置触发

运行自定义架构师目标时，会发生以下情况:

*   创建一个相关的构建器
*   相关的构建器由相关的配置触发

如您所见，预定义命令和自定义 architect 目标之间的唯一区别在于，在后者中，没有从 Angular CLI 命令到 architect 目标的映射。

简而言之，有一个通用命令`ng run`，它接收一个架构师目标作为参数(格式为`project:target`)并要求架构师执行这个命令。

因此，映射到建筑师目标的预定义角度 CLI 命令的每一个都可以用`ng run`来执行。例如:

*   `ng build` : `ng run my-cool-project:build`
*   `ng test` : `ng run my-cool-project:test`

诸如此类…

美妙之处在于，一旦您创建了自己的构建器，您就可以将它放入任何您想要的架构师目标中:

你可以创建你自己的目标，命名为`my-target`并用
T1 执行

运筹学

您可以在一个现有的目标(比如说，`build`目标)中替换构建器，并使用预定义的 Angular CLI 命令(`ng build`执行它，因为正如我们已经看到的，Angular CLI 命令只是到相关架构师目标的*映射。*

## 架构目标配置

让我们仔细看看 *angular.json* 文件:

```
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "example": {
      "root": "",
      "sourceRoot": "src",
      "projectType": "application",
      "prefix": "app",
      "schematics": {},
      "architect": {
        "build": {
            ...
        },
        "serve": {
            ...
        },
      }          
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在每个项目中，都有一个名为`architect`的条目，它包含*架构师目标*配置。因此，在这个特殊的例子中，我们只有一个名为`example`的项目，而这个项目又有两个架构师目标:`build`和`serve`。
如果您想要添加另一个名为`format`的架构师目标，那么该文件将变成:

```
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "example": {
      "root": "",
      "sourceRoot": "src",
      "projectType": "application",
      "prefix": "app",
      "schematics": {},
      "architect": {
        "build": {
            ...
        },
        "serve": {
            ...
        },            
        "format": {
            ...
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

每个架构师目标配置都有 3 个属性:

*   `builder` —到构建器的路径。路径的格式是`[package-path]:[builder-name]`，其中`[package-path]`是一个包含 *builders* 条目的 *package.json* 文件夹的路径，`[builder-name]`是 *builders.json* 中的条目之一(我们稍后将返回到这个)
*   `options` —构建器的配置。必须与生成器配置架构匹配，否则命令将失败。
*   `configurations` —备选目标选项(生产、开发等)图。).这是一个可选属性。

理论背景差不多就这些了。

说够了，让我们做点真正的事吧！

## 创建自己的构建器

我不是一个喜欢徒劳无功的人，所以我必须想出一些不仅仅是 *Hello World Builder* 的东西，但又像 *Hello World Builder* 一样简单。

假设您想要显示上次构建应用程序的日期和时间。系统正在加载，获取一些包含最新版本时间戳的文件，日期显示在页脚中。

我们要做的是实现一个生成器来创建这个时间戳文件。

### 创建包

一个包可以包含多个构建器，但是在我们的例子中，它只包含一个。

为 builders 包创建文件夹后的第一件事是将 *package.json* 添加到这个文件夹中(architect 假设 builders 包是一个 npm 包)。
这个 *package.json* 只是一个普通的 [package.json](https://docs.npmjs.com/files/package.json) 文件，增加了一个条目:

```
"builders": "builders.json" 
```

Enter fullscreen mode Exit fullscreen mode

*剧透:文件不一定是 builders.json，可以是你选择的任何名字。*

### builders.json

`builders.json`是一个描述你的构建者的文件。这是一个 json 文件，
遵循 Angular builders [模式](https://github.com/angular/angular-cli/blob/7.2.x/packages/angular_devkit/architect/src/builders-schema.json)，其结构如下:

```
{
  "$schema": "@angular-devkit/architect/src/builders-schema.json",
  "builders": {
    "builder-name": {
      "class": "path-to-builder-class",
      "schema": "path-to-builder-schema",
      "description": "builder-description"
    },
    ... more builders definitions
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

单个`builders.json`可以包含多个构建器的定义。

### 构建器定义

每个构建器由两个属性定义:

1.  `class` —实现`Builder`接口的 Javascript 类的路径。Architect 将解析配置并创建该类的一个实例。你可以在这里找到接口[的定义](https://github.com/angular/angular-cli/blob/7.2.x/packages/angular_devkit/architect/src/architect.ts)。
2.  `schema` —定义构建器配置的 json 模式的路径(architect 目标定义中的`options`属性)。Architect 根据这个模式验证配置，如果配置错误，它将使目标失败。

下面是我们的 *builders.json* 的样子:

```
{
  "$schema": "@angular-devkit/architect/src/builders-schema.json",
  "builders": {
    "file": {
      "class": "./timestamp.builder.js",
      "schema": "./schema.json",
      "description": "Builder that creates timestamp"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### schema.json

假设我们希望允许用户修改时间戳的格式以及时间戳将保存到的文件的名称。

因此，我们的 *schema.json* 将如下所示:

```
{
  "id": "TimestampBuilderSchema",
  "title": "Timestamp builder",
  "description": "Timestamp builder options",
  "properties": {
    "format": {
      "type": "string",
      "description": "Timestamp format",
      "default": "dd/mm/yyyy"
    },
    "path": {
      "type": "string",
      "description": "Path to the timestamp file",
      "default": "./timestamp"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果用户没有在 architect 目标配置中指定任何选项，architect 将从模式中选择默认值。

### 安装依赖项

为了格式化日期，我们将使用 [dateformat](https://www.npmjs.com/package/dateformat) 包，让我们安装它:

```
npm i dateformat 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 Typescript 开发我们的构建器(尽管这不是强制性的)，所以我们也必须安装它。
我们还将抓住`@angular-devkit/core`的功能以及`@angular-devkit/architect`的一些接口。为了从 Typescript 静态类型中获益，我们可能想要为`node`和`dateformat`安装`@types`。

这就是 *devDependencies* ( `@angular-devkit`将在运行时使用，但作为对等依赖)。让我们安装它们:

```
npm i -D @angular-devkit/core @angular-devkit/architect @types/node @types/dateformat typescript 
```

Enter fullscreen mode Exit fullscreen mode

### 建造者

现在我们准备实现生成器本身。
首先，让我们将我们的构建器配置定义为 *schema.d.ts* 中的一个接口: