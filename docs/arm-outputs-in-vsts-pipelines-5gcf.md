# VSTS 管道中的军火输出

> 原文：<https://dev.to/sogeti/arm-outputs-in-vsts-pipelines-5gcf>

在我作为云解决方案架构师的工作中，我经常发现团队在努力解决完全相同的问题。在这篇博文中，我将强调其中一个问题；在 VSTS 管道中传递 ARM 输出变量。

许多开发人员都有 Powershell 或其他脚本来解决这个问题，但我想强调一个扩展，它是免费提供的，可以为您免费完成这个任务！

[![ARM Outputs by Kees Schollaart](../Images/1eb6fecfd3ef2eeec79da7161979cdd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iYQygSce--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://keesschollaart.gallerycdn.vsassets.io/extensions/keesschollaart/arm-outputs/3.0.2/1517479176919/images/screenshots-vsts-arm-outputs-1.png)

> 这个扩展使您能够在您的 VSTS 环境中使用 ARM 部署输出。
> 
> 此步骤将使用所选资源组中最后一次成功的部署。如果这个部署有输出，所有输出都通过 ARM 输出键复制到 VSTS 变量中。
> 
> 这些输出随后可以通过默认的 VSTS 方式使用:`$(same-key-as-in-arm-template)`
> 
> 通常该任务在“Azure 资源组部署”任务之后直接运行。

所以我的亲尖；停止使用您自己的脚本，实现这个方便的扩展！

PS:顺便说一句，Kees 也是一个很棒的人，在你把这个安装到你的项目 [![😉](../Images/b26450942c7c42752fe0b02f126abb48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fumfYCPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f609.png) 之后，给他发一条推特

链接到市场:[https://marketplace.visualstudio.com/items?itemName = keesschollaart . arm-outputs](https://marketplace.visualstudio.com/items?itemName=keesschollaart.arm-outputs)

推特上的 Kees:【https://twitter.com/keesschollaart】T4