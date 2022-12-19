# ★false 我们的 Laravel Nova 套餐

> 原文：<https://dev.to/freekmurze/true-our-laravel-nova-packages-eag>

# 介绍我们的 Laravel Nova 软件包

Laravel Nova 是一个漂亮的管理面板，由 Laravel 的创造者 Taylor Otwell 在 Laracon 2018 的[首次展示。使用 Nova 构建丰富的管理面板轻而易举。《新星》于今天](https://www.youtube.com/watch?v=pLcM3mpZSV0)发布[。泰勒很友好，在拉拉康事件后不久就让我们提前接触了新星。在我们早期的访问中，我和我在 Spatie 的团队创造了两个 Nova 工具，我想在这篇博文中介绍一下。](https://twitter.com/laravelphp/status/1032266389153824768)

## 什么是 Nova 工具？

Nova 的一个关键特征是它非常具有可扩展性。你可以创建自己的[字段类型](https://nova.laravel.com/docs/1.0/resources/fields.html#field-types)、[自定义动作](https://nova.laravel.com/docs/1.0/actions/defining-actions.html#overview)，创建[漂亮的小卡片](https://nova.laravel.com/docs/1.0/customization/cards.html#overview)等等。

工具是向 Nova 添加整个自定义屏幕的一种方式。它们由您可以定义的服务端 API 和可以使用该 API 的 Vue 工具组成。你真的可以做任何你想做的事情。

## 新星-尾巴-工具

我们创建的第一个工具是跟踪应用程序日志的工具。这里有一个小演示，展示了该工具的实际应用。

我们制作这个工具是为了熟悉在 Nova 中创建工具。你可以在 GitHub 上的[这个 repo 里找到源代码。](https://github.com/spatie/nova-tail-tool)

## nova-备份-工具

在使用尾部工具后，是时候创建一个更大的 Nova 工具了。在 Laracon US，Nova 的联合创始人 David Hemphill 向我展示了一个备份工具在 Nova 中的小模型。我很快就喜欢上了创建一个真正的备份工具。我们已经有了一个[流行的](https://packagist.org/packages/spatie/laravel-backup/stats)包来[备份一个 laravel 应用](https://docs.spatie.be/laravel-backup/v5/introduction)，所以这个工具需要成为 laravel-backup 包的 UI。

这里有一个视频展示了 Nova 工具可以做什么。

这个用户界面是由几个 Vue 组件呈现的。它通过一个简单的 API 与 Laravel 应用[通信。因为`laravel-backup`已经能够通过 Artisan 命令创建、列出和删除备份和状态，它已经包含了一些好的类，比如`Spatie\Backup\BackupDestination\BackupDestination`和`Spatie\Backup\BackupDestination\Backup`来轻松创建 API。这里是负责在工具的 UI 中提供列出备份状态的上半部分的控制器。](https://github.com/spatie/nova-backup-tool/blob/28f4032bed83b8d7735646f336cdf4303c747917/routes/api.php) 

```
<?php
namespace Spatie\BackupTool\Http\Controllers;
use Illuminate\Support\Facades\Cache;
use Spatie\Backup\Tasks\Monitor\BackupDestinationStatus;
use Spatie\Backup\Tasks\Monitor\BackupDestinationStatusFactory;
class BackupStatusesController extends ApiController
{
    public function index()
    {
        return Cache::remember('backup-statuses', 1 / 15, function () {
            return BackupDestinationStatusFactory::createForMonitorConfig(config('backup.monitorBackups'))
                ->map(function (BackupDestinationStatus $backupDestinationStatus) {
                    return [
                        'name' => $backupDestinationStatus->backupName(),
                        'disk' => $backupDestinationStatus->diskName(),
                        'reachable' => $backupDestinationStatus->isReachable(),
                        'healthy' => $backupDestinationStatus->isHealthy(),
                        'amount' => $backupDestinationStatus->amountOfBackups(),
                        'newest' => $backupDestinationStatus->dateOfNewestBackup()
                            ? $backupDestinationStatus->dateOfNewestBackup()->diffForHumans()
                            : 'No backups present',
                        'usedStorage' => $backupDestinationStatus->humanReadableUsedStorage(),
                    ];
                })
                ->toArray();
        });
    }
} 
```

你可以在 GitHub 的这个报告中找到 nova-backup-tool [的源代码。](https://github.com/spatie/nova-backup-tool)

## 创建新 Nova 工具

我们已经计划创造更多的 Nova 工具。新星本身有一个工匠命令来搭建一个新工具。但是为了包装它，有一些额外的步骤要做，即使有脚手架。

这就是为什么我们创建了[一个名为 skeleton-nova-tool](https://github.com/spatie/skeleton-nova-tool) 的框架回购协议，作为新 nova 工具包的起点。它基于 Nova 提供的脚手架，但是添加了一些细节。有现成的配置项配置文件。gitignore 已经不忽略`dist`目录，已经有一个对 API 的测试，等等。

请随意使用我们的骨架回购来启动您的 Nova 工具包。

[https://github.com/spatie/skeleton-nova-tool](https://github.com/spatie/skeleton-nova-tool)

## 提前获取访问权限

在过去的几天里，有人担心斯帕蒂和其他提前进入市场的公司有不公平的优势。理由是，这样一来，任何获得早期接入的人都可以主导付费的 Nova 市场。

我们的两个 Nova 工具和我们将制作的其他工具将是免费和开源的。我们制造这些工具没有报酬。在早期的访问中，我们帮助简化了 Nova 工具创建工作流程和我们的两个工具。所有这些都是为了在发布的第一天为新用户提供更好的体验。

## 向前看

在过去的 [Spatie](https://spatie.be) 我们创造了[很多包](https://spatie.be/open-source)。这个包装系列已经有机增长。因为我们是 Laravel 的早期适应者，周围根本没有那么多高质量的包，所以我们需要创建自己的包。

从那时起，拉勒维尔生态系统已经有了相当大的发展。在 Spatie，我们确实计划创建更多的 Nova 包，但是现在有很多其他的团体也在创建 Nova 的东西。这就是为什么我不期望我们的 Nova 系列会像我们的 package 系列一样大。

为了让 Nova 发挥作用，我们需要许多帮手。这将是一艘大船，欢迎每个人来创造令人敬畏的东西。如果你做了什么，一定要把它添加到我们在[紧固公司](https://tighten.co/)的朋友正在创建的 [Nova packages](https://novapackages.com/) 网站。已经有一些由 [Marcel Pociot](https://github.com/mpociot) 和 [Mohammed Said](https://twitter.com/themsaid) 设计的很酷的包列在那里。

我们在创造这些工具时获得了很多乐趣。在引擎盖下都是 Laravel 和 Vue，所以学习曲线对我们来说一点也不难。迫不及待地想看到社区在未来几周、几个月或几年内发布的精彩内容！