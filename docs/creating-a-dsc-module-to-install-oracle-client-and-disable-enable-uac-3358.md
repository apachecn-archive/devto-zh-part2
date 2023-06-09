# 创建 DSC 模块以安装 Oracle 客户端并禁用启用 UAC

> 原文：<https://dev.to/briannipper/creating-a-dsc-module-to-install-oracle-client-and-disable-enable-uac-3358>

你有没有接到一个任务，然后想，这能有多难？然后你开始了一段似乎永无止境的旅程。

欢迎来到混合新旧技术。

我发现自己需要在 Azure 上运行的 Windows VM 上使用期望的状态配置(也就是 DSC)安装 Oracle 客户端，嗯，可以说这是一次有趣的旅程。

紧随其后的是 DSC 的片段，我把它贴在这里，希望其他有类似需求的人可以偶然发现它，让自己免于头痛。

我将提供主要部分的分解。我有意将日志部分留在了脚本中，这样任何没有仔细阅读就大胆剪切和粘贴的人至少有一个日志可以研究。

使用该脚本的几个重要先决条件。

*   您需要一个 DSC 模块来下载 Oracle 客户机包，我使用的是 11gR2 的包，其中包含一个客户机的应答文件。

    *   这些项目将根据您的具体情况而定。

*   您的本地配置管理器(又名 LCM)应该设置为允许 DSC 重新启动。

*   该脚本将禁用 UAC，如果由于某种奇怪的原因，在重新启动后，该脚本没有继续运行，UAC 将处于禁用状态...

**！！警告！！UAC 不应该成为残疾人**

请确保您了解该脚本的功能，以及在脚本运行后如何诊断虚拟机的状态。

脚本 InsallOracleWithAnswerFile {

TestScript = {

$oraclePathTest =测试路径 HKLM:\ SOFTWARE \ wow 6432 node \ ORACLE \ KEY _ ora client 11g _ home 1；

$ reg val = Get-item property-Path HKLM:\ SOFTWARE \ Microsoft \ Windows \ current version \ Policies \ System-Name enable Lua；

if(($ oraclePathTest)-and($ reg val。EnableLUA -eq 1)) {

返回$True

}

否则{

返回$False

}

}

SetScript = {

$ continueProcessing = $ True

$ log timestamp = Get-Date-Format yymmdd hhmm；

$logPath = "C:\MyFolder\OracleInstallStatus";

$ log file = " $ log path \ install-$ log timestamp . log "；

add-Content-Path $ log file-Value "-用 Awnser -"开始整个 Oracle 安装；

add-Content-Path $ log file-Value "-打开-获取当前 UAC 值-"；

$ reg val = Get-item property-Path HKLM:\ SOFTWARE \ Microsoft \ Windows \ current version \ Policies \ System-Name enable Lua；

add-Content-Path $ log file-Value " UAC 注册表项:"；

add-Content-Path $ log file-Value $ reg val；

如果($ regval . enable Lua-eq 1){。

add-Content-Path $ log file-Value "-将 UAC 更改为禁用(即 0)-"；

set-item property-Path HKLM:\ SOFTWARE \ Microsoft \ Windows \ current version \ Policies \ System-Name enable Lua-Value 0；

add-Content-Path $ log file-Value "-设置全局 Falg 以触发重新启动-"；

$ global:DSCMachineStatus = 1；

$ continueProcessing = $ False

}

if ($continueProcessing) {

add-Content-Path $ log file-Value "-开始检查 Oracle 安装-"；

$ oracleRegPath = " HKLM:\ SOFTWARE \ wow 6432 node \ ORACLE \ KEY _ ora client 11g _ home 1 "；

add-Content-Path $ log file-Value " Oracle Reg Path:"；

add-Content-Path $ log file-Value $ oracleRegPath；

$ oraclePathTest = Test-Path $ oracleRegPath；

Add-Content -Path $logFile -Value“测试注册路径的结果”；

add-Content-Path $ log file-Value $ oraclePathTest；

if ($oraclePathTest -eq $False) {

$ params = "-silent-nowelcome-no console-wait for completion-no config-response file C:\ my folder \ Oracle package \ Oracle 11 gclientx 86 \ 11g \ x86 \ client \ response \ client _ 030514 . RSP "；

$ Oracle client exe = " C:\ my folder \ Oracle package \ Oracle 11 gclientx 86 \ 11g \ x86 \ client \ setup . exe "；

add-Content-Path $ log file-Value "-启动 Oracle 安装-"；

start-Process-file path $ oracleClientExe-argument list $ params-Wait-Passthru；

add-Content-Path $ log file-Value "-完成 Oracle 安装-"；

}

否则{

Add-Content -Path $logFile -Value "找到了 Oracle Reg 键，因此没有运行安装。

}

add-Content-Path $ log file-Value "-Secondary-获取当前 UAC 值-"；

$ reg val = Get-item property-Path HKLM:\ SOFTWARE \ Microsoft \ Windows \ current version \ Policies \ System-Name enable Lua；

add-Content-Path $ log file-Value " UAC 注册表项:"；

add-Content-Path $ log file-Value $ reg val；

如果($ regval . enable Lua-eq " 0){。

add-Content-Path $ log file-Value "-更改 UAC 以启用(即 1)-"；

set-item property-Path registry::HKEY _ LOCAL _ MACHINE \ SOFTWARE \ Microsoft \ Windows \ current version \ Policies \ System-Name enable Lua-Value 1；

}

add-Content-Path $ log file-Value "-设置全局 Falg 以触发重新启动-"；

$ global:DSCMachineStatus = 1；

}

add-Content-Path $ log file-Value "-使用答案完成整个 Oracle 安装-"

}

get script = { @ { Result = " insalloraclewithsanswerfile " } }

DependsOn = "[Archive]Oracle package extract "

}

### 测试脚本

简而言之，测试脚本确认了两件事。

1.  Oracle 客户端的安装是否基于预期的注册表项。
2.  UAC 是否处于启用状态，也可以通过查看预期的注册表项值来检查。

如果这些条件中的任何一个为假，则需要运行 set 脚本。

### 设置脚本

这就是有趣的地方。

我将跳过对日志记录的评论，因为应该清楚日志在做什么，关于日志记录的方式的一个有趣的点是，它可以指示这个脚本运行了多少次，因为日志被写入到一个文件中，该文件的名称是日期时间戳，我发现这很方便，因为我几次将自己置于无限循环中。

因为我必须给自己提供一个断路器，所以我在开始时设置了一个继续标志，以便以后使用。

由于 Oracle 安装程序的工作方式，即使使用应答文件，在开始安装之前，您仍然会得到 UAC 提示，因此这个 set 脚本的第一部分将通过注册表调用获得 UAC 的值。

获得 UAC 值后，我检查该值的状态，如果该值为 ON(即 1 ),我想禁用它，因此我更改了注册表值，还将我的 continue 标志设置为 false，并设置环境变量以告知 DSC 在该脚本完成后重新启动。也就是...

$ gobal:DSC machinestatus = 1；

在这一点上，我检查我的标志，以确保我跳过尝试安装 oracle 客户端，这是因为即使我们更新了注册表，它也会生效，直到我们重新启动，因此不需要运行 Oracle 安装程序。

因此，我们记录我们无法安装 oracle 客户端并继续。

下一位也导致不应用更改，因为注册表中的 UAC 值在技术上仍然是 1，因为 UAC 实际上直到重新启动才持久化。

我在最后草率地重置了 DSCMachineStatus。咩。

所以现在我们重启。

现在，在重新启动后第二次运行脚本时，我们将处于禁用 UAC 的状态，但我们没有安装 oracle 客户端，因此当我们运行设置的脚本时，将会安装 Oracle。

我们通过启用 UAC 完成了第二次运行，并再次重新启动。

当系统第三次启动时，将启用 UAC 并安装 Oracle 客户端。

如果你在评论中遇到这个脚本的问题，请让我知道。