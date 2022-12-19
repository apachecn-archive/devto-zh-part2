# 纱线感觉:在纱线中开始纱线，当它有用的时候

> 原文：<https://dev.to/frosnerd/yarnception-starting-yarn-within-yarn-through-gulp-and-when-it-is-useful-og3>

# 简介

当开发新的服务时，我个人更喜欢用清晰的目的和明确的界限来设计它们。我也喜欢将一个服务的所有源代码放在同一个版本控制库中。当您设置一个包含 AWS 等云提供商的多个基础设施组件的新项目时，在同一个存储库中管理您的基础设施状态是很方便的(参见我以前关于代码为的[基础设施的帖子)。](https://dev.to/frosnerd/infrastructure-as-code---managing-aws-with-terraform-i9o)

如果您将多个 AWS Lambda 函数的源代码存储在描述基础设施的文件旁边，例如 [Terraform](https://www.terraform.io/) 文件，您可能希望使用一个构建工具来管理整个应用程序生命周期:

*   检查格式
*   编译/林挺
*   执行单元测试
*   包装
*   执行集成测试
*   部署
*   交付

我喜欢使用 [Yarn](https://Yarnpkg.com/en/) 作为我的 JavaScript 应用程序的包管理器，同时也管理应用程序的生命周期。虽然没有像 [Maven](https://maven.apache.org/) 或 [SBT](https://www.scala-sbt.org/) 中那样的一流生命周期支持，但是你可以在你的`package.json`中使用[脚本](https://Yarnpkg.com/lang/en/docs/cli/run/)来构建一些有用的东西。

不仅在单个 Node.js Lambda 函数的生命周期中使用 Yarn，而且在整个 monorepository 中使用 Yarn，包括您的 Terraform 文件，以及您可能使用的所有不同的 Lambda 函数，这不是很好吗？我为自己找到了一个方便的方法，使用 **Yarn** 、 **Yarn workspaces** 、 **Gulp** 和 **Terraform** 的组合。在这篇博文中，我想分享我的蓝图。

该员额的其余部分结构如下。首先，我们将看一下项目设置的概述。之后，我们将详细介绍 Yarn 工作空间的作用。接下来的两节将讨论部署包的创建和实际部署。我们通过讨论主要发现来结束这篇文章。

# 项目设置

为了执行蓝图，我使用了 Yarn 1.7.0 和 Terraform 0.11.7。所有其他依赖关系都在各自的`package.json`文件中定义。[源代码](https://github.com/FRosner/multi-aws-lambda-nodejs-example)可以在 GitHub 上获得。

下面的清单描述了项目结构。我们在顶层`package.json`中定义整体结构和脚本。然后是两个 Node.js 模块，包含 Lambda 函数处理程序`calculator`和`concatenator`。它们有单独的`package.json`文件，包含不同的依赖关系。每个模块还有一个`gulpfile.js`,用于创建部署包。`terraform`文件夹包含 Terraform 文件。

```
├── package.json
├── yarn.lock
├── lambda
│   ├── calculator
│   │   ├── gulpfile.js
│   │   ├── package.json
│   │   ├── src
│   │   │   └── lambda.js
│   │   └── test
│   │       └── lambdaSpec.js
│   └── concatenator
│       ├── gulpfile.js
│       ├── package.json
│       ├── src
│       │   └── lambda.js
│       └── test
│           └── lambdaSpec.js
└── terraform
    ├── calculator.tf
    ├── concatenator.tf
    ├── providers.tf
    └── variables.tf 
```

Enter fullscreen mode Exit fullscreen mode

# 纱线工作区配置

Yarn 工作区是一种在单个存储库中管理多个 Node.js 模块的便捷方式。它在某种程度上可以与 [SBT 子项目](https://www.scala-sbt.org/0.13/docs/Multi-Project.html)或 [Maven 模块](https://maven.apache.org/guides/mini/guide-multiple-modules.html)相媲美。你需要做的就是创建一个顶层`package.json`并指定你需要的工作空间。

如果你执行`yarn install`，它将安装所有的工作空间。对于定制脚本，我喜欢使用 [wsrun](https://www.npmjs.com/package/wsrun) 包，它可以在所有工作区内执行一个 Yarn 脚本。下面是顶级`package.json`的样子。

```
{  "private":  true,  "workspaces":  [  "lambda/*"  ],  "scripts":  {  "format:test":  "prettier --config '.prettierrc.json' --list-different '**/*.js' && (cd terraform && terraform fmt -check=true -list=true)",  "format:fix":  "prettier --config '.prettierrc.json' --write '**/*.js' && (cd terraform && terraform fmt -write=true)",  "lint:test":  "eslint --config .eslintrc.json '**/*.js'",  "lint:fix":  "eslint --config .eslintrc.json '**/*.js' --fix",  "terraform:init":  "set -e; (cd terraform && terraform init)",  "terraform:apply":  "set -e; (cd terraform && terraform apply -auto-approve)",  "terraform:destroy":  "set -e; (cd terraform && terraform destroy -auto-approve)",  "clean":  "yarn wsrun clean && rm -rf node_modules",  "test":  "yarn wsrun test",  "package":  "yarn wsrun package",  "deploy":  "yarn package && yarn terraform:apply",  "destroy":  "yarn package && yarn terraform:destroy"  },  "devDependencies":  {  "eslint":  "^5.5.0",  "prettier":  "^1.14.2",  "terraform-npm":  "^0.2.6",  "wsrun":  "^2.2.1"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

虽然也有一些关于工作区的配置选项，但是每个工作区通常都有常规的`package.json`文件。但是我们不会在这篇文章中详细讨论。接下来让我们看看`package`脚本是如何在两个模块中定义的。

# 生成部署包

在 AWS Lambda 上使用 Node.js 时，创建[部署包](https://docs.aws.amazon.com/lambda/latest/dg/nodejs-create-deployment-pkg.html)的推荐方法是压缩整个源代码，包括所有必需的 Node.js 模块。像 [browserify](http://browserify.org/) 这样的其他方法在过去没有得到官方支持，人们在使用 AWS JavaScript SDK 时会遇到问题。

幸运的是，Gulp 提供了一种便捷的方式来自动创建 AWS 所需的部署包。受亚马逊拉姆达的[大口工作流的启发，我创建了一个](https://medium.com/@AdamRNeary/a-gulp-workflow-for-amazon-lambda-61c2afd723b6) [`gulpfile.js`](https://github.com/FRosner/multi-aws-lambda-nodejs-example/blob/master/lambda/calculator/gulpfile.js) ，它定义了五个不同的任务:

*   `clean`删除`stage`和`dist`文件夹
*   `install`使用 Yarn 在`stage/node_modules`中安装所有生产依赖项
*   `copySrc`复制`stage`中的所有源文件
*   `bundle`将`stage`的内容压缩到`dist`
*   为了获得可重现的构建，执行前面的四个任务

现在我们可以定义`yarn package`脚本来简单地调用`gulp`。然后，它将清除以前构建的状态，只安装当前模块所需的依赖项，复制源文件，并压缩整个包。

# 部署和交付

部署和交付通过 Terraform 完成。我们首先定义需要的资源，即 [`calculator.tf`](https://github.com/FRosner/multi-aws-lambda-nodejs-example/blob/master/terraform/calculator.tf) ， [`concatenator.tf`](https://github.com/FRosner/multi-aws-lambda-nodejs-example/blob/master/terraform/concatenator.tf) 。此时，我们只需要引用上一步中创建的各个 zip 文件作为部署包的文件名。每当我们在顶层运行`yarn deploy`时，它将首先在所有 Lambda 工作区内执行`yarn package`，然后通过`terraform apply`部署和交付变更。

如果您想要分离部署和交付步骤，您可以首先将工件上传到一个 S3 桶中，然后用[指定资源中的位置](https://www.terraform.io/docs/providers/aws/r/lambda_function.html#specifying-the-deployment-package)。对于较大的部署包也建议这样做，因为 S3 API 对较大的文件有更好的支持。

# 结论

在这篇文章中，我们看到了如何结合 Yarn、Yarn workspaces、Gulp 和 Terraform 来管理 node . js AWS Lambda mono repositories。Yarn 充当包管理器和顶级构建工具。Yarn 工作区允许高效灵活地管理同一个存储库中的不同 Node.js 模块。Gulp 使我们能够在`stage`文件夹中只安装每个模块所需的生产依赖项，并创建一个最小的部署包。Terraform 用于将您的基础设施部署到 AWS。

一如既往，完成一项任务有许多方法。我听说[无服务器框架](https://serverless.com/)做类似的事情，但我不知道它是否支持 Terraform 所做的所有不同的资源。如果有人知道，请在下面评论！到今天为止，我个人还没有任何经验。

我也偶然发现了 Lerna，但是我没有发现直接使用 Yarn workspaces 有任何好处，因为它们支持我需要的所有特性。但也许你能发现我漏掉的东西？请在下面评论！

对于所有的 Lambda 函数，您更喜欢单个存储库还是单个存储库？你曾经使用过无服务器框架或 Terraform 吗？你喜欢 Lerna 还是 native Yarn 工作空间？很好奇你的经历和看法:)

* * *

封面图片由 [Marco Verch](https://flic.kr/p/XhpEMo) 拍摄。