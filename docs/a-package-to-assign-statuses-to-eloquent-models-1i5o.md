# ★为雄辩模型分配状态的包

> 原文：<https://dev.to/freekmurze/a-package-to-assign-statuses-to-eloquent-models-1i5o>

想象一下，你想让一个有口才的模特保持一种状态。只要在模型中添加一个`status`字段就可以轻松解决这个问题。但是，如果您需要状态更改的历史，或者需要存储一些关于状态更改原因的额外信息，只添加一个字段是不够的。为了处理这些情况，我们的团队创建了一个名为 [laravel-model-status](https://github.com/spatie/laravel-model-status) 的包。

在[安装包](https://github.com/spatie/laravel-model-status/blob/master/README.md#installation)之后，你必须将`HasStatuses`特征添加到所有需要状态的模型中。

您可以这样设置新状态:

```
$model->setStatus('my first status'); 
```

状态更改的原因可以作为第二个参数传递。

```
$model->setStatus('my second status', 'I found a reason'); 
```

有几个选项可以检索最新状态:

```
$model->status; // returns 'my second status'
$model->status(); // returns the latest instance of `Spatie\ModelStatus\Status`
$model->status()->reason; // returns 'I found a reason' 
```

当然，您也可以检索模型的整个状态历史。

```
$model->statuses; // returns a collection of all statuses 
```

该包提供了一个范围来检索具有给定状态的所有模型。

```
$allPendingModels = Model::currentStatus('pending'); 
```

要了解更多关于这个包的信息，请阅读 GitHub 上的自述文件。《包裹》的主要作者是我们的实习生[托马斯·弗赫尔斯特](https://twitter.com/TVke91)。一定要看看[我们团队之前制作的其他 Laravel 套装](https://spatie.be/en/opensource/laravel)。