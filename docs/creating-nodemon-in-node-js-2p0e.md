# 在 Node.js 中创建 Nodemon

> 原文：<https://dev.to/bnevilleoneill/creating-nodemon-in-node-js-2p0e>

[![](../Images/07f494fdc4ff62ee1485237f6763d7fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bhK0AjbE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOS5a9h-Bgti2tuCHYw9yNA.png)

<figcaption>[nodemon . io](https://nodemon.io/)</figcaption>

每次修改代码库后，手动重启应用程序的进程既令人疲惫又令人沮丧。幸运的是，一位杰出的开发者最终说“够了！”并决定用一个非常简单的包*——*[*Nodemon*](https://github.com/remy/nodemon)*结束疯狂。*

Nodemon 监视给定目录中发生的任何更改，并在每次更改后重新启动我们的应用程序，让我们专注于编写代码。使用 Nodemon 之类的东西是加深我们对文件系统如何工作的理解以及进一步理解 Node.js 环境的一个很好的方式。

### 建筑

当我第一次遇到 Nodemon 时，我不禁想知道如何能够获得关于文件系统变化的通知。

*   我们是否设置了一个时间间隔，并递归地检查给定目录中的所有文件
*   这难道不会占用大量资源，从而实际上减缓我们的工作吗？
*   相反，如果我们*汇集*现有文件并定期检查它们，会怎么样？
*   但是，话又说回来，我们怎么知道正在创建的新文件呢？

虽然这些都是合理的猜测，但 Nodemon 的工作方式有些不同。

如果我们检查 Node.js 文档，我们会看到有一个用于监视文件的 API。它被称为[*fs watcher*](https://nodejs.org/docs/latest/api/fs.html#fs_class_fs_fswatcher)(file system watcher)，它让我们设置一个回调，一旦目录或文件发生变化，就执行这个回调。

在我们开始庆祝这带来的所有可能性之前，让我们来看一个使使用这个 API 变得麻烦的问题。你看，改变一个文件有时可以通知我们两次。这是一件非常麻烦的事情，因为大多数操作系统都是这样设计的*。这反过来意味着我们不能像对待 Node.js 中的常规错误一样对待它，并通过简单地发送一个 [pull 请求](https://github.com/nodejs/node/pulls)来修复它。

* *例如，Windows 记事本在写入过程中执行多个文件系统动作。记事本批量写入磁盘，创建文件内容和文件属性。其他应用程序可能以类似的方式工作。因为 FileSystemWatcher 监视操作系统活动，所以这些应用程序触发的所有事件都将被拾取。*

为了防止重复通知，以及其他一些[已知的 bug](https://github.com/paulmillr/chokidar#why)，我们将使用一个名为 [chokidar](https://github.com/paulmillr/chokidar) 的包。有了这个包为监视文件系统提供的所有改进和修复，我们终于可以开始考虑我们将在应用程序中使用的其他库了。

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 核心

我选择了 [RxJS](https://rxjs-dev.firebaseapp.com/) 作为核心库，因为它使得基于事件的架构易于管理。感谢 observables 和它们的流，我们将不再发现自己在回调地狱中，在我们的事件处理程序中调用一个又一个函数。

与 RxJS 合作围绕着我们所谓的*反应式编程*，其整体思想是基于对可观察对象创建的流进行操作。你可以把一个可观察的事物看作是一种可以返回多个值的承诺。每次事件发生时，一个新的值被传递到我们的流中，导致在*管道*方法中的每个函数都以那个值作为参数被调用。

起初，按照反应式编程范式编写代码可能会感觉奇怪，因为即使是最简单的任务也可能看起来过于复杂。但是过一段时间后，它将成为另一种编程方式——而且是一种有趣的方式。

如果你曾经接触过 *Angular* 的 HTTP 客户端(它使用 *RxJS* ，所有这些看起来都很熟悉。否则，如果你还没有尝试过 *Angular* ，现在就彻底掌握 *RxJS* 的基础可能是个好主意，因为这将使你在未来更快地学习 *Angular* 。

为了更好地理解 RxJS 的基础知识，我建议阅读:

[http://reactivex.io/rxjs/manual/overview.html](http://reactivex.io/rxjs/manual/overview.html)

[https://rxjs-dev.firebaseapp.com/guide/overview](https://rxjs-dev.firebaseapp.com/guide/overview)

#### 命令行用法

我们希望我们的应用程序可以像我们的控制台中的命令一样访问，就像最初的 _ Nodemon_:

```
npm run start -- -e ts,js,html -w src -i build -d 5000 -x "ls -l" 
```

当然，稍后——在发布我们的包之后——NPM run start 将被我们的包的名称替换，例如我们的-nodemon。

#### 格式化

我们将加入一些颜色，只是为了好玩。

继续，复制[库](https://github.com/maciejcieslar/nodemon)，让我们开始吧。

### 实现

#### 获取并解析参数

每次我们执行一个脚本时，我们都可以给它传递参数。传递给我们的进程的所有参数都可以在名为 *process.argv* 的变量中找到。

所以现在，在我们继续看实际的实现之前，让我们考虑一下我们希望用户传递给我们的应用程序的到底是什么。以下是我想到的一些论点:

*   *–分机* —要观看的分机
*   *–观察* —要观察的目录
*   *–忽略* —要忽略的目录
*   *–延迟* —重启的延迟时间，单位为毫秒
*   *–exe*—每次重新启动的脚本

请注意，只有*–exe*参数是必需的，因为其他选项都可以设置为默认值。

我们可以用很多方法解析参数，但最简单的方法是使用 [*指挥官*](https://www.npmjs.com/package/commander) 。一个*指挥官*解析参数并给我们能力以一种非常简洁的方式声明它们，就像我们在上面的列表中所做的一样:

```
const commandArguments = program
  .version('1.0.0')
  .option('-e, --ext <items>', 'Extensions to watch', parsers.list)
  .option('-w, --watch <items>', 'Directories to watch', parsers.list)
  .option('-i, --ignore <items>', 'Directories to ignore', parsers.list)
  .option('-d, --delay <n>', 'Delay before the execution', parsers.int)
  .option('-x, --exe <script>', 'Execute script on restart')
  .parse(process.argv) 
```

*我们命令的参数*

这里我们可以看到*解析器*对象的一些方法。一个*指挥官*需要它们来将作为字符串传递的参数改变为我们期望的格式:

```
const parsers = {
  int: (number: string) => parseInt(number, 10),
  float: (number: string) => parseFloat(number),
  list: (val: string) => val.split(','),
  collect: (val, memo) => {
    memo.push(val)

    return memo
  },
} 
```

*解析器*

现在，我们已经将所有参数传递给了应用程序中的*执行*变量。一切看起来都很好，但我们也希望有默认参数，并在*–exe*参数丢失时抛出错误。我们可以用一个简单的函数 *parseArguments* :
来确保满足上述所有需求

```
const parseArguments = (execution: program.Command) => {
  const { ext = [], watch = [], ignore = [], delay = 0, exe } = execution

  if (!exe) {
    throw new Error('No script provided')
  }

  return {
    delay,
    extensions: ext.map((e) => `.${e}`),
    watchedDirectories: watch,
    ignoredDirectories: [...ignore, 'node_modules', 'build'],
    shouldWatchEveryDirectory: not(watch.length),
    shouldWatchEveryExtension: not(ext.length),
    script: exe,
  }
} 
```

*parseArguments*

#### 倾听变化

有了 *chokidar，*就很容易听出变化。我们所要做的就是简单地为*变更*事件设置一个监听器，如下所示:

```
fromEvent(chokidar.watch(process.cwd()), 'all').pipe(
  // ...
) 
```

*手表*

注意，我们使用了 dirname。这是因为我们希望观察应用程序执行目录中的文件，而不是 *Nodemon* 的代码所在的位置。

此外，这里我们可以看到第一次使用一个*RxJS*observable。 *fromEvent* 函数允许我们监听 *change* 事件。每次执行*监听器*时，都会有一个值被发送到我们的流中。

我们*通过一组函数来传输*我们的流，例如，这些函数可以更改(*映射*)或拒绝(*过滤器*)值。既然有很多，下面就来个有用的[小抄](https://www.learnrxjs.io/)。

#### 获取关于文件的信息

*Chokidar* ，在每次活动中，将为我们提供以下信息:

*   事件的名称
*   已更改文件的路径

我们对事件的名称并不感兴趣，但是我们确实关心文件的路径。收到文件的路径后，我们可以检查该文件是否会导致脚本重启。

然而，仍然有一个问题——也就是说，路径的格式如下:

```
/Users/wow/repositories/awesome-app/service/blabla/index.ts 
```

这是我们想要的样子:

```
service/blabla/index.ts 
```

不要担心——之前使用的 *process.cwd()* 为我们提供了路径:

```
/Users/wow/repositories/awesome-app 
```

因此，我们可以从 chokidar 的路径中删除重复的部分，并获得应用程序目录的相对路径。

```
fromEvent(chokidar.watch(process.cwd()), 'all').pipe(
  // ...
  map(([event, filePath]: string[]) => {
    const filename = path.basename(filePath)
    const extension = path.extname(filename)

    return {
      event,
      filename,
      extension,
      filePath: filePath.slice(process.cwd().length + 1),
    }
  }),
  // ...
) 
```

*映射事件*

拥有相对于应用程序目录的路径可以很容易地过滤掉对被忽略文件的更改。

#### 只看需要的文件

通过提供*–watch*参数，用户可以指定他们感兴趣的目录。

例如:-观察 src，assets 意味着我们忽略在目录 *src* ， *assets* 之外所做的所有更改。请注意，仍然可以忽略特定的子目录。

通过比较 _，–ignore _ 让我们设置希望被忽略的目录。默认情况下，我们将把 *node_modules* 和 *build* 添加到用户提供的目录中，因为我们总是希望它们被忽略。

```
const isInDirectory = (directories: string[]) => (filePath: string) =>
  directories.some(filePath.startsWith.bind(filePath))

const isExpectedExtension = (extensions: string[]) => (extension: string) =>
  extensions.some(is(extension))

// ...

const shouldPathBeIgnored = isInDirectory(ignoredDirectories)
const shouldPathBeWatched = isInDirectory(watchedDirectories)
const shouldExtensionBeWatched = isExpectedExtension(extensions)

// ...

fromEvent(chokidar.watch(process.cwd()), 'all').pipe(
  // ...
  filter(
    ({ filePath, extension }) =>
      (shouldWatchEveryDirectory || shouldPathBeWatched(filePath)) &&
      (shouldWatchEveryExtension || shouldExtensionBeWatched(extension)) &&
      not(shouldPathBeIgnored(filePath)),
  ),
  // ...
) 
```

*过滤器*

接受文件的规则可以如下所示:

1.  我们会看每个目录吗？如果没有，检查该目录是否被监视。
2.  我们看每一个扩展吗？如果没有，检查是否正在监视扩展。
3.  该忽略路径吗？如果是，对结果求反得到*假*。

如果过滤器返回 false，该值将不会在流中继续前进，并将被丢弃。我们可以确定从那时起我们只处理我们感兴趣的文件。

#### 拖延执行

有时我们会逐个保存几个文件。这可能会导致脚本多次重启，从而消耗大量处理能力。我们可以在发出最后一个 *change* 事件后等待一秒钟，这样脚本只重新启动一次。

持续延迟给定动作的技术被称为*去抖动*。

```
fromEvent(chokidar.watch(process.cwd()), 'all').pipe(
  debounceTime(delay || 1000),
  // ...
) 
```

*去抖*

*去抖*函数实际上是作为第一个函数放在我们的*管道*中的，所以我们不会做任何事情，直到我们百分之百确定我们必须这样做。

#### 显示消息

在我们执行脚本之前，最好给消息涂上颜色并加入一些表情符号。为此我们需要一个小帮手

```
const message = (content: string, color: string) => {
  const msg = emoji.emojify(content)

  if (colors[color]) {
    return colors[color](msg)
  }

  return msg
} 
```

*消息助手*

#### 执行提供的脚本

*Node.js* 使我们能够通过使用 *child_process* 模块——即 *spawn* 函数来执行命令。

在生成提供的脚本后，我们需要保存一个对它的实例的引用，这样我们就可以在每次我们关注的文件发生变化时杀死它并再次生成它。

```
const createScriptExec = (script: string) => {
  let instance = null

  return async function execute() {
    if (instance) {
      await kill(instance, 'SIGKILL')
    }

    instance = spawn(script, [], { shell: true })

    return merge<String, String>(
      fromEvent(instance.stderr, 'data').pipe(
        map((data) => message(data.toString(), 'red')),
      ),
      fromEvent(instance.stdout, 'data').pipe(
        map((data) => message(data.toString(), 'cyan')),
      ),
    ).pipe(takeUntil(fromEvent(instance, 'close')))
  }
}

// ...

const executeScript = createScriptExec(script)

// ...

fromEvent(chokidar.watch(process.cwd()), 'all').pipe(
  // ...
  switchMap(executeScript),
  tap(() => console.log(message('Executing...', 'green'))),
  switchMap((obsvr) => {
    return obsvr.pipe(
      tap(console.log),
      reduce(() => null),
    )
  }),
  tap(() => console.log(message('Finished! :fire:', 'green'))),
) 
```

*执行脚本*

作为调用 *executeScript，*的结果，我们得到了一个可观察的流，它记录了已执行脚本的日志。脚本中的日志( *stdout* )以青色记录。我们用红色来记录应用程序的错误( *stderr* )，用绿色来记录应用程序创建的日志。这样，日志的来源就更清楚了。

在第 30 行，我们使用 *switchMap* 来获得上面提到的可观察值。我们通过 tap(console.log)管道传输它返回的日志，将所有内容记录到控制台，并减少(()= > null)来等待可观察对象完成并继续前进。

观察完成后，我们向用户记录脚本已经执行。

### 总结

恭喜你，你刚刚使用了一种全新的编程方式来构建一个应用程序！

关于 [Reactive Extensions 的 API](http://reactivex.io/) (在 JavaScript 中以 RxJS 的形式提供)的伟大之处在于它的普遍性，这使得您希望在这里学到的一些知识可以很容易地应用于其他编程语言。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

在 Node.js 中创建 Nodemon 的帖子[首先出现在](https://blog.logrocket.com/creating-nodemon-in-node-js-70b295c2610c/)[博客](https://blog.logrocket.com)上。