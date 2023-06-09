# JavaScript:处理 Go 之类的错误

> 原文：<https://dev.to/oieduardorabelo/javascript-handling-errors-like-go-3efk>

早在八月份，我用巴西葡萄牙语写了一篇[文章，解释了我如何使用`async` / `await`来隔离错误处理。](https://medium.com/@oieduardorabelo/javascript-tratando-erros-ao-estilo-go-65c5da9c12a9)

今天我将把它翻译成英语，但是用不同的例子！

* * *

我喜欢 [Go](http://golang.org/) 以类似同步的方式处理副作用。让我们看看这个来自`net/http`包的例子:

```
func main() {
  res, err := http.Get("http://example.com/")

  if err != nil {
    // handle `err`
  }

  // do something with `res`
} 
```

Enter fullscreen mode Exit fullscreen mode

或者可能是`os`包:

```
func main() {
  file, err := os.Open("words.txt")

  if err != nil {
    // handle `err`
  }

  // do something with `file`
} 
```

Enter fullscreen mode Exit fullscreen mode

撇开实现细节不谈，我想知道是否有办法用 JavaScript 写出这样的东西？

嗯，就像他们说的，有志者事竟成！😂

# 日常承诺类功能

如今，类似承诺的环境在我们中间很常见。

我们可以用它来读取 Node.js 中的一个文件:

```
let util = require("util");
let fs = require("fs");

let read = util.promisify(fs.readFile);

function main() {
  read("./test.js", { encoding: "utf8" })
    .then(file => {
      // do something with `file`
    })
    .catch(err => {
      // handle `err`
    });
}

main(); 
```

Enter fullscreen mode Exit fullscreen mode

也许从 API 中获取一些数据:

```
let url = "https://dog.ceo/api/breeds/image/random";

function main() {
  fetch(url)
    .then(res => res.json())
    .then(res => {
      // do something with `res`
    })
    .catch(err => {
      // handle `err`
    });
}

main(); 
```

Enter fullscreen mode Exit fullscreen mode

由于天生懒惰，我们创建函数来隐藏一些样板文件，这样我们就可以在代码库中编写更少的代码:

```
let readFile = require("./readFile");

function main() {
  readFile("./test.js")
    .then(file => {
      // do something with `file`
    })
    .catch(err => {
      // handle `err`
    });
}

main();

// readFile.js
let util = require("util");
let fs = require("fs");

let read = util.promisify(fs.readFile);

module.exports = path => {
  return read(path, { encoding: "utf8" })
    .then(file => {
      return file;
    })
    .catch(err => {
      throw err;
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

还有:

```
let api = require("./api");

function main() {
  api.getRandomDog()
    .then(res => {
      // do something with `res`
    })
    .catch(err => {
      // handle `err`
    });
}

main();

// api.js
let url = "https://dog.ceo/api/breeds/image/random";

let api = {};

api.getRandomDog = () => {
  return fetch(url)
    .then(res => res.json())
    .catch(err => {
      throw err;
    });
};

module.exports = api; 
```

Enter fullscreen mode Exit fullscreen mode

尽管如此，这里还是有很多重复，在这段代码的两边都有`.then`和`.catch`。

他们说`async` / `await`可以修复这个，所以...那我们试试吧？

# 转换为`async` / `await`

来看看我们的 Node.js 在`async` / `await` :
做的怎么样

```
let readFile = require("./readFile");

async function main() {
  try {
    let res = await readFile("./test.js");
    // do something with `file`
  } catch (err) {
    // handle `err`
  }
}

main();

// readFile.js
let util = require("util");
let fs = require("fs");

let read = util.promisify(fs.readFile);

module.exports = async path => {
  try {
    let res = await read(path, { encoding: "utf8" });
    return res;
  } catch (err) {
    throw err;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们怎样才能用它来找回我们的狗

```
let api = require("./api");

async function main() {
  try {
    let res = await api.getRandomDog();
    // do something with `res`
  } catch (err) {
    // handle `err`
  }
}

main();

// api.js
let url = "https://dog.ceo/api/breeds/image/random";

let api = {};

api.getRandomDog = async () => {
  try {
    let res = await fetch(url);
    let json = await res.json();
    return json;
  } catch (err) {
    throw err;
  }
};

module.exports = api; 
```

Enter fullscreen mode Exit fullscreen mode

咳...我想我们用另一个问题改变了一个问题。现在两个地方都有`try...catch`。考虑到我们当前的消费者/服务界面，我们已经:

1.  在我们的`main()`函数中，我们调用一个“服务”( **readFile** 和 **api)。**
2.  我们的“服务”函数返回一个`Promise`
3.  当完成时，我们的服务**返回**一个有效载荷
4.  当被拒绝时，我们的服务**抛出**一个错误

嗯（表示踌躇等）...也许这就是问题所在！对于满足和拒绝的场景，我们的消费者/服务之间的接口**不同**。

刷新我们对 [Go](http://golang.org/) 的记忆，例如顶部的

```
 func main() {
  res, err := http.Get("http://example.com/")

  if err != nil {
    // handle `err`
  }

  // do something with `res`
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们对满足和拒绝场景都有相同的接口。

让我们用上一个`async` / `await`例子来试试吧！

# 与`async` / `await`统一返回界面

在我们的 Node.js 示例中:

```
let readFile = require("./readFile");

async function main() {
  let [err, file] = await readFile("./test.js");

  if (err) {
    // handle `err`
  }

  // do something with `file`
}

main();

// readFile.js
let util = require("util");
let fs = require("fs");

let read = util.promisify(fs.readFile);

module.exports = async path => {
  try {
    let res = await read(path, { encoding: "utf8" });
    return [null, res];
  } catch (err) {
    return [err, null]
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

还有我们的 Fetch API:

```
let api = require("./api");

async function main() {
  let [err, res] = await api.getRandomDog();

  if (err) {
    // handle `err`
  }

  // do something with `res`
}

main();

// api.js
let url = "https://dog.ceo/api/breeds/image/random";

let api = {};

api.getRandomDog = async () => {
  try {
    let res = await fetch(url);
    let json = await res.json();
    return [null, json];
  } catch (err) {
    return [err, null]
  }
};

module.exports = api; 
```

Enter fullscreen mode Exit fullscreen mode

干得好！！🎉🎉🎉

那正是我们要找的！我们的`main()`函数看起来像我们的 Go 示例，现在我们已经在“服务”函数中隔离了所有的`try...catch`。

使用这种方法，你可以清理 Node.js 中间件/控制器，在你的前端，比如说使用 React/Redux，清理`redux-thunks`或`redux-saga`函数/生成器。

您还可以单独对这些“服务”功能进行单元测试，并保证它们返回预期的接口/数据。