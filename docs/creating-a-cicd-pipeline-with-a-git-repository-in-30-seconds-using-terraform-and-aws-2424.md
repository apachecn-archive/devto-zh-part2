# 使用 Terraform 和 AWS 在大约 30 秒内创建一个带有 git 存储库的 CI/CD 管道

> 原文：<https://dev.to/kylegalbraith/creating-a-cicd-pipeline-with-a-git-repository-in-30-seconds-using-terraform-and-aws-2424>

Terraform 是一款用于供应和管理云基础设施的强大工具。我最近写了一篇博文，提供了一个与 GitHub 存储库集成的 CI/CD 管道来持续部署静态网站。在经历了创建模板和博客帖子的练习后，我意识到我可以抽象所有这些。

所以我创建了一个新的 [Terraform 模块](https://registry.terraform.io/modules/slalompdx/codecommit-cicd/aws/0.1.0)，它可以在大约 30 秒内提供整个 CI/CD 管道和 AWS CodeCommit Git 存储库。

为了利用该模块，我们可以创建一个名为`my-stack.tf`的新模板，并向其中添加以下内容。

```
module "codecommit-cicd" {
    source                 = "git::https://github.com/slalompdx/terraform-aws-codecommit-cicd.git?ref=master"
    repo_name              = "learn-by-doing-newsletter"              # Required
    organization_name      = "kylegalbraith"                     # Required
    repo_default_branch    = "master"                     # Default value
    aws_region             = "us-west-2"                  # Default value
    char_delimiter         = "-"                          # Default value
    environment            = "dev"                        # Default value
    build_timeout          = "5"                          # Default value
    build_compute_type     = "BUILD_GENERAL1_SMALL"       # Default value
    build_image            = "aws/codebuild/nodejs:6.3.1" # Default value
    test_buildspec         = "buildspec_test.yml"         # Default value
    package_buildspec      = "buildspec.yml"              # Default value
    force_artifact_destroy = "false"                      # Default value
}

output "repo_url" {
    depends_on = ["module.codecommit-cicd"]
    value = "${module.codecommit-cicd.clone_repo_https}"
} 
```

Enter fullscreen mode Exit fullscreen mode

有相当多的变量可以传递到模块中。以下是对每种方法的简要说明:

*   我们新的 Git 仓库的名字。
*   `organization_name`:提供模板的组织名称。
*   我们的 Git 库的默认分支。
*   我们希望在 AWS 地区提供基础设施。
*   `char_delimiter`:用于唯一名称的分隔符。
*   我们的开发团队称之为这种环境(即开发对生产)。
*   【CodeBuild 等待构建完成的时间，以分钟为单位。
*   CodeBuild 将用来运行我们的构建的实例类型。
*   我们的构建实例将用来运行我们的构建的基础 AMI。
*   `test_buildspec`:测试运行器 buildspec.yml 文件的路径。
*   `package_buildspec`:包& deploy buildspec.yml 文件的路径。
*   `force_artifact_destroy`:指定当这个模板被销毁时，我们是否想要强制删除我们的构建工件。

为了在我们的 AWS 帐户中提供资源，我们从命令行运行以下命令。

```
$>terraform init
.....this will initialize terraform template.....
$>terraform plan
.....this will show you what the module is going to provision.....
$>terraform apply
.....this will create all of the resources..... 
```

Enter fullscreen mode Exit fullscreen mode

一旦`apply`命令完成，我们将在 Amazon Web Services 帐户中创建以下资源。

*   AWS CodeCommit git 源代码存储库。
*   带有构建和测试阶段的 AWS 代码管道。
*   AWS CodeBuild 项目，一个寻找一个`buildspec_test.yml`文件，另一个寻找一个`buildspec.yml`文件。

这里需要注意的重要一点是，我们的新存储库还没有初始化。我们需要使用模板输出中的 URL 克隆存储库，添加一个文件，然后推送。

```
$>git clone https://git-codecommit.us-west-2.amazonaws.com/v1/repos/learn-by-doing-newsletter
$>cd learn-by-doing-newsletter
$>echo 'hello world' > touch.txt
$>git commit -a -m 'init master'
$>git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

使用这个模块和大约 30 秒的命令行时间，我们已经创建了一个新的 git 存储库，并在 AWS 中提供了一个 CI/CD 管道。

表面上看起来可能没什么，但是像这样的代码可以节省大量的时间。看看这篇附带的博客文章中的[，其中有*手动*创建 CI/CD 渠道的步骤。创建管道有 21 个不同的步骤。在我们的模板中，我们用 20 行代码创建了管道**和**一个新的存储库。](https://medium.freecodecamp.org/how-to-continuously-deploy-a-static-website-in-style-using-github-and-aws-3df7ecb58d9c)

当我们需要频繁提供新的微服务时，这非常方便。我们可以把它变成一个模板，其他团队可以用它来快速建立他们自己的 CI/CD 管道和存储库。Terraform 是一个很好的工具，可以用来配置我们的基础设施，将通用基础设施封装到自己的模块中会有更大的优势。

如果你想把你的基础设施当作代码，开始考虑使用 Terraform。试试这个模块，如果您想看到我们添加更多内容，请告诉我们！

### 通过实际使用来学习 AWS

如果你喜欢这篇文章，并且渴望开始学习更多关于 Amazon Web Services 的知识，我已经创建了一个关于如何在 AWS 上托管、保护和交付静态网站的新课程！这是一个书籍和视频课程，从信息的海洋中切入，加速你对 AWS 的学习。给你一个框架，让你通过实际使用来学习复杂的东西。

前往登录页面，为自己获取一份副本！