# 提升二进制文件和可修复部署

> 原文：<https://dev.to/davidwengier/promoting-binaries-and-hotfixable-deployments-ahf>

在部署到生产环境时，有两种不同的思想流派。好吧，我们是开发者，所以可能有 100 种不同的思想流派，但是请原谅我。一种选择是从测试开始，通过试运行，一直到生产，提升相同的二进制文件，另一种选择是为生产的当前状态在源存储库中维护一个分支，并从该分支进行部署。一般的想法是，对于前者，您可以安全地知道您的生产部署是经过您的测试周期的*精确的*,而对于后者，无论您的测试分支可能处于什么状态，您总是能够修复和纠正生产问题。

幸运的是，这是一个可以避免的争论，相反，您可以建立一个两全其美的环境:将二进制文件升级到产品的可预测结果，以及万一您需要修补时的保险政策。我用的是团队城市和 T2 章鱼部署，但是不管你用什么技术，想法都是一样的。

## 提交散列很重要

我给任何设置任何 CI/CD、自动化、devops 工作流的人的最好的建议之一是尽可能早和尽可能频繁地将提交散列放入二进制文件和包中。拥有一个可以跟踪二进制文件并直接将它们与源代码相关联的已知标识符对于各种事情来说都是非常宝贵的，但是在这种情况下尤其重要，这样构建服务器和部署包就可以知道彼此在谈论什么。

在 TeamCity 中将提交散列加入到您的构建输出中就像在有问题的构建上配置一个设置一样简单。“Build number format”设置规定了 TeamCity 应该如何在其输出中格式化 Build number，以及您可以在脚本和构建步骤中使用或传递给脚本和构建步骤的`%build.number%`变量的格式。构建号的正常方法类似于`1.0.%build.counter%`，其中主版本和次版本被硬编码为 1.0，并且构建号随着每个构建自动增加。就个人而言，我喜欢使用 GitVersion 之类的东西来允许使用提交数量而不是构建数量，因为它允许跨构建服务器重新安装的弹性，但这是另一个讨论的内容。

通过在构建计数器后添加一个连字符，然后插入提交散列，可以在末尾标记提交散列。在 TeamCity 中，这是一个`%build.vcs.number%`变量，所以我们的完整版本号格式如下。

```
1.0.%build.counter%-%build.vcs.number% 
```

Enter fullscreen mode Exit fullscreen mode

这将给出一个类似于`1.0.134-770ac6d169006ce42b5bbc022a6a166135bbe8a7`的编译号。成功之处在于，我们在内部版本号中有提交散列，但是它有点难看而且不必要的长。对于大多数 repos，你只需要 7 或 8 个字符就可以了(Linux 内核开始需要 12 个字符，但他们有成千上万的提交)，所以我喜欢把散列缩短一点。在 TeamCity 中这样做有点不直观，因为在您用来指定构建格式的简单宏语言中没有可以执行的操作。要更改构建号，您需要使用构建步骤，并使用名为[服务消息](https://confluence.jetbrains.com/display/TCD10/Build+Script+Interaction+with+TeamCity#BuildScriptInteractionwithTeamCity-servMsgsServiceMessages)的 TeamCity 功能，这是一个标准的预定义输出结构，写入标准输出，TeamCity 将获取并处理该输出。我用一个简短的 PowerShell 脚本完成了这个任务，这是我定义的每个构建的第一步。

```
Write-Host "Old build number was: %build.number%"
$buildNumber = "1.0.%build.counter%"
$shortHash = "%build.vcs.number%"
$shortHash = $shortHash.substring(0, 10)
$buildNumber = "$buildNumber-$shortHash"
Write-Host "New build number is: $buildNumber"
Write-Host "##teamcity[buildNumber '$buildNumber']" 
```

Enter fullscreen mode Exit fullscreen mode

由于调试输出的原因，我的脚本太长了，但是我发现构建日志已经够冗长的了，所以去掉几行就不值得担心了。严格地说，整个脚本可以是一行程序。

```
Write-Host "##teamcity[buildNumber '1.0.%build.counter%-$("%build.vcs.number%".substring(0, 10))']" 
```

Enter fullscreen mode Exit fullscreen mode

我将短散列保持在 10 个字符，没有什么好的理由，您可以很容易地将其更改为您想要的任何字符。值得注意的是，作为构建计划的第一步,“构建编号格式”的设置实际上在构建的前几秒是没有用的，直到它运行这个脚本。由于脚本被指责，编译号现在将是`1.0.134-770ac6d169`。

## 将哈希传递给 Octopus

现在我们有了短的内部版本号，重要的是在任何推送到 Octopus 的包的版本号中使用它，以及从这些包发布的版本号。这提供了从 git 提交、构建到部署的完全可追溯性。如果你也用类似 [NerdBank 的东西。您可以用相同的提交散列来标记您的 dll，这意味着您也可以将它包含在您的应用程序日志或审计跟踪中。](https://github.com/AArnott/Nerdbank.GitVersioning)

有了在 Octopus 中部署的包中的版本号，这意味着我们现在可以创建一个 powershell 脚本，并将其放入生产部署流程中。该脚本将主分支快进到已经部署的特定提交，保证主分支将准确地指向构建包时开发分支所在的位置。

[![Fast Forward to Master Step](img/960207b9b529fc2edfbbd446c9bef05c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QmG0Wh4G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/img/posts/fast-forward-to-master-step.png)T3】

```
Set-Location -Path "<path to source repository>"
$vers = "$($OctopusParameters["Octopus.Release.Number"])".Split("-")[1]
Write-Host "Version is: $vers"
$commitHash = $vers.Substring($vers.IndexOf("-") + 1)
Write-Host "This release is from commit hash: $commitHash" 

Write-Host "Fetching latest origin just to be sure"
git fetch origin --prune
Write-Host "Resetting to current master"
git reset origin/master --hard
Write-Host "Fast forwarding to $hash"
git merge $hash --ff-only
Write-Host "Pushing back to origin"
git push origin
Write-Host "Finished" 
```

Enter fullscreen mode Exit fullscreen mode

该脚本需要在 git 工作副本中运行，并假设 master 已签出，尽管这很容易添加。我本可以重置到特定的提交并直接推送，但是我喜欢`--ff-only`提供的额外保护。它确保了如果工作副本出现任何问题，或者脚本在不正确的时间运行，至少不会丢失任何需要导航 reflog 的提交。可能有更好的方法来实现这一点，或者这种担心是多余的，但我并不自称是 git 专家。

## 修补程序现在只是另一个版本

既然主服务器已经部署到生产构建点，那么就可以从主服务器分支创建修补程序分支，并将其合并回主服务器分支，然后可以使用正常的构建和部署过程来构建和部署主服务器分支，并知道对开发分支所做的任何更改都不会包含在内。在一个理想的世界中，develop 仍然是可部署的，这个过程是不需要的，但是一个保险策略是一个好主意，并且在这种情况下，拥有它是很便宜的。在我的例子中，我在 TeamCity 上为主分支建立了一个单独的构建，它不会自动触发，并且考虑到每个生产部署都会改变主分支，这是最好的。

[![HotFix Lifecycle](img/fa17ba7f5375d749211c812f20e83136.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tR_73lST--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.wengier.cimg/posts/hotfix-lifecycle.png)

修补程序构建在 Octopus 中的修补程序通道上发布，这样它可以直接部署到 staging，从而避免测试。这种方式测试仍然映射到开发分支，这样过程就不会被中断。指定要使用的通道就是在创建 Octopus 版本的 TeamCity 构建步骤中设置正确的参数。

[![Specify Channel in Octopus](img/9cd00fe327e228761bfd53c67516c692.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B58ld6iI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.wengier.cimg/posts/push-to-octopus.png)

我遇到的唯一问题是，因为我没有使用一个“智能”构建号，而只是一个数字递增的构建计数器，第一个修补程序构建实际上没有被 Octopus 部署。查看 TeamCity 和 Octopus 日志可以清楚地看到，虽然构建和发布版本是正确的，但是当选择哪个包进入发布版本时，Octopus 认为修补程序的构建比上一个开发版本旧，这仅仅是因为构建计数器。

为了解决这个问题，我配置了 Octopus release creator 来强制使用一个包版本。因为我们在每一步都使用了提交散列，所以实际的版本号变得无关紧要，所以这看起来是一件非常安全的事情。理论上，如果两个版本指向相同的提交散列，那么一个版本是 v2.0.1 而另一个版本是 v3.56.231 并不重要，它们具有相同的代码，因此将以相同的方式运行。

[![Advanced Octopus Options](img/e684400f7f1aa70e70d54f6957e3c967.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8OuM0TSL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.wengier.cimg/posts/push-to-octopus-advanced-options.png)

您可能需要在 TeamCity 中单击“显示高级选项”来显示此项。

## 抱最好的希望，做最坏的打算

现在我们有一个情况，devlop 分支是自动构建和部署的，只要我们愿意。我们知道每一步的提交散列，因此我们可以将所有内容映射回原始源提交，并且如果出现问题，我们可以通过主分支移动和可用于手动触发的热修复构建来制定保险策略。