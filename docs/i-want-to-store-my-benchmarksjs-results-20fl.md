# 我想存储我的 benchmarks.js 结果

> 原文：<https://dev.to/lgraziani2712/i-want-to-store-my-benchmarksjs-results-20fl>

> 特蕾莎·阿尔芭的封面图片

在过去的一周里，我一直在搜索一个为 benchmark.js 的结果生成 JSON 的库。既然什么都没找到，就自己写了一个。

默认情况下，它将所有内容存储在一个文件中。但是它有一个回调函数，所以你可以做任何你想做的事情，比如把它存储在数据库中。

它还使用 [systeminformation](https://www.npmjs.com/package/systeminformation) 来存储关于运行基准测试的机器的静态数据。

我认为这个库很酷，因为它可以在 CI 管道中运行您的基准，并将结果存储在数据库中以供将来分析。

## 那么，今天怎么开始用呢？

首先，您需要安装依赖项:

`yarn add benchmark benchmark-json-reporter`

或者

`npm install --save benchmark benchmark-json-reporter`

最后，您必须创建一个文件来运行您的基准:

```
const Benchmark = require('benchmark');
const jsonReporter = require('benchmark-json-reporter');

const suite = new Benchmark.Suite('my-bench-suite');

// Just this
jsonReporter(suite);

suite
  .add('bench-name-1', () => {
    // Faster heavy process
  })
  // ...
  .add('bench-name-n', () => {
    // Slower heavy process
  })
  // run async
  .run({ async: true }); 
```

这个基本示例将结果存储在下面的文件中:`<rootFolder>/benchmarks/my-bench-suite-({md5-hash}.log)`。md5-hash 用于明确地标识一台机器。

顺便说一下，您也可以将您的基准存储在这样的数据库中:

```
const Benchmark = require('benchmark');
const jsonReporter = require('benchmark-json-reporter');

const suite = new Benchmark.Suite('my-bench-suite');

// Just this
jsonReporter(suite, {
  callback(result, hashId, name, folder) {
    // 1\. Connect to a database
    const connection = new SomeEndPoint();
    // 2\. Store the sysinfo with the hashId as a main ID
    connection
      .getById(hashId)
      .update({ sysinfo: result.sysinfo })
      .then(() => 
        // 3\. Store the benchmarks
        Promise.all(
          benchs.map(bench =>
            // For each benchmark, push the result into the collection
            connection
              .getById(hashId)
              .getProp('benchmarks')
              .getCollection(bench.timestamp).push(bench),
          )
        )
      ).then(() => {
        // 4\. Close the database connection
        connection.close();
      });
    // 5\. Profit.
  },
});

suite
  .add('bench-name-1', () => {
    // Faster heavy process
  })
  // ...
  .add('bench-name-n', () => {
    // Slower heavy process
  })
  // run async
  .run({ async: true }); 
```

感谢您的阅读！如果你有任何问题，尽管问！