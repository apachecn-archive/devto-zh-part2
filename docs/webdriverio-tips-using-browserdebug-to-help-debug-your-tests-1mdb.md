# webdriverIO 提示:使用 browser.debug()帮助调试测试

> 原文：<https://dev.to/intricatecloud/webdriverio-tips-using-browserdebug-to-help-debug-your-tests-1mdb>

有没有想过中途停止你的硒测试，并尝试看看你的测试是什么？使用。debug()有所帮助，但是要注意测试超时和代码的上下文。

编辑 2019 年 10 月-我在我的 Youtube 频道上发布了一个新视频，在那里我调试了一个测试，并向你展示了 browser.debug()如何与 VS 代码调试器一起使用。看看吧！

[https://www.youtube.com/embed/wvvIz60DNp4](https://www.youtube.com/embed/wvvIz60DNp4)

### 超时

让我们来看一个简单的用例。去一个网站，把你放进网络驱动器里里奥·REPL:

```
describe('a test', function() {
  it('runs', function() {
    browser.url('https://msn.com');
    browser.debug();
    expect(true).toBeTruthy();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

运行这个测试正是我想要的。它让我进入 REPL 的网络驱动器，这样我就可以开始与网页互动了。我还在测试中，期望还没有运行:

```
[16:58:20]  DEBUG   Queue has stopped!
[16:58:20]  DEBUG   You can now go into the browser or use the command line as REPL
[16:58:20]  DEBUG   (To exit, press ^C again or type .exit)

> 
```

Enter fullscreen mode Exit fullscreen mode

第一次运行时，您会失望地看到一个由于超时而失败的测试。类似于

```
> F

0 passing (15.80s)
1 failing

1) a testsuite1 runs:
Error: Timeout - Async callback was not invoked within 10000ms (set by jasmine.DEFAULT_TIMEOUT_INTERVAL)
running firefox
Error: Timeout - Async callback was not invoked within 10000ms (set by jasmine.DEFAULT_TIMEOUT_INTERVAL)
    at <Jasmine>
    at ontimeout (timers.js:498:11)
    at tryOnTimeout (timers.js:323:5)
    at Timer.listOnTimeout (timers.js:290:5) 
```

Enter fullscreen mode Exit fullscreen mode

刚刚发生了什么？根据 Jasmine 的定义，您的测试有一个 10 秒的默认超时。因为我们正在测试中，Jasmine 仍然在跟踪测试的执行时间，并且会杀死你的测试，因为它看起来像是挂起了。通常，这是您想要的，因为如果您在等待页面上出现选择器，而您在错误的页面上，您希望测试在无法及时找到它时失败。在这种情况下，我想调试我的测试，这很烦人，因为每当我想使用`browser.debug()`时，我都需要改变它。

您可以在您的`wdio.conf.js`文件中通过改变`jasmineNodeOpts`
来改变这一点

```
// Options to be passed to Jasmine.
jasmineNodeOpts: {
    //
    // Change this to something really large...
    // but not too large
    defaultTimeoutInterval: 10000, 
```

Enter fullscreen mode Exit fullscreen mode

### 在 REPL 失去语境

当你坠入 REPL，你会失去一点点的背景。例如，有这样一个测试:

```
describe('a test', function() {
  it('runs', function() {
    browser.url('https://msn.com');
    var foo = "Bar";
    browser.debug();

    expect(true).toBeTruthy();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦你在 REPL，你就看不到`foo`的价值了。

```
[17:38:54]  DEBUG   Queue has stopped!
[17:38:54]  DEBUG   You can now go into the browser or use the command line as REPL
[17:38:54]  DEBUG   (To exit, press ^C again or type .exit)

> foo
evalmachine.<anonymous>:1
foo
^

ReferenceError: foo is not defined
    at evalmachine.<anonymous>:1:1
    at ContextifyScript.Script.runInThisContext (vm.js:50:33) 
```

Enter fullscreen mode Exit fullscreen mode

因此，REPL 对于在测试过程中与浏览器对象进行交互是有用的，但是对于检查当前的上下文和变量的值是没有用的。为此，我们可能需要依赖本机节点调试器，或者像往常一样...不过，您可以将它用作典型的节点 REPL，并在控制台上设置变量和打印内容。

### 重述

当使用 REPL 的网络驱动时，你需要记住两件事

*   您的测试框架(在本例中是 Jasmine)有一个全局默认超时，这将阻止您有效地使用 REPL，所以当您尝试调试时，记得将它更改为一个大的数字
*   您将失去上下文，因此您将看不到现有变量的值。

* * *

上周，我开始整合之前使用 Nightwatch 构建的测试套件，并让它与 webdriverIO 一起工作。虽然我喜欢 webdriverIO 的所有功能，如使用测试运行器和 REPL 时的同步代码，但我想分享一些在文档或快速搜索中很难找到的东西。

以防你错过了...这个星期的每一天，我都在张贴我在建立 webdriverIO 时学到的一件事。点击这里查看我以前的帖子:

*   [用$$。(选择器)vs browser.elements(选择器)](https://www.intricatecloud.io/2018/11/webdriverio-tips-using-selector-vs-browser-elementsselector/)