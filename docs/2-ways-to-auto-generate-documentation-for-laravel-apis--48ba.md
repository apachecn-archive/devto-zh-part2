# 自动生成 laravel apis 文档的两种方法

> 原文：<https://dev.to/whoisryosuke/2-ways-to-auto-generate-documentation-for-laravel-apis--48ba>

最近，Kushy API 终于发布了 v1 的测试版，我重新设计了 Kushy API 文档以使用 GatsbyJS。文档本身是用 Markdown 手写的，其结构受大多数 API 文档的启发(带有描述、代码片段和响应示例的端点)。

这很快变得非常乏味，当 API 发生任何小的更新并需要手动修改单独的文档报告时，这立即成为一个问题。是时候从我们的代码库自动生成我们的文档，并在 Laravel 框架内发现实现这一点的最佳方式了。

经过一些研究后，选择相当少:

1.  使用 API 规范 **Swagger** 来记录我们的代码并输出一个 Swagger JSON 文件。
2.  使用 **docblocks** 记录我们的代码，并找到一个生成器来扫描代码并将其转换为 Markdown。

## 对霸气说不

我不喜欢 PHP 中的 Swagger 实现。写文档看起来很糟糕，更难阅读，并且给我的产品代码库增加了过多的代码。我宁愿为 PHP 编写标准的文档块，并将它们作为 API 文档的基础。

### “去你的，我更喜欢招摇”

这很酷，我理解 Swagger 规范的轻松。能够运行脚本并生成可以导入到其他库/框架/生成器中的 JSON 文件真是太棒了。如果你喜欢使用 Swagger，我推荐使用 [InfyOm Laravel 生成器](https://github.com/InfyOmLabs/laravel-generator)。

您可以用一个相当简单的 CLI 生成整个 API。在一个简单的`artisan`命令和几个回答的问题中，您拥有了一个控制器、模型、迁移、资源、验证和 API 测试。它还可以为它创建的 API 控制器生成 Swagger 认可的文档，只需对配置进行简单的更改。

## 轻松编写文档

幸运的是，有一个包可以完成我们需要的功能- [Laravel API 文档生成器](https://github.com/mpociot/laravel-apidoc-generator)。它扫描您的 API 端点，并实际运行它们来生成文档(自动为您的文档创建 JSON 响应对象)。它将控制器的类/方法之上的 PHP 文档块与请求验证、JSON 响应和目录结合起来。

如果您运行的是 Laravel 5.5+，只需运行下面这一行来安装包:

```
composer require mpociot/laravel-apidoc-generator 
```

Enter fullscreen mode Exit fullscreen mode

并使用 Artisan CLI 命令开始生成文档:

```
php artisan api:generate --routePrefix="api/*" 
```

Enter fullscreen mode Exit fullscreen mode

使用这个包有很多好处:

### 自定义书写内容

你不局限于自动生成的文档，这个包也能够附加其他 Markdown 文件的内容。非常适合编写额外的认证文档。

### 认证 API 请求

它甚至处理脏进程，比如认证 API 调用:

```
php artisan api:generate --routePrefix="api/*" --actAsUserId=1 
```

Enter fullscreen mode Exit fullscreen mode

### 邮递员

这个包还为查询 API 的 app[Postman](https://www.getpostman.com/)生成一个 JSON‘collection’文件。如果你像我一样用它来测试你的 API，那就方便了。[您可以导入集合](https://www.getpostman.com/docs/v6/postman/collections/creating_collections)，并拥有一个包含您所有端点的文件夹。

### 不评论码？放心吧！

由于这个包分析了您的整个路由文件并查询每条路由的数据，它将为每条路由生成 API 文档——即使它没有文档/文档块/注释！生成器创建一个“general”部分，其中包含您没有在*“resources”*中分组的所有端点。

## 自动生成的问题

从一开始，我就对集成文档生成器感到不安。自动化有几个陷阱，导致更多的工作由开发人员完成，或者更糟，代码被极大地重构。

### 一个巨大的降价文件

尽管能够用 API 生成器将东西分成“资源”，但是生成器创建了一个巨大的降价文件。每个“资源”或 API 控制器都被分成几个部分，但是没有单独的文件，这使得用 GatsbyJS 这样的框架进行解析更加困难。

### 没有评论块？不准确的文件。

我在使用 docblock 和*甚至 Swagger documentation* 时遇到的最初问题是需要一个物理文件，其中包含每个端点的注释。

如果我使用一个基类来扩展我的 API 控制器会怎么样？我所有的主要端点(索引、存储等)都存储在基本控制器中，我只在实际的端点控制器中添加了几个额外的端点。

基本控制器:

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

abstract class ApiController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index(Request $request)
    {
        // Does API stuff
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

生成器使用基类中的文档块组合扩展类中的注释。问题？除非我们在端点控制器中重新设置方法，否则我们会得到从基类扩展的任何端点的一般注释。

这导致了重复的代码、多余的代码和对创新的限制。

## 手写>文档块>大摇大摆

除非您的 API 是为它设置的，或者您正在使用一个不断变化的庞大 API，否则最好还是手工编写文档。

**使用生成器作为起点很好，**因为它将生成所有的端点/请求对象等。但是对于*的长期*维护来说，似乎就连发电机也不是完全自动化的——需要如此多的维护以至于超过了它的效用。

然而，如果你正在寻找一个不是基于 Swagger 的生成器，mpociot 的 laravel-API doc-generator(T2)做了足够扎实的工作来生成文档。**如果你对 Swagger** 没意见，InfyOmLabs 的 Laravel 生成器可以很好地创建 API 所需的一切(控制器、验证、模型、迁移、测试，一切！).

## (对我来说)成功

我遇到的主要问题是降价的生成格式。这是一个单一的大文件，与 Slate 等文档生成器兼容，但与我的 GatsbyJS 设置不兼容。对于我的 Gatsby 文档，它们是从一个`docs`文件夹中生成的，每个资源组有一个子文件夹(`docs/endpoint/`)。然后我们为每条路线创建一个文件(`docs/posts/get-all-posts.md`)。

### 使用 Javascript

我的第一反应是编写一个 NodeJS 脚本来解析大量的 Markdown 文件，并把它分成我需要的块。我使用 NodeJS 读取文件内容，使用 markdown-js 将 MD 解析为可遍历的树，然后解析为 HTML，使用 turndown 将 HTML 转换回 MD

```
var fs = require('fs');
var markdown = require("markdown").markdown;
var TurndownService = require("turndown");
var turndownService = new TurndownService();

// Grab API markdown master file and dump file into variable
let docs = fs.readFileSync('public/docs/source/index.md', 'utf8');

// Parse the markdown into a JSONML tree 
// (giant array with objects that contain MD data)
// [
//     {
//         'header',
//         {
//             level: 1
//         },
//         'The actual header content'
//     },
// ]
var tree = markdown.parse(docs);
var i = 0;
var separateDocs = [];

// Loop through tree to find headers
// Whenever header is found, insert last collection, 
// and make new collection variable to insert ongoing data into
// Each collection should contain name (used for filename) and data
var collection = [];
tree.forEach(function(element) {
    if(element[0] === 'header')
    {
        if(element[1].level === 1)
        {
            // Put collection the main doc array
            // Then wipe it out and start fresh
            separateDocs.push(collection);

            collection = [
                'markdown'
            ]
        }
    }

    collection.push(element);

    i++
});

// console.log(separateDocs);

// Convert from JSONML to HTML then to MD
// Then save the file
var convertedDocs = []
separateDocs.forEach(function(doc) {
    console.log(doc[1][0])
    var header
    if(doc[1][0] == 'header') {
        header = doc[1][2];
    } else {
        header = 'index';
    }
    var html = markdown.renderJsonML(markdown.toHTMLTree(doc));
    var convertedMarkdown = turndownService.turndown(html);
    convertedDocs.push(convertedMarkdown);

    fs.writeFile(`tmp/${header}.md`, convertedMarkdown, function (err) {
        if (err) {
            return console.log(err);
        }

        console.log("The file was saved!");
    }); 

}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用 CLI 运行该脚本:`node generate-api-docs.js`

经过一点修补，我发现我可以完成我所需要的，但原始材料变得退化了。从 MD -> JSON -> HTML -> MD 的转换导致了格式问题。

### 回到源头

我意识到修改最终产品并不可取，编辑生成器的输出代码会更容易。在四处窥探源代码后，我找到了我需要的方法:`writeMarkdown()`。稍加修改，我最终得到了这个结果:

```
 /**
     * @param  Collection $parsedRoutes
     *
     * @return void
     */
    private function writeMarkdown($parsedRoutes)
    {
        $outputPath = $this->option('output');

        $infoText = view('apidoc::partials.info')
            ->with('outputPath', ltrim($outputPath, 'public/'))
            ->with('showPostmanCollectionButton', ! $this->option('noPostmanCollection'));

        $parsedRouteOutput = $parsedRoutes->map(function ($routeGroup) {
            return $routeGroup->map(function ($route) {
                $route['output'] = (string) view('apidoc::partials.route')->with('parsedRoute', $route);

                return $route;
            });
        });

        $documentarian = new Documentarian();

        $parsedRouteOutput->each(function ($routeGroup) use ($infoText, $outputPath) {
            $routeGroup->each(function ($route) use ($infoText, $outputPath) {
                // dd($route);

                // $cleanResource = preg_replace('/\s+/', '', $route['resource']);
                $routeUrl = str_replace('api/v1/', '', $route['uri']);
                $safeUrl = str_replace('/', '-', $routeUrl);
                $safeUrl = str_replace('{', '', $safeUrl);
                $safeUrl = str_replace('}', '', $safeUrl);
                $cleanResource = explode('\n', $route['resource']);
                $cleanResource = $cleanResource[0];
                // $cleanResource = trim(preg_replace('/\s+/', ' ', $route['resource']));
                $title = strtolower($route['methods'][0] . '-' . $safeUrl);

                $folder = $outputPath.DIRECTORY_SEPARATOR.'dist'.DIRECTORY_SEPARATOR.strtolower($cleanResource);
                $this->createFolder($folder);
                $targetFile = $folder.DIRECTORY_SEPARATOR.$title.'.md';

                $frontmatter = view('apidoc::partials.frontmatter')
                    ->with('method', $route['methods'][0])
                    ->with('route', $routeUrl)
                    ->with('title', $route['title'])
                    ->with('date', date('Y-m-d'));

                $markdown = view('apidoc::singledoc')
                    ->with('writeCompareFile', false)
                    ->with('frontmatter', $frontmatter)
                    ->with('infoText', $infoText)
                    ->with('outputPath', $this->option('output'))
                    ->with('showPostmanCollectionButton', ! $this->option('noPostmanCollection'))
                    ->with('route', $route)
                    ->with('group', $cleanResource);

                // Write output file
                file_put_contents($targetFile, $markdown);

                $this->info('Wrote '. $title .' to: '.$outputPath);
            });
        });

        if ($this->option('noPostmanCollection') !== true) {
            $this->info('Generating Postman collection');

            file_put_contents($outputPath.DIRECTORY_SEPARATOR.'collection.json', $this->generatePostmanCollection($parsedRoutes));
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

确保将这个 helper 方法添加到`GenerateDocumentation`类中(我把它扔在了最下面):

```
 /**
     * Create folder if none exists
     *
     * @param string $folder
     * @return void
     */
    private function createFolder($folder)
    {
        if (!file_exists($folder)) {
            mkdir($folder, 0777, true);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里是视图文件`singledoc.blade.php` :

```
---
{!! $frontmatter !!}
---

@if($group)
# {{$group}}
@endif
@if($writeCompareFile === true)
{!! $route['output']!!}
@else
{!! isset($route['modified_output']) ? $route['modified_output'] : $route['output']!!}
@endif 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们通过`$routeGroupsOutput`进行循环，这是一个按组名排序的路由集合。
2.  然后，我们遍历每个组，以获得每个资源的路由(文档块中标记为`@Resource`的每个部分)。
3.  我们用我们的路线数据填充一个视图文件，然后将视图保存到一个 Markdown 文件(使用`file_put_contents()`)。

> 我暂时移除了比较功能。通常这个包会创建两个 Markdown 文件，并比较不同版本的差异，但是我还没有尝试让它与新的设置一起工作。

### 结果

稍微编辑了一下这个包之后，我能够完成我所需要的，并且创建——至少——我的 API 文档的基础。理想情况下，如果我解决了这些问题，这可以作为一个自动生成文档的解决方案。但是，嘿，我们会看到在实践中效果如何。当超越思想并付诸行动时，事情总是不一样的。

**目前为止的问题**:

*   本地开发 URL 显示在查询的 JSON 结果中
*   无法使用用户 ID 验证请求。不确定是因为我使用 UUIDs 还是我的中间件设置。
*   摆脱 HTML 比较注释
*   删除`public/docs/`文件夹并确保它不会生成
*   安装分叉包(或创建一个新包到`composer install`)

你可以在这里找到[我的 Laravel API 文档生成器的 fork。](https://github.com/whoisryosuke/laravel-apidoc-generator)

希望这对您的 API 自动化搜索有所帮助！🍻
良

* * *

**参考文献**:

*   [laravel-apidoc-generator](https://github.com/mpociot/laravel-apidoc-generator)
*   [InfyOmLabs Laravel 发生器](https://github.com/InfyOmLabs/laravel-generator)