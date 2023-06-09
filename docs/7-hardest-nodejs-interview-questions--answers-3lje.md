# 7 个最难的 Node.js 面试问答

> 原文：<https://dev.to/fullstackcafe/7-hardest-nodejs-interview-questions--answers-3lje>

[![7 Hardest Node.js Interview Questions & Answers](img/036394b4cc187b8516ab41405b99fe03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XQuWmKnq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/674268/pexels-photo-674268.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D350) 
Node.js 是一个开源的、跨平台的 JavaScript 运行时环境，在服务器端执行 JavaScript 代码。Node.js 允许开发人员使用 JavaScript 进行服务器端脚本编写——在页面被发送到用户的 web 浏览器之前，在服务器端运行脚本来生成动态网页内容。

> 🔴最初发表于 [FullStack。咖啡馆-杀死你的技术&编码面试](https://www.fullstack.cafe/?utm_source=dev&utm_medium=blog)

### Q1:为开发和生产环境提供一些配置文件分离的例子

> 题目:**node . js**T2】难度:⭐⭐⭐⭐

完美无瑕的配置设置应确保:

*   可以从文件和环境变量中读取密钥
*   秘密保存在提交的代码之外
*   配置是分层的，以便于查找

考虑下面的配置文件:

```
var config = {
  production: {
    mongo : {
      billing: '****'
    }
  },
  default: {
    mongo : {
      billing: '****'
    }
  }
}

exports.get = function get(env) {
  return config[env] || config.default;
} 
```

Enter fullscreen mode Exit fullscreen mode

它的用法:

```
const config = require('./config/config.js').get(process.env.NODE_ENV);
const dbconn = mongoose.createConnection(config.mongo.billing); 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【github.com/i0natan/nodebestpractices】T2

### Q2:node . js 有什么计时功能？

> 题目:**node . js**T2】难度:⭐⭐⭐⭐

Node.js 中的 Timers 模块包含在设定的时间段后执行代码的函数。

*   **setTimeout/clear time out**-可用于在指定的毫秒数后调度代码执行
*   **setInterval/clearInterval**-可用于多次执行一段代码
*   **set immediate/clear immediate**-将在当前事件循环周期结束时执行代码
*   **process . next tick**——用于调度在事件循环的下一次迭代中调用的回调函数

```
function cb(){
  console.log('Processed in next iteration');
}
process.nextTick(cb);
console.log('Processed in the first iteration'); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Processed in the first iteration
Processed in next iteration 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【github.com/jimuyouyou】T2

### Q3:解释 Node.js 中的反应器模式是什么？

> 题目:**node . js**T2】难度:⭐⭐⭐⭐⭐

**反应器模式**是 Node.js 中非阻塞 I/O 操作的一种思想。该模式提供了一个与每个 I/O 操作相关联的处理程序(对于 Node.js，是一个*回调函数*)。当产生一个 I/O 请求时，它被提交给一个*解复用器*。

这个*解复用器*是一个通知接口，用于在非阻塞 I/O 模式下处理并发，并以事件的形式收集每个请求，并将每个事件排列在一个队列中。因此，解复用器提供了*事件队列*。

同时，还有一个事件循环，它遍历事件队列中的项目。每个事件都有一个与之关联的回调函数，当事件循环迭代时，该回调函数被调用。

🔗**来源:**【hackernoon.com】T2

### Q4:node . js 的 LTS 版本是什么为什么要关注？

> 题目:**node . js**T2】难度:⭐⭐⭐⭐⭐

Node.js 的一个 **LTS(长期支持)**版本接收所有关键的错误修复、安全更新和性能改进。

Node.js 的 LTS 版本至少支持 18 个月，并由偶数版本号表示(例如 4、6、8)。它们最适合生产，因为 LTS 发布系列专注于稳定性和安全性，而当前的*发布系列寿命更短，代码更新更频繁。对 LTS 版本的更改仅限于针对稳定性的错误修复、安全更新、可能的 npm 更新、文档更新和某些性能改进，可以证明这些改进不会破坏现有应用程序。*

🔗**来源:**【github.com/i0natan/nodebestpractices】T2

### Q5:为什么要把快递的‘app’和‘server’分开？

> 题目:**node . js**T2】难度:⭐⭐⭐⭐⭐

将 API 声明与网络相关的配置(端口、协议等)分开，允许在不执行网络调用的情况下测试 API，并带来所有好处:快速测试执行和获得代码的覆盖度量。它还允许在灵活和不同的网络条件下部署相同的 API。好处:更好的关注点分离和更干净的代码。

API 声明，应该驻留在 app.js:

```
var app = express();
app.use(bodyParser.json());
app.use("/api/events", events.API);
app.use("/api/forms", forms); 
```

Enter fullscreen mode Exit fullscreen mode

服务器网络声明，应该驻留在/bin/www:

```
var app = require('../app');
var http = require('http');

/**
 * Get port from environment and store in Express.
 */

var port = normalizePort(process.env.PORT || '3000');
app.set('port', port);

/**
 * Create HTTP server.
 */

var server = http.createServer(app); 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【github.com/i0natan/nodebestpractices】T2

### Q6:process . next tick()和 setImmediate()有什么区别？

> 题目:**node . js**T2】难度:⭐⭐⭐⭐⭐

`process.nextTick()`和`setImmediate()`的区别在于`process.nextTick()`将一个动作的执行推迟到事件循环的下一次循环，或者一旦正在进行的事件循环的执行完成，它就简单地调用回调函数，而`setImmediate()`在事件循环的下一个循环执行一个回调，并返回事件循环执行任何 I/O 操作。

🔗**来源:**【codingdefined.com】T2

### Q7:重写不带 try/catch 块的代码示例

> 题目:**node . js**T2】难度:⭐⭐⭐⭐⭐

考虑代码:

```
async function check(req, res) {
  try {
    const a = await someOtherFunction();
    const b = await somethingElseFunction();
    res.send("result")
  } catch (error) {
    res.send(error.stack);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

重写不带 try/catch 块的代码示例。

**答案:**

```
async function getData(){
  const a = await someFunction().catch((error)=>console.log(error));
  const b = await someOtherFunction().catch((error)=>console.log(error));
  if (a && b) console.log("some result")
} 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您想知道哪个特定函数导致了错误:

```
async function loginController() {
  try {
    const a = await loginService().
    catch((error) => {
      throw new CustomErrorHandler({
        code: 101,
        message: "a failed",
        error: error
      })
    });
    const b = await someUtil().
    catch((error) => {
      throw new CustomErrorHandler({
        code: 102,
        message: "b failed",
        error: error
      })
    });
    //someoeeoe
    if (a && b) console.log("no one failed")
  } catch (error) {
    if (!(error instanceof CustomErrorHandler)) {
      console.log("gen error", error)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【medium.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *查看更多 FullStack 面试问题&答案上👉 [www.fullstack.cafe](https://www.fullstack.cafe)*