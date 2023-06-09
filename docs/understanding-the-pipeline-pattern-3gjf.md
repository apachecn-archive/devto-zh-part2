# 了解管道模式

> 原文：<https://dev.to/matthewbdaly/understanding-the-pipeline-pattern-3gjf>

在以前的一篇文章中，我使用管道模式来演示使用光学识别和机器学习处理信件。最近几个月，我发现管道模式非常有用。对于一系列连续的任务，这种方法可以让你的代码更容易理解，因为它允许你把代码分解成简单的、有逻辑的步骤，这些步骤易于单独测试和理解。如果您熟悉 Unix 中的管道和重定向，您会知道如何将多个相对简单的命令连接在一起，以对数据执行一些非常复杂的转换。

几个月前，我被要求为工作中的一个脸书领导表单创建一个 webhook。我的一位同事不得不从脸书手动导出 CSV 数据，然后将其导入 MySQL 数据库和 Campaign Monitor 邮件列表，这是一项繁重的任务，因此他们要求我寻找更自动化的解决方案。我最终用 Lumen 构建了一个 webhook，它将经历以下步骤:

*   从网络钩子上获取线索 ID
*   用这些 ID 从脸书 API 中找出线索
*   将原始数据处理成更合适的格式
*   将数据保存到数据库
*   将数据推送到活动监视器

因为这涉及到许多不连续的步骤，所以我选择将每个步骤作为一个单独的阶段来实现。这样，每一步都易于单独测试，并且易于重用。事实证明，这种方法救了我们，因为脸书需要批准这个应用程序(最终拒绝了它——他们当时的文档没有明确说明如何实现服务器到服务器的应用程序，因此很难满足他们的指导方针)，所以我们需要一个临时的解决方案。相反，我编写了一个 Artisan 任务，用于从 CSV 导入文件，包括以下步骤:

*   从 CSV 文件中读取行
*   将 CSV 数据格式化为所需的格式
*   将数据保存到数据库
*   将数据推送到活动监视器

这意味着现有的两个步骤可以被重用，不需要修改代码或测试。我只是添加了两个新的类来读取数据和格式化数据，以及 Artisan 命令，它简单地调用了各种管道阶段，*，这就是全部的*。在这篇文章中，我将展示我是如何实现的。

虽然有不止一种实现可用，并且开发自己的实现并不难，但我通常使用 PHP 联盟的[管道包](https://pipeline.thephpleague.com/)，因为它简单、可靠且经过充分测试。假设我们的应用程序有三个步骤:

*   格式化请求数据
*   保存日期
*   将其推送给第三方服务。

因此，我们需要为流程中的每个步骤编写一个阶段。每一个都必须是可调用的，比如闭包、回调或者实现`__invoke()`魔法方法的类。我通常选择后者，因为它允许您更容易地通过其构造函数将依赖项注入到 stage 中，从而更容易使用和测试。下面是我们的第一阶段可能的样子:

```
<?php

namespace App\Stages;

use Illuminate\Support\Collection;

class FormatData
{
    public function __invoke(Collection $data): Collection
    {
        return $data->map(function ($item) {
            return [
                'name' => $item->fullname,
                'email' => $item->email
            ];
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个类只不过接收一个集合，并按预期格式化数据。我们可以让它接受一个请求对象，但是我选择不这样做，因为我觉得将数据作为一个集合传入更有意义，这样它就不会被绑定到 HTTP 请求。这样，它也可以使用 Artisan 任务处理从 CSV 文件传递过来的数据，并且它如何首先接收数据的细节被推迟到首先调用管道的类。注意这个阶段也返回一个集合，供下一步处理:

```
<?php

namespace App\Stages;

use App\Lead;
use Illuminate\Support\Collection;

class SaveData
{
    public function __invoke(Collection $data): Collection
    {
        return $data->map(function ($item) {
            $lead = new Lead;
            $lead->name = $item->name;
            $lead->email = $item->email;
            $lead->save();
            return $lead;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这一步将每个销售线索保存为一个雄辩模型，并返回保存的模型的集合，这些模型被传递到最后一步:

```
<?php

namespace App\Stages;

use App\Contracts\Services\MailingList;
use Illuminate\Support\Collection;

class AddDataToList
{
    protected $list;

    public function __construct(MailingList $list)
    {
        $this->list = $list;
    }

    public function __invoke(Collection $data)
    {
        return $data->each(function ($item) {
            $this->list->add([
                'name' => $item->name,
                'email' => $item->email
            ]);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这一步使用邮件服务的包装类，它作为构造函数中的依赖项传递。然后,`__invoke()`方法遍历每个雄辩模型并使用它来获取数据，然后将数据添加到列表中。完成我们的阶段后，我们现在可以将它们放在我们的控制器中:

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Stages\FormatData;
use App\Stages\SaveData;
use App\Stages\AddDataToList;
use League\Pipeline\Pipeline;
use Illuminate\Support\Collection;

class WebhookController extends Controller
{
    public function store(Request $request, Pipeline $pipeline, FormatData $formatData, SaveData $savedata, AddDataToList $addData)
    {
        try {
            $data = Collection::make($request->get('data'));
            $pipe = $pipeline->pipe($formatData)
                ->pipe($saveData)
                ->pipe($addData);
            $pipe->process($data);
        } catch (\Exception $e) {
            // Handle exception
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，我们提取请求数据(假设是一个 webhook 的数据数组)，并将其转换为一个集合。然后，我们整合我们的管道。注意，我们使用依赖注入来获取步骤——可以随意使用适当的方法或构造函数注入。我们实例化我们的管道，并多次调用`pipe()`方法来添加新的阶段。

最后，我们通过调用`process()`方法，传入初始数据，将数据传递给管道进行处理。请注意，我们可以将整个事情包装在一个`try...catch`语句中来处理异常，因此如果发生了意味着我们想要在该点停止处理的事情，我们可以在阶段中抛出一个异常，并在管道之外处理它。

这意味着我们的控制器非常简单。它只是将数据作为一个集合，然后将管道放在一起并传递数据。如果我们随后必须从命令行编写 Artisan 任务来做类似的事情，我们可以通过 CSV reader 类获取数据，然后将其传递给相同的管道。如果我们需要改变初始数据的格式，我们可以用一个单独的类来代替`FormatData`类，这没什么麻烦。

你可以用联盟管道包做的另一件事，但我还没有机会去尝试，就是使用`League\Pipeline\PipelineBuilder`以一种更动态的方式建立管道。您可以使步骤有条件，如下例所示:

```
<?php

use League\Pipeline\PipelineBuilder;

$builder = (new PipelineBuilder)
    ->add(new FormatData);
if ($data['type'] = 'foo') {
    $builder->add(new HandleFooType);
}
$builder->add(new SaveData);
$pipeline = $builder->build();
$pipeline->process($data); 
```

Enter fullscreen mode Exit fullscreen mode

管道模式并不适合每种情况，但是对于涉及对相同数据的一组操作的任何情况，它都很有意义，并且可以很容易地将较大的操作分解为较小的步骤，以便于理解、测试和重用。