# 从回调到 fs/promises 处理 Node.js 中的文件系统

> 原文：<https://dev.to/mrm8488/from-callbacks-to-fspromises-to-handle-the-file-system-in-nodejs-56p2>

在这篇文章中，我将向您展示 Node.js 在使用文件系统方面的发展。
让我们从创建一个文件开始:

```
const fs = require("fs");

fs.writeFile("/tmp/test.js", "console.log('Hello world');", error => {
    if (error) console.error(error);
    else console.log("file created successfully!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想避免**回调**，在 *Node.js v8* 之前，我们必须手动预定义 **fs.writeFile** 函数，或者使用第三方模块，如**蓝鸟**或 **Q** 。

让我们手动地将上面的代码封装在一个函数中:

```
const fs = require("fs");

const writeFilePromise = (file, data) => {
    return new Promise((resolve, reject) => {
        fs.writeFile(file, data, error => {
            if (error) reject(error);
            resolve("file created successfully with handcrafted Promise!");
        });
    });
};

writeFilePromise(
        "/tmp/test2.js",
        "console.log('Hello world with handcrafted promise!');"
    )
    .then(result => console.log(result))

    .catch(error => console.log(error)); 
```

Enter fullscreen mode Exit fullscreen mode

随着 *Node.js V8* 的到来， **'util.promisify()'** 都允许我们将返回回调的 I/O 函数转换成返回承诺的 I/O 函数。

```
const fs = require("fs");
const util = require("util");

const writeFile = util.promisify(fs.writeFile);

writeFile("/tmp/test3.js", "console.log('Hello world with promisify!');")
  .then(() => console.log("file created successfully with promisify!"))

  .catch(error => console.log(error)); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看**异步/等待**版本:

```
const fs = require("fs");
const { promisify } = require("util");

const writeFile = promisify(fs.writeFile);

async function main() {
    await writeFile("/tmp/test4.js",
        "console.log('Hello world with promisify and async/await!');");

    console.info("file created successfully with promisify and async/await!");
}

main().catch(error => console.error(error)); 
```

Enter fullscreen mode Exit fullscreen mode

在其最新版本( *Node.js V10* )中， **fs** 的函数可以直接返回承诺，省去了老方法的额外步骤和开销。这要归功于它的 **fs/promises** API。

```
const fsp = require("fs/promises");

try {
    await fsp.writeFile("/tmp/test5.js", "console.log('Hello world with Node.js v10 fs/promises!'");
    console.info("File created successfully with Node.js v10 fs/promises!");
} catch (error){
    console.error(error);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我使用的是**顶级 await** (我的 *await* 代码不在里面，还有 *async* 函数)。这个特性还处于试验阶段，所以如果您想测试一下，可以使用下面的标志: *- experimental-repl-await* 。

**fs/promises** API 提供了以下方法:

> access、copyFile、open、read、write、rename、truncate、ftruncate、rmdir、fdatasync、fsync、mkdir、readdir、readlink、symlink、fstat、lstat、stat、link、unlink、fchmod、chmod、lchmod、lchown、fchown、chown、utimes、futimes、realpath、mkdtemp、writeFile、appendFile 和 readFile。

更新:

在*节点 13* 中，它甚至可以与 *ESM 模块* :
一起工作

```
import { promises as fs } from 'fs';

try {
    await fs.writeFile("/tmp/test6.js", "console.log('Hello world with Node.js v13 fs.promises!'");
    console.info("File created successfully with Node.js v13 fs.promises!");
} catch (error){
    console.error(error);
} 
```

Enter fullscreen mode Exit fullscreen mode