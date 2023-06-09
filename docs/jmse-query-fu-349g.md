# 质疑傅

> 原文：<https://dev.to/ferricoxide/jmse-query-fu-349g>

昨天，我发表了一篇文章，阐述了 LDAP 的查询语言的[之痛。今天，我不得不去听 JMSE 的报道。](https://dev.to/ferricoxide/the-pain-of-ldap-41cb)

我所在的团队管理着多个地区的多个 AWS 账户。我们还为许多租户和他们的 AWS 帐户提供支持。

这种支持是必要的，因为就像 AWS 努力使采用“云”变得容易一样，许多人蜂拥而至，但不一定知道如何经济有效地做到这一点。使用 AWS *可能是一种经济高效的做事方式，但未能进行充分的内务管理可能会让你损失惨重。不幸的是，部分低门槛意味着许多进入 AWS 的用户并不真正理解内务管理的必要性。而且，即使他们知道，他们也不一定知道如何实现自动化的内务处理方法。所以，随着时间的推移，东西会越积越多...导致不必要的成本暴露。*

不可避免地，这导致了“帮助:我们的费用比预期的要高得多”的情况。通常被证明是主要原因的是废弃的存储(和孤立的 ec2 保持 24/7/365 运行...但那是后话了)。通常，这是孤立(长期分离)的 EBS 卷、维护不善的 S3 存储桶和陈旧的 EBS 快照的某种组合。

识别孤立的 EBS 卷非常简单。不需要太多的询问。去看它。

维护不善的 S3 铲斗更难发现。但是，您通常可以启用存储桶库存报告生命周期策略来帮助自动降低成本。

虽然 EBS 快照的成本相对较低，但当它们越来越多时，您就开始注意到费用了。报道*可以*相对直接。然而，如果您碰巧维护定制 ami，那么做一个简单的“我可以删除什么”报告就变成了查询过滤的一个练习。幸运的是，与 AMI 相关的快照通常可以通过几个过滤器识别(排除),留给您一个快照列表以供进一步研究。不幸的是，编写查询过滤器意味着使用 JMSE 过滤器语法...这有点可怕。不知道它是否比 LDAP 更可怕。

无论如何，我最终编写了一个非常简单的报告脚本，让我对陈旧的快照是否是一个问题有一个基本的了解...以及所述问题(如果存在的话)的可能范围。虽然我们的租户很少维护自己的 ami，但我们在我主要合作的客户中维护。所以，我写了我的脚本，从相关报道中排除他们的快照:

```
for YEAR in $( seq 2015 2018 )
do
   for MONTH in $( seq -w 1 12 )
   do
      for REGION in us-{ea,we}st-{1,2}
      do
         printf "%s-%s (%s): " "${YEAR}" "${MONTH}" "${REGION}"
         aws --region "${REGION}" ec2 describe-snapshots --owner <ACCOUNT> \
           --filter "Name=start-time,Values=${YEAR}-${MONTH}*" --query \
           'Snapshots[?starts_with(Description, `Created by CreateImage`) == `false`]|[?starts_with(Description, `Copied for DestinationAmi`) == `false`].[StartTime,SnapshotId,VolumeSize,Description]' \
           --output text | wc -l
      done
    done
done | sed '/ 0$/d' 
```

Enter fullscreen mode Exit fullscreen mode

JMSE 查询的第一个糟糕之处是它可能非常长。更糟糕的是:it *真的*不允许换行以减少脚本的“杂乱无章”。如果您和我一样，您希望脚本中的给定代码行不超过“X”个字符。我通常更喜欢 X=80。JMSE 的疑问几乎是说，“是啊:去他妈的那些漂亮的废话”。

无论如何，要快速解释以上较简单的部分:

*   首先，这是一个基本的 BASH 包装器(应该可以在其他知道 Bourne-ish 语法的 shells 中工作)
*   我使用嵌套循环:第一层是年，第二层是月，第三层是 AWS 区域。这允许容易地对输出进行分组。
*   我使用`printf`来赋予我的输出意义
*   在我的`aws`命令的末尾，我使用`wc`来提供一个简单的返回行数:当使用`--output text`参数到`aws`命令时，每个对象的返回数据都作为一行完成...允许`wc`提供符合 JMSE 选择标准的线路的快速计数
*   在所有循环的末尾，我使用`sed`来隐藏任何给定地区/月份/年份没有快照的行

请注意，我没有回答为什么要定义输出字符串的答案...然后，本质上，扔掉那根线。简单地说，我必须输出*一些*给`wc`计数，我也可以输出几个有用的项目，以防我想要`tee`它并在脚本的扩展中使用数据。

上面有趣的部分是 JMSE 恐怖秀:

*   当您查询 EBS 快照对象时，它会输出一个 JSON 文档。该文档的结构如下所示:

```
{
    "Snapshots": [
        {
            "Description": "Snapshot Description Strin",
            "Tags": [
                {
                    "Value": "TAG1 Value",
                    "Key": "TAG1"
                },
                {
                    "Value": "TAG2 Value",
                    "Key": "TAG2"
                },
                {
                    "Value": "TAG3 Value",
                    "Key": "TAG3"
                }
            ],
            "Encrypted": false,
            "VolumeId": "<volume_id>",
            "State": "completed",
            "VolumeSize": <volume_size>,
            "StartTime": "<yyyy>-<mm>-<dd>T<hh>:<mm>:<ss>.000Z",
            "Progress": "100%",
            "OwnerId": "<account>",
            "SnapshotId": "<snap_id>"
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `"--query"`字符串的`".[StartTime,SnapshotId,VolumeSize,Description]"`部分将输出约束为包含来自对象的 JSON 文档的`"StartTime"`、`"SnapshotId"`、`"VolumeSize"`和`"Description"`元素。同样，在进行基本的元素计数普查时不会立即使用，但在以后的任务中可以转换成有用的东西。
*   当构造 JMSE 查询时，您将通知工具您想要处理 JSON 节点树的哪些元素。您几乎总是必须指定顶级文档节点。在这种情况下，那就是`"Snapshots"`。因此，查询字符串的初始部分是`"Snapshots[]"`。与本文档没有直接关系，但可能值得了解:
    *   在 JMSE，`"Snapshots[]"`基本上意味着“给我从`'Snapshots'`节点开始的一切”。
    *   与此相对应的是`"Snapshots[*]"`。
    *   如果只想返回第一个对象，可以使用`"Snapshots[0]"`。对于其他返回的对象类似(它使用一个从零开始的正整数索引)。
    *   如果您只想返回对象的一个子范围，您可以使用`"Snapshots[X:Y]"`
*   如果您想根据选择标准限制您的输出，JMSE 提供了`"[?]"`构造。在这个特殊的例子中，我使用了`"starts_with"`查询，或`"[?starts_with]"`。这个查询接受参数:您正在查询什么子属性；要匹配的字符串，以及您选择的是正还是负。因此:
    *   为了消除描述以“CreateImage 创建的”开头的快照，我的查询看起来像:`"?starts_with(Description, 'Created by CreateImage') == 'false'"`
    *   为了消除描述以`"Copied for DestinationAmi"`开头的快照，我的查询看起来像:`"?starts_with(Description, 'Copied for DestinationAmi') == 'false'"`
    *   如果我为这些字符串中的选择*——而不是当前的反匹配——我需要将我的`"false"`选择器改为`"true"`。*
*   要执行 OR 类型的复合查询，需要执行`"[query1]|[query2]"`。理论上，如果我需要通过添加更多的`"|"`操作符来选择/反对任意数量的属性值，我可以将任意多的这些值串在一起。

在运行我的脚本的最后，我得到了类似如下的输出:

```
 2017-07 (us-east-1): 61
    2017-08 (us-east-1): 746
    2017-09 (us-east-1): 196
    2017-10 (us-east-1): 4
    2017-11 (us-east-1): 113
    2017-12 (us-east-1): 600
    2018-01 (us-east-1): 149
    2018-03 (us-east-1): 6
    2018-04 (us-east-1): 3
    2018-06 (us-east-1): 302
    2018-07 (us-east-1): 1620
    2018-08 (us-east-1): 206
    2018-08 (us-west-2): 3 
```

Enter fullscreen mode Exit fullscreen mode

显然，在被查询的帐户中有一些清理工作要做:任何超过 30 天的快照都不太可能是有用的——2017 年的东西几乎是“糟糕的内务管理”的结果。