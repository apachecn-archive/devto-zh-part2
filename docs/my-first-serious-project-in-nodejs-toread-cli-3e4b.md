# 我在 node.js 中的第一个重要项目是读取 cli

> 原文：<https://dev.to/leoat12/my-first-serious-project-in-nodejs-toread-cli-3e4b>

作为一个狂热的读者，我总是有一大串文章，主要是关于发展的，我打算阅读。发展是一个快速的世界，每天都有更多来自时事通讯、Twitter 等的文章堆积起来。我总是在寻找一种工具来放置我的阅读材料。我尝试使用的一些应用程序是 Pocket、Flipboard、Feedly 和其他不太专业的应用程序，如 Trello、Google Keep 等。没有一个真正让我满意，我想要的功能，如通过标签和标题搜索，存档文章等。是由这些服务提供的，但需要订阅。作为一名开发人员，我理解与应用程序相关的成本，但这不是一个重要到让我订阅的应用程序。然后，我诉诸于一个开发者最大的优势:如果你不喜欢市场上的应用程序，就自己构建吧！

该项目仍处于早期阶段，我计划的功能还没有全部开发出来，**欢迎在 [Github](https://github.com/Leoat12/toread-cli)** 上投稿！:D

这里我将解释一点关于代码的结构。这是我在 NodeJS 的第一个“严肃”的项目，在此之前我只写了一些脚本来学习和练习这种语言。这也是我第一次能够体面地将 NodeJS 和 TypeScript 结合起来，这是一种我也正在学习的语言，我非常欣赏它。除了 TypeScript 之外，该项目还有以下主要依赖项:

*   巴比伦式的城市
*   玩笑
*   rx-Http-请求
*   JSDOM
*   骨桥蛋白
*   Commander.js
*   RxJS
*   白垩

其中一些非常直截了当，其他的我会在整篇文章中解释我的决定。两个项目对我帮助很大: [Taskbook](https://github.com/klauscfhq/taskbook) 和 [TypeScript Babel Starter](https://github.com/Microsoft/TypeScript-Babel-Starter#readme) 。第一个是这个项目的灵感，一些依赖和设计决策是基于它做出的。第二个非常有助于我理解项目的结构以及如何配置 Babel 来完成这项工作。非常感谢两位！

这个项目到目前为止已经被分成 5 部分。ts 文件各自具有单独的角色。我正在尽可能地划分职责，以便于扩展和理解。第一个文件是 **index.ts** ，应用的主入口。我使用 Commander.js 描述了这个文件中的所有命令，例如列出所有文章的命令:

```
Commander
    .command('list')
    .alias('ls')
    .description('List all articles')
    .action(() => {
        Actions.getArticles();
    }); 
```

当然，有些命令更复杂并且有参数，但是结构基本上是相同的，并且都指向`Actions`类中的一个方法，这将我们带到下一个文件: **actions.ts**

**actions.ts** 有静态类`Actions`，顾名思义，它实现了应用程序的所有动作，比如获取文章、打开文章、保存文章等。比如上面我们有`Actions.getArticles()`，下面可以详细看到:

```
static storage:Storage = new Storage();

static getArticles() : void{
        let articles:Article[] = this.storage.getArticles();
        articles.forEach(a => {
            Display.printArticle(a, PresentationMode.LIST);            
        });
    } 
```

一般来说，`Actions`类中的一个方法从组成应用程序的其他三个文件中提取类: **article.ts** 、 **storage.ts** 和 **display.ts** ，它们都有非常简单的名字。首先，最简单的一个， **article.ts** 仅仅包含了表示一篇文章的接口:

```
export interface Article{
    id?:number,
    title:string,
    url:string,
    description?:string,
    tags?:string[],
} 
```

**storage.ts** 是存储类所在的地方，这个类负责将数据写入 JSON 文件，我的意图是做一些非常轻量级的事情，也是受我提到的 Taskbook 项目的启发。下面是课堂上的一个片段:

```
 prepareDB(){
        if(!fs.existsSync("file.json")){
            let file : FileStructure = {articles: [], index: 0}
            fs.writeFileSync("file.json", JSON.stringify(file));
        }
    }

    getArticles(): Article[] {
        this.prepareDB();

        let file:FileStructure = JSON.parse(fs.readFileSync("file.json", "utf8"));
        return file.articles;
    } 
```

如果 JSON 文件不存在，总是调用`prepareDB()`来创建它。类的其余部分有方法来做 CRUD，例如上面的`getArticles()`方法。整个存储类基本上依赖于`fs`库和`JSON`常量。实际上，一个花哨的外部依赖是不必要的，尽管我计划改进它，如果必要的话，除了别的以外，还可以使用加密技术。

最后， **display.ts** 包含了`Display`类，负责与屏幕上的打印相关的一切。它用`chalk`把它弄得五颜六色。作为一个简单的例子，这里有一个打印错误消息的方法:

```
static printOpenErrorMessage(){
        let message = 'The article was not found. Verify the ID of the article.';
        console.info(chalk`{red.bold ${message}}`);
    } 
```

正如我以前说过的，分离关注点是基础架构的主要目标，有时我认为我分离得太多了，但我对现在的方式很满意。至于类和方法本身，我试图用尽可能少的依赖和尽可能简单的方式编写代码，当我还在学习的时候就更是如此。现在是解释一些仍然缺乏解释的依赖关系的好时机。例如，在下面的代码中保存一篇新文章时会用到 RxJS 和 JSDOM:

```
static saveArticle(url: string, description: string, tags?: string) : void{

        RxHR.get(url).subscribe(
            (data:any) => {
                if (data.response.statusCode === 200) {
                    let window = (new JSDOM(data.body)).window;
                    let title = window.document.title;
                    let article:Article = {
                        title: title, 
                        url: url,
                        description: description,
                        tags: tags ? tags.split(',') : []
                    };

                    Actions.storage.saveArticle(article);

                    Display.printSaveArticleMessage(data.response.statusCode);
                    Display.printArticle(article, PresentationMode.ONE);
                } else {
                    Display.printSaveArticleMessage(data.response.statusCode);
                }
            },
            (err:any) => console.error(err) // Show error in console
        );
    } 
```

如上所述，我使用 RxJS、RxHR 和 JDOM 向用户给定的 URL 发出请求，获取页面的标题并存储包含这些信息的文章。对我来说，这是唯一一次有必要在整个应用程序中使用 RxJS，但其他机会可能会出现。

最后，在测试端，我使用 Jest，这是我在开发应用程序时发现的，我发现测试和执行的方式非常简单。也许它比我在 Java 中习惯的功能更多，但它仍然提醒我 JUnit 的使用方式，所以使用它是一帆风顺的。测试示例如下:

```
test('write', () => {    
    let storage = new Storage();
    storage.saveArticle({title: "Teste", url: "http://www.example.com", description: "Description test"})
    expect(fs.existsSync("file.json")).toBe(true);
    fs.unlinkSync("file.json");
}); 
```

开发这个项目是一次很棒的经历，我期待着每个人对我如何改进它的意见。因为它是作为实践开发的，所以我真的没有想过在 NPM 上发表，但是谁知道未来会怎样呢...你们觉得怎么样？让我知道一切！