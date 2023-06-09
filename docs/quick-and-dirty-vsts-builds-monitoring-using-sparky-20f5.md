# 快速和肮脏的 VSTS 使用 Sparky 建立监控

> 原文：<https://dev.to/melezhik/quick-and-dirty-vsts-builds-monitoring-using-sparky-20f5>

Sparky 是一个强大的 crontab 风格的口袋大小的任务运行服务器。

如何使用 Sparky 监控 VSTS 的构建只是小菜一碟。

任务是检查 VSTS 大楼是否按计划成功完成日常工作。

# 监控场景

生产发布候选 web 应用程序有**每日版本**。应用程序源代码在 VSTS git 仓库`$git-repository`中，构建定义名为`Web Build`。我们希望确保构建发生后的第二天就能成功。

Sparrowdo 插件名为[vsts-schedule-build-check](https://sparrowhub.org/info/vsts-schedule-build-check)完成所有工作:

```
$ nano sparrowfile

#!perl6

my $git-repository = "http/git/repo";

git-scm $git-repository;

task-run "WebApplication Build Check", "vsts-schedule-build-check", %(
  pattern => "Web Build",
); 
```

Enter fullscreen mode Exit fullscreen mode

# 监控调度程序

随着构建每天运行，我们也每天运行 monitor，*在*构建之后:

```
$ nano sparky.yaml

crontab: "2 20 * * *"
keep_builds: 10 
```

Enter fullscreen mode Exit fullscreen mode

`keep_builds`指令意味着不再为此作业保留 sparky 报告，因为我们不想永远保留监控报告。

# 报告

```
2018-10-24 20:02:19 : [task] WebApplication Build Check [plg] vsts-schedule-build-check [path] /
++ grep schedule
++ grep 'Web Build Update'
++ vsts build list
104316  2.0181e+07   completed  succeeded              872  Web Build master     2018-10-24 03:00:00.560775  schedule
102573  2.0181e+07   completed  succeeded              872  Web Build master     2018-10-23 03:00:00.267757  schedule
100867  2.0181e+07   completed  succeeded              872  Web Build master     2018-10-22 03:00:01.772785  schedule
 77631  2.0181e+07   completed  succeeded              872  Web Build master     2018-10-21 03:00:01.573334  schedule
 78325  2.0181e+07   completed  succeeded              872  Web Build master     2018-10-20 03:00:00.225068  schedule
 76774  2.0181e+07   completed  failed                 872  Web Build master     2018-10-17 03:00:00.877437  schedule
 75028  2.0181e+07   completed  failed                 872  Web Build master     2018-10-18 03:00:00.548563  schedule
 73277  2.0181e+07   completed  failed                 872  Web Build master     2018-10-17 03:00:00.477304  schedule
 71571  2.0181e+07   completed  failed                 872  Web Build master     2018-10-16 03:00:02.605516  schedule
 87715  2.0181e+07   completed  failed                 872  Web Build master     2018-10-15 03:00:02.266283  schedule
 88667  2.0181e+07   completed  failed                 872  Web Build master     2018-10-14 03:00:00.535670  schedule
 87423  2.0181e+07   completed  succeeded              872  Web Build master     2018-10-13 03:00:00.537774  schedule
 61408  2.01807e+07  completed  succeeded              872  Web Build develop    2018-07-26 03:00:01.518767  schedule
ok  scenario succeeded
ok  text match /completed\s+succeeded.*\s+2018-10-24\s+\S+\s+schedule$/
ok  text match /completed\s+(succeeded|failed).*\s+2018-10-24\s+\S+\s+schedule$/
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

昨天的最后一次构建(`2018-10-24`)成功了，我们很高兴。