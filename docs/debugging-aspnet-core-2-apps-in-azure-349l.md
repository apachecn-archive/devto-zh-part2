# 在 Azure 中调试 Asp.Net 核心 2 应用

> 原文：<https://dev.to/craignicol/debugging-aspnet-core-2-apps-in-azure-349l>

在 Asp.Net 核心版本 1 的一次失败实验后，我一直在关注 Asp.Net 核心版本 2。我当然发现了。Net 标准和相关的框架支持非常受欢迎，而且与我之前的项目不同，我们不需要支持 GDI 或 SignalR 库，所以这更符合核心优势。

我将在一些后续帖子中谈论该项目和涉及的技术，因此如果您对 Asp.Net 核心、React/Redux with Typescript 或 C#中的 CosmosDb 有任何实际问题。让我知道，当我接触到这些技术时，我会尝试解决这些问题。

但是现在，我想从基础开始。Azure 上的 Asp.Net 核心在大多数情况下都工作得很好，但如果 Startup.cs 中出现问题，就没有应用程序洞察，一个通用的 502.5 IIS 错误——这仅仅意味着它不能与 Kestrel 对话，也没有 web 日志来帮助你。所以在你部署到 Azure 之前，帮自己一个忙，把下面的内容添加到 *web.config* 中，这样你就有日志来帮助你了。

`<system.webServer>
<handlers>
<add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
</handlers>
<aspNetCore processPath="dotnet" arguments=".\web.api.dll" stdoutLogEnabled="true" stdoutLogFile=".\logs\stdout" forwardWindowsAuthToken="true" />
</system.webServer>`

这样，如果你在你的网站上看到一个 502.5 错误，你可以跳到 Kudu 并开始阅读日志。根据您的 web 应用程序生命周期设置，它们可能会增长得非常快，因此您可能会受益于定期清理日志文件夹。

如果日志仍然没有帮助，或者你不明白你看到的是什么，[在 MSDN 有一个很好的 Asp.Net core 2 故障排除指南，但并不是所有的都适用于 Azure](https://blogs.msdn.microsoft.com/rohithrajan/2018/03/13/500-19-error-or-502-5-error-when-hosting-asp-net-core-2-application-inside-iis/) 。