# 我为什么喜欢 UNIX/Linux CLI

> 原文：<https://dev.to/ferricoxide/why-i-love-the-unixlinux-cli-49d5>

有很多原因，真的。可能太多了，无法一次列出来。

也就是说，有时候你从包里拿出一个老把戏，你会被提醒为什么你喜欢某样东西。今天就是这样的一天。

一整天，我们都在 AWS 的 CloudFormation 功能上遇到零星的性能问题。一些作业将在(几十分钟)内运行，其他作业将需要一个小时或更长时间。如果它们是明显不同的任务，这是可以理解的。但事实并非如此。字面意思是“运行模板‘A’并等待”,然后是“重新运行模板‘A’并等待”,第一次运行的时间大约是您预期的时间，而第二次运行相同的自动化模板需要 10 倍的时间。

cloudformation 的有趣之处在于，当您从命令行启动时，您可以通过一个文件传递所有参数。但是，有些模板不喜欢两个活动副本包含相同的参数值。解决这个问题的方法是概括您的参数文件，然后为每次运行“处理”该参数文件。UNIX/Linux CLI 意味着您可以以内联方式完成所有这些工作，如下所示:

```
aws --region us-east-1 cloudformation create-stack --stack-name RedMine07 \
  --disable-rollback --capabilities CAPABILITY_NAMED_IAM \
  --template-url https://s3.amazonaws.com/my-automationbucket/Templates/RedMine.tmplt.json \
  --parameters file://\<(sed 's/__NUM__/07/g' RedMine.parms.json) 
```

最精彩的部分在这个命令的最后一行。当您将 shell 命令封装在“

滑头。

在我的例子中，我将我的参数文件一般化为包含一个可替换的标记“__NUM__”。每次我需要运行这个命令时，我所要做的就是将“07”更改为一个新值，结果，新的堆栈在需要的地方有了唯一的值。

另一个好玩的是外壳的可编辑历史功能。如果我想运行完全相同的命令，再次更改我的值—作为堆栈参数传递的值和结果堆栈的名称—我可以这样做:

```
!!:gs/07/08 
```

这将导致前面的节重新运行，如下所示:

```
aws --region us-east-1 cloudformation create-stack --stack-name RedMine08 \
  --disable-rollback --capabilities CAPABILITY_NAMED_IAM \ 
  --template-url https://s3.amazonaws.com/my-automation-bucket/Templates/RedMine.tmplt.json \
  --parameters file://\<(sed 's/__NUM__/08/g' RedMine.parms.json) 
```

类似地，如果我想要更改的命令是我历史上的第 66 个命令，而不是我刚刚运行的命令，我可以这样做:

```
!66:gs/07/08 
```

并获得相同的结果。