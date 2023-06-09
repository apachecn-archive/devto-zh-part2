# 让 CLI 在控制台中运行

> 原文：<https://dev.to/yysun/make-cli-run-in-the-console-42ho>

我们一直在终端窗口和命令提示符中使用命令行界面(CLI)。你有没有想到在浏览器的开发者工具的控制台中使用 CLI？

我最近为 [AppRun](https://github.com/yysun/apprun) 制作了一个。

AppRun 是一个 3K 库，用于使用 Elm 启发的架构、事件发布订阅和组件来构建应用程序。

[![CLI in the console](img/29a58318d08fd9a0d459585e1764e42b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5p8ESaes--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/khumq8np94i5uwo9bwn1.png)

# 它是如何工作的？

在浏览器开发工具(F12)的控制台中，您可以键入命令。

```
_apprun `<command> [options]` 
```

Enter fullscreen mode Exit fullscreen mode

就像许多其他 CLI 一样， *help* 命令列出了所有可用的命令。在上面的截图中，你可以看到有三个命令:*组件*、*事件*和*日志*。

# 为什么我们在控制台中需要 CLI？

控制台中的 CLI 便于查看运行时数据。在应用程序开发过程中，我们经常需要调试和检查应用程序的内部数据。使用 *console.log* 函数是显示数据最简单也是最强大的方法，因为控制台允许我们深入到嵌套的数组和对象结构中。

[![Drilldown](img/68e868988f7362bd694bf13abb4e4f43.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WJro7zaV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fq37a5rjfoz4pqsi0f05.png)

有了控制台中的 CLI，应用程序代码库就不会受到 *console.log* 的影响。CLI 提供了一种查看运行时数据的非破坏性方法。我们可以将 CLI 脚本包含在开发环境中，并将其从生产环境中移除。

# 是怎么做出来的？

在控制台中创建一个 CLI 比创建一个开发工具作为浏览器扩展相对容易。它基于 JavaScript [标记的模板](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_templates)。

我们在*窗口*对象中创建了 *_apprun* 函数。

```
window['_apprun'] = (strings) => { } 
```

Enter fullscreen mode Exit fullscreen mode

当我们在控制台中键入 apprun 命令时，就会调用 *_apprun* 函数。命令和命令参数作为函数参数*字符串*传递给 *_apprun* 函数，我们可以解析并调用命令函数。

```
window['_apprun'] = (strings) => {
  const [cmd, ...p] = strings[0].split('  ').filter(c => !!c);
  const command = window[`_apprun-${cmd}`];
  if (command) command[1](...p);
  else window['_apprun-help'][1]();
} 
```

Enter fullscreen mode Exit fullscreen mode

它有一个广泛的架构。我们在*窗口*对象中创建 AppRun 命令。AppRun 命令是一个元组，包括命令的描述和命令的实现功能。例如，帮助命令看起来像这样:

```
window['_apprun-help'] = ['', () => {
  Object.keys(window).forEach(cmd => {
    if (cmd.startsWith('_apprun-')) {
      cmd === '_apprun-help' ?
        console.log('AppRun Commands:') :
        console.log(`* ${cmd.substring(8)}: ${window[cmd][0]}`);
    }
  });
}]; 
```

Enter fullscreen mode Exit fullscreen mode

*help* 命令搜索存储在*窗口*对象中的元组，并打印其他 AppRun 命令的描述。

这就是我们在控制台中创建 CLI 命令所需的所有基础架构代码。让我们看一个例子。

# 现场演示

AppRun 是一个现代的 JavaScript 库，用于构建快速上市、高性能和可靠的 web 应用。
AppRun 采用了 Elm 架构的原则和事件发布订阅模式。它学习曲线低，开发生产率高。

控制台中的 AppRun CLI 是 AppRun 库中包含的开发工具之一。您可以访问 AppRun RealWorld 示例应用程序[https://gothinkster.github.io/apprun-realworld-example-app](https://gothinkster.github.io/apprun-realworld-example-app)来查看 CLI 的运行情况。

*   *components* 命令记录具有 AppRun 组件的 DOM 元素
*   *事件*命令记录应用的事件订阅
*   *log* 命令记录应用程序的运行时事件发布
*   *create-event-tests* 命令为应用程序创建单元测试
*   *create-state-tests* 命令为应用程序创建 Jest 快照测试

# 结论

开发者喜欢 CLI。控制台中的 CLI 有助于获取运行时事件和消息，这是传统的终端 CLI 难以做到的。控制台中的 AppRun CLI 甚至将 CLI 扩展到了观察数据之外，以生成测试。它提高了调试和测试的开发效率。

希望你喜欢这个主意。祝你编码愉快。