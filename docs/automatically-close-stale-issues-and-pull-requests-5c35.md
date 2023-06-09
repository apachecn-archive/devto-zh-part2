# ★自动关闭过期问题和拉式请求

> 原文：<https://dev.to/freekmurze/automatically-close-stale-issues-and-pull-requests-5c35>

在 [Spatie](https://spatie.be) 我们[有超过 180 个公共存储库](https://github.com/spatie)。我们有的套餐[有](https://packagist.org/packages/spatie/laravel-backup/stats) [变](https://packagist.org/packages/spatie/laravel-permission/stats) [相当](https://packagist.org/packages/spatie/laravel-fractal/stats) [流行](https://packagist.org/packages/spatie/laravel-newsletter/stats)。我们非常感谢我们的许多用户打开 [issues](https://github.com/issues?utf8=%E2%9C%93&q=is%3Aissue+org%3Aspatie) 和 [PRs](https://github.com/issues?utf8=%E2%9C%93&q=is%3Apr+org%3Aspatie+) 来提问、通知我们问题并尝试解决这些问题。...

这些问题和 pr 大多由我们的团队处理。但有时这些问题和公关变得陈旧。原作者可能不再回应，讨论可能会终止，或者有时这个问题并不重要，不值得我们投入时间。手动跟踪那些陈旧的问题和 PRs，并在一段时间后关闭它们需要一些努力，而且并不真正有趣。

这就是为什么我们创造了一个机器人，可以自动关闭陈旧的问题和公关。这里有一张[机器人在行动](https://github.com/spatie/laravel-tags/issues/79)的截图。

[![bot closing an issue](img/618ae087f40b673234f5f1ef42c1e6b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gSCDd-xN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/github-bot/bot-comment.png)

在这篇文章中，我想分享这个机器人的代码。

## 显示代码

在我们公司，有一个机器人生活在我们的闲暇时间。它由[马塞尔·波西奥特](https://twitter.com/marcelpociot)出色的[伯特曼](https://botman.io)套装提供动力。它可以为我们的团队做很多有用的事情。自动关闭的 GitHub 问题和 PRs 代码部分并没有真正利用任何 BotMan 特定的功能。因为不涉及交互，所以自动关闭是通过一个简单的 artisan 命令来完成的。

为了与 GitHub 通信，我们使用了优秀的 [knplabs/github-api](https://github.com/KnpLabs/php-github-api) 包。这是引导该包的服务提供商。

```
namespace App\Providers;

use Github\Client;
use App\Services\GitHub\GitHub;
use Illuminate\Support\ServiceProvider;

class GitHubServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->singleton(Client::class, function () {
            $client = new Client();

            $client->authenticate(config('services.github.token'), null, Client::AUTH_HTTP_TOKEN);

            return $client;
        });

        $this->app->singleton(GitHub::class, function () {
            $client = app(Client::class);

            return new GitHub($client);
        });
    }
} 
```

`config('services.github.token')`包含一个在 GitHub 上使用[为我们的机器人](https://github.com/spatie-bot)创建的新专用账户[创建的令牌。](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)

为了方便使用 API 和自动分页结果，我在它周围创建了一个薄薄的包装器。

```
namespace App\Services\GitHub;

use Github\Client;
use Github\ResultPager;
use Illuminate\Support\Collection;

class GitHub
{
    /** @var \Github\Client */
    protected $client;

    /** @var \Github\ResultPager */
    protected $paginator;

    public function __construct(Client $client)
    {
        $this->client = $client;

        $this->paginator = new ResultPager($client);
    }

    public function search(string $for, string $query): Collection
    {
        $api = $this->client->api('search');

        $repos = $this->paginator->fetchAll($api, $for, [$query]);

        return collect($repos);
    }
} 
```

这是关闭问题和 PRs 的实际 artisan 命令。它将关闭过去 6 个月中未激活的每个问题和 PR。如果问题/请购单有标签`enhancement`、`help wanted`或`bug`，我们将保持开放。这个命令是[安排](https://laravel.com/docs/5.6/scheduling)每天运行的。

```
namespace App\Console\Commands;

use App\Services\GitHub\Issue;
use App\Services\GitHub\GitHub;
use Illuminate\Console\Command;

class CloseInactiveGithubIssuesAndPRs extends Command
{
    protected $signature = 'bot:close-inactive-github-issues-and-prs';

    protected $description = 'Close inactive GitHub issues and PRs';

    /** @var \App\Services\GitHub */
    protected $gitHub;

    public function __construct(GitHub $gitHub)
    {
        parent::__construct();

        $this->gitHub = $gitHub;
    }

    public function handle()
    {
        $this->info('Start closing issues and PRs...');

        $sixMonthsAgo = now()->subMonths(6);

        $this->gitHub
            ->search('issues', "org:spatie is:public state:open updated:<{$sixMonthsAgo->format('Y-m-d')} ")
            ->map(function (array $issueAttributes) {
                return Issue::create($issueAttributes);
            })
            ->reject(function (Issue $issue) {
                return $issue->hasLabel(['enhancement', 'help wanted', 'bug']);
            })
            ->each(function (Issue $issue) {
                $issue->close('Dear contributor, ' . PHP_EOL . PHP_EOL . "because this {$issue->type()} seems to be inactive for quite some time now, I've automatically closed it. If you feel this {$issue->type()} deserves some attention from my human colleagues feel free to reopen it." );

                $this->comment("Closed {$issue->url()}");
            });

        $this->info('All done');
    }
} 
```

你会注意到我们将把来自 GitHub 的响应映射到`Issue`对象，这样我们可以更容易地处理它。GitHub 考虑公关。只是另一种类型的问题。下面是`Issue`类的代码。

```
namespace App\Services\GitHub;

use Github\Client;

class Issue
{
    /** @var array */
    protected $issueAttributes;

    /** @var \App\Services\GitHub\GitHub */
    protected $gitHub;

    public static function create(array $issueAttributes): self
    {
        return app(static::class)->setAttributes($issueAttributes);
    }

    public function __construct(Client $gitHub)
    {
        $this->gitHub = $gitHub;
    }

    public function setAttributes(array $issueAttributes)
    {
        $this->issueAttributes = $issueAttributes;

        return $this;
    }

    public function number(): int
    {
        return $this->issueAttributes['number'];
    }

    public function repositoryName(): string
    {
        return array_reverse(explode('/', $this->issueAttributes['repository_url']))[0];
    }

    /**
     * @param string|array $searchLabels
     *
     * @return bool
     */
    public function hasLabel($searchLabels): bool
    {
        $searchLabels = array_wrap($searchLabels);

        $foundLabels = array_intersect($this->labelNames(), $searchLabels);

        return count($foundLabels) > 0;
    }

    public function labelNames(): array
    {
        return collect($this->issueAttributes['labels'])->pluck('name')->values()->toArray();
    }

    public function type(): string
    {
        return array_has($this->issueAttributes, 'pull_request')
            ? 'pull request'
            : 'issue';
    }

    public function url(): string
    {
        return $this->issueAttributes['html_url'];
    }

    public function close(string $message)
    {
        $this->gitHub
            ->api('issue')
            ->comments()
            ->create('spatie', $this->repositoryName(), $this->number(), ['body' => $message]);

        $this->gitHub
            ->api('issue')
            ->update('spatie', $this->repositoryName(), $this->number(), ['state' => 'closed']);
    }
} 
```

## 在关闭

我希望你喜欢这次自动关闭机器人背后的代码之旅。它将在[我们所有的公开回复](https://github.com/spatie)中激活。