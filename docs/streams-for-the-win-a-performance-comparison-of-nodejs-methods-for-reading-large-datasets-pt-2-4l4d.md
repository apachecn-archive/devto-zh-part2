# 成功的流:读取大型数据集的 Node.js 方法的性能比较(第 2 部分)

> 原文：<https://dev.to/paigen11/streams-for-the-win-a-performance-comparison-of-nodejs-methods-for-reading-large-datasets-pt-2-4l4d>

[![Node.js logo](../Images/2c0a5bc91483ab636d3f08cef5769f51.png "Node.js logo")T2】](///static/fbf24df0c3e7745cbb5a4b1bf7445cfc/a2510/node-2.jpg)

## 简介

如果你一直关注我的写作，几周前，我发表了一篇 **[博客](https://www.paigeniedringhaus.com/using-node-to-read-really-really-large-datasets-pt-1)** 谈论使用 Node.js 读取非常大的数据集的各种方法。

令我惊讶的是，它在读者中表现得非常好——这似乎(对我来说)像是一个许多人已经在帖子、博客和论坛中涉及的主题，但不管出于什么原因，它得到了许多人的关注。所以，感谢所有花时间阅读它的人！我真的很感激。

一位特别敏锐的读者:马丁·科克甚至问解析这些文件需要多长时间。他似乎已经读懂了我的心思，因为我关于使用 Node.js 读取非常非常大的文件和数据集的系列文章的第二部分就涉及到了这一点。

在这篇文章中，我将评估我用来读取文件的 Node.js 中的三种不同方法，以确定哪一种是性能最好的。

* * *

## 来自 part 1 的挑战

我不会深入探讨挑战和解决方案的细节，因为你可以阅读我的第一篇文章了解所有细节，但我会给你一个高层次的概述。

我所在的 Slack 频道的一个人发布了他收到的一个编码挑战，其中涉及读取一个非常大的数据集(总共超过 2.5GB)，解析数据并提取各种信息。

它要求程序员打印出:

*   文件中的总行数。
*   第 432 和 43243 号索引中的名称。
*   计算每月捐赠总数。
*   和文件中最常见的名字，以及如何计算该名字出现的频率。

如果你想看的话，这里有一个数据链接:[https://www.fec.gov/files/bulk-downloads/2018/indiv18.zip](https://www.fec.gov/files/bulk-downloads/2018/indiv18.zip)

### 针对较小数据集的三种不同解决方案

当我朝着处理大型数据集的最终目标努力时，我在 Node.js 中提出了三个解决方案。

**方案一:`fs.readFile()`**

第一个涉及 Node.js 的本地方法 **[`fs.readFile()`](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback)** ，包括读取整个文件，将其保存在内存中，并对整个文件执行操作，然后返回结果。至少对于较小的文件是有效的，但是当我达到最大文件大小时，我的服务器崩溃了，出现了一个 JavaScript `heap out of memory error`。

**方案二:`fs.createReadStream()` & `rl.readLine()`**

我的第二个解决方案还涉及 Node.js 自带的另外两个方法: **[`fs.createReadStream()`](https://nodejs.org/api/fs.html#fs_fs_createreadstream_path_options)** 和 **[`rl.readLine()`](https://nodejs.org/api/readline.html#readline_event_line)** 。在这次迭代中，文件通过 Node.js 以一个`input`流的形式传输，我能够在每一行上执行单独的操作，然后在输出流中将所有这些结果拼凑在一起。

同样，这在较小的文件上工作得很好，但是当我到达最大的文件时，同样的错误发生了。尽管 Node.js 对输入和输出进行了流式处理，但在执行操作时，它仍然试图将整个文件保存在内存中(并且无法处理整个文件)。

**方案三:`event-stream`**

最终，我在 Node.js 中找到了唯一一个解决方案，它能够处理我想要一次解析的 2.55GB 文件。

> **有趣的事实:** Node.js 在任一时刻最多只能容纳 1.67GB 的内存，之后它会抛出一个 JavaScript `heap out of memory error`。

我的解决方案涉及一个流行的 NPM 包，名为，它实际上让我对数据的*吞吐量流*执行操作，而不仅仅是输入和输出流，因为 Node.js 的本机功能允许。

你可以在 GitHub 的 这里看到我所有的三个解决方案 **[。](https://github.com/paigen11/file-read-challenge)**

我解决了这个问题，这是我最初的目标，但它让我思考:我的解决方案真的是三个选项中最有效的吗？

## 比较方案，找出最优的一个

现在我有了一个新的目标:确定我的解决方案中哪一个是最好的。

由于我无法在 Node.js 本地解决方案中使用完整的 2.55GB 文件，所以我选择使用一个较小的文件，大约有 400MB 的数据，这是我在开发解决方案时用于测试的。

对于性能测试 Node.js，我遇到了两种跟踪文件和单个函数处理时间的方法，我决定将这两种方法结合起来，看看这两种方法之间的差异有多大(并确保我没有完全偏离我的计时)。

**`console.time()`&`console.timeEnd()`T3】**

Node.js 有一些方便的内置方法可用于计时和性能测试，分别称为 **[`console.time()`](https://nodejs.org/api/console.html#console_console_time_label)** 和 **[`console.timeEnd()`](https://nodejs.org/api/console.html#console_console_timeend_label)** 。要使用这些方法，我只需为`time()`和`timeEnd()`传递相同的标签参数，如下面的代码，Node 足够聪明，可以在函数完成后输出它们之间的时间。

```
// timer start
console.time('label1');

// run function doing something in the code
doSomething();

// timer end, where the difference between the timer start and timer end is printed out
console.timeEnd('label1');

// output in console looks like: label1 0.002ms 
```

Enter fullscreen mode Exit fullscreen mode

这是我用来计算处理数据集所需时间的一种方法。

**T2`performance-now`**

另一个是我在 Node.js 上遇到的经过验证且广受欢迎的性能测试模块，它在 NPM 上被命名为 [performance-now](https://www.npmjs.com/package/performance-now) 。

每周从 NPM 下载 700 多万次，不会太离谱吧？？

将`performance-now`模块实现到我的文件中也几乎和原生 Node.js 方法一样简单。导入模块，为方法实例化的开始和结束设置一个变量，计算两者之间的时间差。

```
// import the performance-now module at the top of the file
const now = require('performance-now');

// set the start of the timer as a variable
const start = now();

// run function doing something in the code
doSomething();

// set the end of the timer as a variable
const end = now();

// Compute the duration between the start and end
console.log('Performance for timing for label:' + (end — start).toFixed(3) + 'ms';

// console output looks like: Performance for timing label: 0.002ms 
```

Enter fullscreen mode Exit fullscreen mode

我认为通过同时使用节点的`console.time()`和`performance-now`,我可以分开这种差异，得到一个关于我的文件解析函数实际花费了多长时间的非常准确的读数。

下面是在我的每个脚本中实现`console.time()`和`performance-now`的代码片段。这些只是每个函数的片段——完整的代码，你可以在这里看到我的回购。

### Fs.readFile()代码实现示例

```
console.time('line count');
let t0 = now();

if(contents === undefined){
  totalLines = contents.split('\n').length - 1;
}
console.log(totalLines);

let t1 = now();
console.timeEnd('line count');

console.log(`Performance now line count timing: ` + (t1 - t0).toFixed(3)); 
```

Enter fullscreen mode Exit fullscreen mode

因为这个脚本使用的是`fs.readFile()`实现，在执行任何函数之前，整个文件都被读入内存，所以这是看起来最同步的代码。

> 它实际上不是同步的，那是一个完全独立的叫做`fs.readFileSync()`的节点方法——这个方法就像它一样。

但是很容易看到文件的总行数和两个计时方法来确定执行行数需要多长时间。

### Fs.createReadStream()代码实现示例

**输入流(逐行):**

```
console.time('line count');

let t0 = now(); 
```

Enter fullscreen mode Exit fullscreen mode

**输出流(一旦在输入期间读取了完整的文件):**

```
let t1 = now();

console.log(lineCount);
console.timeEnd('line count');
console.log(`Performance now line count timing: ` + (t1 - t0).toFixed(3)); 
```

Enter fullscreen mode Exit fullscreen mode

由于使用`fs.createReadStream()`的第二个解决方案涉及到为文件创建输入和输出流，我将代码片段分成两个单独的截图，第一个来自输入流(逐行运行代码)，第二个是输出流(编译所有结果数据)。

### 事件流代码实现示例

**吞吐流(也是逐行):**

```
console.time('line count');

let t0 = now();

totalLines++; 
```

Enter fullscreen mode Exit fullscreen mode

**在流端:**

```
let t1 = now();

console.log(totalLines);
console.timeEnd('line count');
console.log(`Performance now line count timing: ` + (t1 - t0).toFixed(3)); 
```

Enter fullscreen mode Exit fullscreen mode

`event-stream`解决方案看起来与`fs.createReadStream()`非常相似，除了代替*输入流*，数据在*吞吐流*中处理。然后，一旦整个文件被读取，并且文件上的所有功能都已完成，流就结束了，所需的信息被打印出来。

### 结果

现在到了我们期待已久的时刻:结果！

我在同一个 400MB 的数据集上运行了我的三个解决方案，该数据集包含了近 200 万条要解析的记录。

[![Solution printouts](../Images/338841a18f9334ba36b3bb0738dc0e98.png "Solution printouts")T2】](///static/1e7b95b57c7d7b1d0570f4b3ec68d6aa/2cefc/stream-ftw.png)

*为胜利而奔流！*

正如你从表中看到的，`fs.createReadStream()`和`event-stream`都表现不错，但总的来说，`event-stream`在我看来是最大的赢家，因为它可以处理比`fs.readFile()`或`fs.createReadStream()`大得多的文件。

上表末尾也列出了改进的百分比，以供参考。

刚刚被竞争对手打得落花流水。通过流式传输数据，文件的处理时间缩短了至少 78% —有时接近 100%，这是相当惊人的。

以下是我的每个解决方案在终端上的原始截图。

**方案一: [`fs.readFile()`](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback)**

[![Solution 1 print out](../Images/5ba025cdd4e9766301793829eab08cd4.png "Solution 1 print out")T2】](///static/95e87af5abe7bba3ee4534707d4c0e94/ecf19/solution-1.png)

*仅使用 fs.readFile()的解决方案*

**方案二: [`fs.createReadStream()`](https://nodejs.org/api/fs.html#fs_fs_createreadstream_path_options) & [`rl.readLine()`](https://nodejs.org/api/readline.html#readline_event_line)**

[![Solution 2 print out](../Images/6209a00725b70a3383d468d50bef1894.png "Solution 2 print out")T2】](///static/158797187d264bbb507d1f59c341af3f/cc488/solution-2.png)

*使用 fs.createReadStream()和 rl.readLine()的解决方案*

**方案三: [`event-stream`](https://www.npmjs.com/package/event-stream)**

[![Solution 3 print out](../Images/248fad4b96694fa4d9d2717635c56785.png "Solution 3 print out")T2】](///static/4fd982dfa1e0f4f3aef5ef1056de85fa/5bf79/solution-3.png)

*使用事件流的解决方案*

**奖金**

这是我的`event-stream`解决方案在 2.55GB 的怪物文件中翻腾的截图。这是 400MB 文件和 2.55GB 文件之间的时差。

[![Bonus solution print out](../Images/ea124937b80562c4574af706d753b852.png "Bonus solution print out")T2】](///static/86820fcd9c4845a3eca3487675044881/7960f/bonus-results.png)

看看这些超快的速度，尽管文件大小增加了近 6 倍。

**解决方案#3: `event-stream`(在 2.55GB 文件上)**

[![Bonus solution print out](../Images/124a50135f4943daf9543bc61c6f85f5.png "Bonus solution print out")T2】](///static/b6ca69d60d84afe359e4da9aa9ea6167/966a0/solution-3.2.png)

看看这些超快的速度，尽管文件大小增加了近 6 倍。

* * *

## 结论

最后，无论是 Node.js 本地的流还是非本地的流，在处理大型数据集时都要高效得多。

感谢回到我的使用 Node.js 读取非常非常大的文件系列的第 2 部分。如果你想再次阅读第一篇博客，你可以在这里 访问它 **[。](https://www.paigeniedringhaus.com/using-node-to-read-really-really-large-datasets-pt-1)**

几周后，我将带着新的 JavaScript 主题回来——可能是在节点中调试，或者是使用 Puppeteer 和 headless Chrome 进行端到端测试。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

感谢您的阅读，我希望这能让您了解如何使用 Node.js 高效地处理大量数据，并对您的解决方案进行性能测试。

* * *

## 参考文献&进一步资源

*   [Github，读取文件回购](https://github.com/paigen11/file-read-challenge)
*   Node.js 文档，[文件系统](https://nodejs.org/api/fs.html)
*   Node.js 文档，[控制台。时间](https://nodejs.org/api/console.html#console_console_time_label)
*   NPM，[现在的表现](https://www.npmjs.com/package/performance-now)
*   NPM， [EventSream](https://www.npmjs.com/package/event-stream)
*   链接到 [FEC 数据](https://www.fec.gov/files/bulk-downloads/2018/indiv18.zip)