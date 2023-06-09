# 使用节点读取非常非常大的数据集(第 1 部分)

> 原文：<https://dev.to/paigen11/using-node-to-read-really-really-large-datasets-pt-1-10gf>

[![Node.js logo](img/0863bfb02788931937ad1bab18d8fff6.png "Node.js logo")T2】](///static/de70150a98f9af865dc1b3eaf3e734ad/2cefc/node-logo.png)

## 简介

这篇博文有一个有趣的灵感点。上周，有人在我的一个 Slack 频道上发布了一个他收到的关于一家保险技术公司开发人员职位的编码挑战。

它激起了我的兴趣，因为挑战包括阅读来自联邦选举委员会的非常大的数据文件，并显示这些文件中的特定数据。由于我没有太多处理原始数据的经验，并且我总是准备迎接新的挑战，我决定用 Node.js 来解决这个问题，看看我是否能自己完成这个挑战，这只是为了好玩。

这里有 4 个问题，以及一个程序要解析的数据集的链接。

*   写一个程序，打印出文件中的总行数。
*   请注意，第 8 列包含一个人的姓名。编写一个程序，加载这些数据并创建一个包含所有名称字符串的数组。打印出第 432 号和第 43243 号姓名。
*   请注意，第 5 列包含一种日期形式。计算每个月有多少捐赠，并打印出结果。
*   请注意，第 8 列包含一个人的姓名。用每个名字创建一个数组。确定数据中最常见的名字及其出现的次数。

链接到数据:[https://www.fec.gov/files/bulk-downloads/2018/indiv18.zip](https://www.fec.gov/files/bulk-downloads/2018/indiv18.zip)

当您解压缩该文件夹时，您应该会看到一个 2.55GB 的主`.txt`文件和一个包含该主文件较小部分的文件夹(这是我在转移到主文件之前测试我的解决方案时使用的)。

不算太可怕吧？似乎可行。

今天我们来谈谈我是如何使用 Node.js 来解析这个非常非常大的文件的，以及我在这个过程中想到的各种解决方案。

* * *

## 我最初想到的两个 Node.js 解决方案

处理大文件对 JavaScript 来说并不是什么新鲜事，事实上，在 Node.js 的核心功能中，有许多读写文件的标准解决方案。

最直接的是 **[`fs.readFile()`](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback)** ，其中，整个文件被读入内存，然后一旦节点读取了它就对其进行操作，第二个选项是 **[`fs.createReadStream()`](https://nodejs.org/api/fs.html#fs_fs_createreadstream_path_options)** ，它类似于 Python 和 Java 等其他语言，使数据流入(和流出)。

### 我选择和&一起跑的解决方案为什么

由于我的解决方案需要包括计算总行数和解析每一行以获得捐赠名称和日期，所以我选择使用第二种方法:fs.createReadStream()。然后，我可以使用 rl.on('line '，...)函数在我浏览文档时从每行代码中获取必要的数据。

对我来说，这似乎更容易，因为一旦文件被读入，就必须将整个文件分割开来，并以这种方式逐行运行。

### Node.js `createReadStream()` & `readFile()`代码实现

下面是我用 Node.js 的 fs.createReadStream()函数想出来的代码。下面我来分解一下。

**[`readFileStream.js`](https://github.com/paigen11/file-read-challenge/blob/master/readFileStream.js)**T5】

```
var fs = require('fs');
var readline = require('readline');
var stream = require('stream');

var instream = fs.createReadStream('itcont.txt');
var outstream = new stream();
var rl = readline.createInterface(instream, outstream);

//get line count for file
var lineCount = 0;

// create array list of names
var names = [];

// donations occurring in each month
var dateDonationCount = [];
var dateDonations = {};

// list of first names, and most common first name
var firstNames = [];
var dupeNames = {};

rl.on('line', function(line) {
  // increment line count
  lineCount++;

  // get all names
  var name = line.split('|')[7];
  names.push(name);

  // get all first halves of names
  var firstHalfOfName = name.split(', ')[1];
  if (firstHalfOfName !== undefined) {
    firstHalfOfName.trim();
    // filter out middle initials
    if (firstHalfOfName.includes('  ') && firstHalfOfName !== '  ') {
      firstName = firstHalfOfName.split('  ')[0];
      firstName.trim();
      firstNames.push(firstName);
      dupeNames[firstName] = (dupeNames[firstName] || 0) + 1;
    } else {
      firstNames.push(firstHalfOfName);
      dupeNames[firstHalfOfName] = (dupeNames[firstHalfOfName] || 0) + 1;
    }
  }

  // year and month
  var timestamp = line.split('|')[4].slice(0, 6);
  var formattedTimestamp = timestamp.slice(0, 4) + '-' + timestamp.slice(4, 6);
  dateDonationCount.push(formattedTimestamp);
  dateDonations[formattedTimestamp] =
    (dateDonations[formattedTimestamp] || 0) + 1;
});

rl.on('close', function() {
  // total line count
  console.log(lineCount);

  // names at various points in time
  console.log(names[432]);
  console.log(names[43243]);

  // most common first name
  var sortedDupeNames = Object.entries(dupeNames);

  sortedDupeNames.sort((a, b) => {
    return b[1] - a[1];
  });
  console.log(sortedDupeNames[0]);

  const name = sortedDupeNames[0][0];
  const nameOccurrences = sortedDupeNames[0][1];
  console.log(
    `The most common name is '${name}' with ${nameOccurrences} occurrences.`,
  );

  // number of donations per month
  const logDateElements = (key, value, map) => {
    console.log(
      `Donations per month and year: ${value} and donation count ${key}`,
    );
  };
  new Map(Object.entries(dateDonations)).forEach(logDateElements);
}); 
```

Enter fullscreen mode Exit fullscreen mode

我要做的第一件事就是从 Node.js 导入所需的函数:`fs`(文件系统)、`readline`和`stream`。这些导入允许我创建一个`instream`和`outstream`，然后是`readLine.createInterface()`，这将让我逐行读取流并从中打印出数据。

我还添加了一些变量(和注释)来保存不同的数据位:一个`lineCount`、`names`数组、`donation`数组和对象，以及`firstNames`数组和`dupeNames`对象。稍后您将看到这些在哪里发挥作用。

在`rl.on('line',...)`函数内部，我能够进行所有的逐行数据解析。在这里，我为流过的每一行增加了`lineCount`变量。我使用 JavaScript `split()`方法解析出每个名字，并将其添加到我的`names`数组中。我进一步将每个名字简化为名字，同时考虑中间的首字母、多个名字等。在 JavaScript 的`trim()`、`includes()`和`split()`方法的帮助下显示名字。我将 year 和 date out of date 列切片，重新格式化为可读性更好的`YYYY-MM`格式，并将它们添加到`dateDonationCount`数组中。

在`rl.on('close',...)`函数中，我对收集到的数据进行了所有的转换，然后`console.log`将我的所有数据输出给用户查看。

第 432 和 43，243 个索引处的`lineCount`和`names`无需进一步操作。找到最常见的名字和每个月的捐款数量有点难。

对于最常见的名字，我首先必须为每个名字(键)和它出现的次数(值)创建一个键值对对象，然后我使用 ES6 函数`Object.entries()`将它转换成一个数组的数组。从那里开始，按照名称的值对名称进行排序并打印出最大值是一项简单的任务。

捐款还要求我制作一个类似的键值对对象，创建一个`logDateElements()`函数，我可以很好地使用 ES6 的字符串插值来显示每个捐款月的键和值。然后创建一个`new Map()`，将`dateDonations`对象转换成一个数组的数组，并通过调用每个数组上的`logDateElements()`函数来循环访问每个数组。咻！不像我最初想的那么简单。

但是成功了。至少对于我用于测试的较小的 400MB 文件…

在我用`fs.createReadStream()`完成之后，我又用`fs.readFile()`实现了我的解决方案，看看有什么不同。这是相关的代码，但我不会在这里详述所有细节——它与第一个代码片段非常相似，只是看起来更同步(不过，除非你使用`fs.readFileSync()`函数，否则 JavaScript 会像所有其他代码一样异步运行这段代码，不用担心。

**[`readFile.js`](https://github.com/paigen11/file-read-challenge/blob/master/readFile.js)**T5】

```
var fs = require('fs');

var totalLines = 0;
var lines = [];
var names = [];
var firstNames = [];
var dupeNames = {};
var dateDonationCount = [];
var dateDonations = {};

fs.readFile('itcont.txt', 'utf8', (err, contents) => {
  if (contents !== undefined) {
    totalLines = contents.split('\n').length - 1;
  }
  console.log(totalLines);

  if (contents !== undefined) {
    lines = contents.split('\n');
    lines.forEach(line => {
      var name = line.split('|')[7];
      names.push(name);
    });
  }

  console.log(names[432]);
  console.log(names[43243]);

  names.forEach(name => {
    var firstHalfOfName = name.split(', ')[1];
    if (firstHalfOfName !== undefined) {
      firstHalfOfName.trim();

      if (firstHalfOfName !== '  ' && firstHalfOfName.includes('  ')) {
        firstName = firstHalfOfName.split('  ')[0];
        firstName.trim();
        firstNames.push(firstName);
      } else {
        firstNames.push(firstHalfOfName);
      }
    }
  });

  firstNames.forEach(x => {
    dupeNames[x] = (dupeNames[x] || 0) + 1;
  });
  var sortedDupeNames = [];
  sortedDupeNames = Object.entries(dupeNames);

  sortedDupeNames.sort((a, b) => {
    return b[1] - a[1];
  });
  console.log(sortedDupeNames[0]);

  lines.forEach(line => {
    var timestamp = line.split('|')[4].slice(0, 6);
    var formattedTimestamp =
      timestamp.slice(0, 4) + '-' + timestamp.slice(4, 6);
    dateDonationCount.push(formattedTimestamp);
  });

  dateDonationCount.forEach(x => {
    dateDonations[x] = (dateDonations[x] || 0) + 1;
  });

  const logDateElements = (key, value, map) => {
    console.log(
      `Donations per month and year: ${value} and donation count ${key}`,
    );
  };
  new Map(Object.entries(dateDonations)).forEach(logDateElements);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看我的完整回购和我所有的代码，你可以在这里 看到它 **[。](https://github.com/paigen11/file-read-challenge)**

### 初步结果:第 1 轮

使用我的工作解决方案，我将文件路径添加到 2.55GB 的 monster 文件的`readFileStream.js`文件中，并看到我的节点服务器崩溃，出现 JavaScript 堆内存不足错误。

[![Console output when Node program ran out of memory and crashed](img/12c70fc7f64e26eb67165d11dfdec52e.png "Console output when Node program ran out of memory and crashed")T2】](///static/62aa3517869c8529073fb9b25a458532/2cefc/node-out-of-memory.png)

*失败。撞击声，撞击声...*

事实证明，尽管 Node.js 正在流式传输文件输入和输出，但在这两者之间，它仍然试图在内存中保存整个文件内容，对于这样大小的文件，它做不到这一点。节点一次最多可以容纳 1.5GB 的内存，但不能再多了。

因此，我目前的解决方案都不能完全应对挑战。

我需要一个新的解决方案。通过节点运行的更大数据集的解决方案。

## 新的数据流解决方案

我以 **[EventStream](https://www.npmjs.com/package/event-stream)** 的形式找到了我的解决方案，这是一个受欢迎的 NPM 模块，每周下载超过 200 万次，并承诺“让创建和使用流变得容易”。

在 [EventStream 的文档](https://github.com/dominictarr/event-stream)的一点帮助下，我能够再次弄清楚如何逐行读取代码并做需要做的事情，希望是以一种对 Node 更友好的方式。

### EventStream 代码实现

这是我的代码使用 NPM 模块 EventStream 的新代码。

**[`readFileEventStream.js`](https://github.com/paigen11/file-read-challenge/blob/master/readFileEventStream.js)**T5】

```
var fs = require('fs');
var es = require('event-stream');

var totalLines = 0;
var names = [];
var firstNames = [];
var dupeNames = {};
var dateDonationCount = [];
var dateDonations = {};

var s = fs
  .createReadStream('itcont.txt')
  .pipe(es.split())
  .pipe(
    es
      .mapSync(function(line) {
        totalLines++;

        // get all names
        var name = line.split('|')[7];
        if (totalLines === 433 || totalLines === 43244) {
          names.push(name);
        }

        // get all first halves of names
        var firstHalfOfName = name.split(', ')[1];

        if (firstHalfOfName !== undefined) {
          firstHalfOfName.trim();

          // filter out middle initials
          if (firstHalfOfName.includes('  ') && firstHalfOfName !== '  ') {
            firstName = firstHalfOfName.split('  ')[0];
            firstName.trim();
            firstNames.push(firstName);
            dupeNames[firstName] = (dupeNames[firstName] || 0) + 1;
          } else {
            firstNames.push(firstHalfOfName);
            dupeNames[firstHalfOfName] = (dupeNames[firstHalfOfName] || 0) + 1;
          }
        }

        // year and month
        var timestamp = line.split('|')[4].slice(0, 6);
        var formattedTimestamp =
          timestamp.slice(0, 4) + '-' + timestamp.slice(4, 6);
        dateDonationCount.push(formattedTimestamp);
        dateDonations[formattedTimestamp] =
          (dateDonations[formattedTimestamp] || 0) + 1;
      })
      .on('error', function(err) {
        console.log('Error while reading file.', err);
      })
      .on('end', function() {
        console.log('Read entire file.');
        console.log(totalLines);

        sortedDupeNames = Object.entries(dupeNames);

        sortedDupeNames.sort((a, b) => {
          return b[1] - a[1];
        });
        console.log(sortedDupeNames[0]);

        const name = sortedDupeNames[0][0];
        const nameOccurrences = sortedDupeNames[0][1];
        console.log(
          `The most common name is '${name}' with ${nameOccurrences} occurrences.`,
        );

        // number of donations per month
        const logDateElements = (key, value, map) => {
          console.log(
            `Donations per month and year: ${value} and donation count ${key}`,
          );
        };
        new Map(Object.entries(dateDonations)).forEach(logDateElements);
      }),
  ); 
```

Enter fullscreen mode Exit fullscreen mode

最大的变化是文件开头的管道命令——所有这些语法都是 EventStream 的文档建议您将流分成由`.txt`文件每行末尾的`\n`字符分隔的块的方式。

我必须改变的另一件事是答案。我不得不稍加修改，因为如果我试图将所有 13 毫米的名字添加到一个数组中，我又会遇到内存不足的问题。我通过收集第 432 和第 43，243 个名字并将它们添加到它们自己的数组中来解决这个问题。不完全是被要求的，但是嘿-我得有点创意。

### 结果来自 Node.js & EventStream:第二轮

好了，随着新解决方案的实现，我再次用我的 2.55GB 文件启动 Node.js，我的手指交叉这将工作。看看结果。

[![Console output when Node Event Stream program successfully chewed through 2.55GB of file data](img/a9bf26562cf2f01dff3608fc39586e60.png "Console output when Node Event Stream program successfully chewed through 2.55GB of file data")T2】](///static/e80665c386ede41e173fd684b731999e/f1901/node-event-stream-results.png)

*吼吼！*

成功！

* * *

## 结论

最终，Node.js 的纯文件和大数据处理功能没有达到我的要求，但只需要一个额外的 NPM 包 EventStream，我就能够解析一个大规模数据集，而不会使节点服务器崩溃。

请继续关注本系列的第 2 部分，在那里我将 Node.js 中读取数据的三种不同方式与性能测试进行了比较，以了解哪种方式真正优于其他方式。结果令人大开眼界，尤其是当数据变大时…

过几周再来看看——我会写更多关于 JavaScript、React、IoT 或其他与 web 开发相关的东西。

如果你想确保你不会错过我写的一篇文章，在这里注册我的时事通讯:[https://paigeniedringhaus.substack.com](https://paigeniedringhaus.substack.com)

感谢您的阅读，我希望这能让您了解如何使用 Node.js 处理大量数据。

* * *

## 参考文献&进一步资源

*   Node.js 文档，[文件系统](https://nodejs.org/api/fs.html)
*   Node.js 文档， [Readline](https://nodejs.org/api/readline.html#readline_event_line)
*   Github，[读取文件报告](https://github.com/paigen11/file-read-challenge)
*   NPM， [EventSream](https://www.npmjs.com/package/event-stream)