# 通过 TeamCity 自动化 VSTS 扩展发布

> 原文：<https://dev.to/akuryan/automating-vsts-extensions-publishing-via-teamcity-371m>

**首先，警告的话:**
在[https://marketplace.visualstudio.com/](https://marketplace.visualstudio.com/)创建发布者时要格外小心，如果你是几个 AAD 的成员——这并不像你想象的那么简单。我最终得到了一个出版商，它是从我的一个 AAD 租户那里创建的(尽管是在 MSFT 帐户上授权的——有一个下拉菜单可以选择租户)。这是一个问题-我没有这些租户的 VSTS 帐户，所以我不能自动化的事情(我创建了令牌，但令牌是来自 MSFT 用户，而不是 AAD 租户用户)。我最终请求支持帮助——他们把我在 MSFT 的登录信息添加到了我的出版商中。

现在，一步一步地指导如何建立 Teamcity build。

1)安装 [Teamcity。节点插件](https://github.com/jonnyzzz/TeamCity.Node)

2)扩展版本是在 Teamcity 级别上修补的(因此，开发人员不需要记住增加这些数字的必要性，但是，如果有重大更改，他必须通知 DevOps 或更新主版本)
我使用 Teamcity 的“VCS 标记”功能来放置成功构建的标签，并使用“文件内容替换”功能来替换 vss-extension.json 和 task.json 中的版本。这样，Teamcity 将始终在创建扩展 vsix 包之前增加这两个文件中的版本。
[![Example of VCS labeling](img/52ca3e0e534628a0f6adba9f4ef1ec36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qngPZG6w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dobryak.org/wp-content/uploads/2018/04/features-600x305.png) 
我正在试验几个正则表达式(所以，这不是一个简单的事实，而是对您自己的实现有用的开始)

3)构建步骤:

1.  团队城市的 Node.js NPM。节点插件它安装 tfx-cli 并使用以下命令更新它:“install -g tfx-cli up -g tfx-cli”
2.  命令行它调用 tfx 发布我的扩展“tfx . cmd extension publish-t % access . token %-manifest-globs VSS-extension . JSON-no-color-no-prompt”

### 一些额外的发现

*   当你需要生成 PAT(个人访问令牌)描述[https://docs . Microsoft . com/en-us/vsts/extend/publish/command-line？view=vsts](https://docs.microsoft.com/en-us/vsts/extend/publish/command-line?view=vsts) -不要忘记在下拉列表中选择“所有帐户”(如此处所示-[https://social . msdn . Microsoft . com/Forums/vstudio/en-US/21073 d22-f16e-40 D2-9568-3d 379 e 64 ff 00/why-my-token-can-be-use-and-it-always-return-401-error？forum=vsx](https://social.msdn.microsoft.com/Forums/vstudio/en-US/21073d22-f16e-40d2-9568-3d379e64ff00/why-my-token-cannot-be-used-and-it-always-return-401-error?forum=vsx) 。

*   修复 NPM 扩展的 Windows 搜索逻辑——我无法通过`%AppData%\npm\tfx from Teamcity`启动它，所以，我为在系统帐户(`mklink D:\data\agentNpm C:\Windows\System32\config\systemprofile\AppData\Roaming\npm /d`)下运行的代理创建了一个`%AppData%\npm`的符号链接，并在命令行运行程序中作为`D:\data\agentNpm\tfx.cmd extension publish -t %access.token% --manifest-globs vss-extension.json --no-color --no-prompt`执行它