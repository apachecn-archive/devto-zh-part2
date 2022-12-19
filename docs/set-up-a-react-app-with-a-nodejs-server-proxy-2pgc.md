# 使用 Node.js 服务器代理设置 React 应用程序

> 原文：<https://dev.to/twilio/set-up-a-react-app-with-a-nodejs-server-proxy-2pgc>

*创建 React 应用*是一个让 React 应用启动并运行的伟大工具。然而，当你构建或原型化一个需要服务器端组件的应用程序时，就不那么清楚了，比如[为 Twilio](https://www.twilio.com/docs/iam/access-tokens) [视频](http://twilio.com/docs/video)或[聊天](https://www.twilio.com/docs/chat)生成访问令牌。我发现在同一个项目中使用服务器是最简单的，这样你就可以用一个命令启动所有的东西。

在这篇文章的最后，你将学会如何设置一个与 React 应用一起运行的 [Express](http://expressjs.com/) 服务器。如果你迫不及待，那么你可以直接进入 GitHub 的启动项目[。](https://github.com/philnash/react-express-starter)

### 工作原理

您可以在 *Create React App* 的`package.json` 中设置一个[选项，将非`text/html`请求代理到另一个后端。您可以使用这个特性来代理运行在其他地方的应用程序，但是今天我们希望能够在 React 项目本身中运行服务器。](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#proxying-api-requests-in-development)

我们将把几个 npm 模块放在一起，使运行一个命令来同时运行我们的 React 应用程序和一个 Express 服务器成为可能，这样我们就可以代理它。

### 入门

要完成这篇文章的其余部分，您需要安装 Node.js 和 npm。

首先用*创建 React 应用*创建一个新的 React 应用。你知道吗，你不需要全局安装`create-react-app`包就可以做到这一点？相反，你可以运行:

```
npm init react-app MyNewApp
cd MyNewApp 
```

Enter fullscreen mode Exit fullscreen mode

在幕后， [`npm init`接受一个初始化程序名，在它前面加上`create-`，并使用 npx 来安装和运行命令](https://docs.npmjs.com/cli/init#description)。

运行新的 React 应用程序，确保它是正确生成的。

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

如果你看到一个旋转的 React 标志，那么我们就可以开始了。

### 添加服务器

我们将把我们的服务器依赖项添加到 React 应用程序的`devDependencies`中，因为它们不是构建前端的一部分。

用`Cmd/Ctrl + C`停止服务器，用`npm`安装 Express 和 Body 解析器:

```
npm install express body-parser --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

添加以下依赖项，以帮助我们一起运行前端和服务器:

*   [node-env-run](https://www.npmjs.com/package/node-env-run) - [张秀坤的模块，用于从开发中的配置文件加载环境变量](https://www.twilio.com/blog/2017/08/working-with-environment-variables-in-node-js.html)
*   [nodemon](https://www.npmjs.com/package/nodemon) -当服务器发生变化时自动重启服务器
*   [npm-run-all](https://www.npmjs.com/package/npm-run-all) -用于同时运行多个 npm 脚本
*   [express-pino-logger](https://github.com/pinojs/express-pino-logger) 和 [pino-colada](https://github.com/lrlna/pino-colada) -用于更好的服务器日志记录

```
npm install node-env-run nodemon npm-run-all express-pino-logger pino-colada --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

在项目目录中创建一个名为`.env`的文件来存储我们的环境变量。我们现在还不需要向它添加任何东西，但是以后它会很有用，可以包含我们需要的服务器凭证，比如 API 密钥。

接下来，在项目目录中创建一个名为`server`的新目录和一个`server/index.js`文件。我们将创建一个可以测试的小应用程序。在`server/index.js`中添加以下代码:

```
const express = require('express');
const bodyParser = require('body-parser');
const pino = require('express-pino-logger')();

const app = express();
app.use(bodyParser.urlencoded({ extended: false }));
app.use(pino);

app.get('/api/greeting', (req, res) => {
  const name = req.query.name || 'World';
  res.setHeader('Content-Type', 'application/json');
  res.send(JSON.stringify({ greeting: `Hello ${name}!` }));
});

app.listen(3001, () =>
  console.log('Express server is running on localhost:3001')
); 
```

Enter fullscreen mode Exit fullscreen mode

打开`package.json`，在`"scripts"`对象中添加一个新脚本，使用`node-env-run`和`nodemon` :
运行服务器

```
"scripts":  {  //  other  scripts  "server":  "node-env-run server --exec nodemon | pino-colada"  }, 
```

Enter fullscreen mode Exit fullscreen mode

通过运行脚本测试服务器是否正常运行:

```
npm run server 
```

Enter fullscreen mode Exit fullscreen mode

打开[http://localhost:3001/API/greeting](http://localhost:3001/api/greeting)进行测试。您应该看到一个带有“Hello World！”的 JSON 响应问候。尝试在 URL 中添加一个名为`name`的查询参数，看看会得到什么。

### 运行服务器和 React app

为了同时运行服务器和 React 应用程序，我们需要向`package.json`添加一些东西。

首先，我们将设置服务器的代理。将`"proxy"`键加到`package.json`上。我们已经将服务器设置为在端口 3001 上运行，因此将代理指向 localhost:3001。

```
"proxy":  "http://localhost:3001" 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个脚本来同时运行服务器和前端。为此，我们将使用`npm-run-all`。因为我们将同时运行两个脚本，所以我们希望使用并行模式。`npm-run-all`用`run-p`命令给了我们一个方便的快捷方式。

在`package.json`的`"scripts"`部分增加以下内容:

```
"scripts":  {  //  other  scripts  "server":  "node-env-run server --exec nodemon",  "dev":  "run-p server start"  }, 
```

Enter fullscreen mode Exit fullscreen mode

运行`npm run dev`，React 应用程序和服务器都将启动。然而，我们现在不能在浏览器中加载 localhost:3000/api/greeting，因为 *Create React App* 代理只会用基本 HTML 进行响应。

让我们在 React 应用程序的一个组件中测试它。

### 使用 React 的代理服务器

我们将向`App`组件添加一个表单，该表单将使用`/api/greeting`组件来形成一个问候并将其显示在页面上。在`src/App.js`的 App 组件中添加以下构造函数和函数:

```
class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name: '',
      greeting: ''
    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({ name: event.target.value });
  }

  handleSubmit(event) {
    event.preventDefault();
    fetch(`/api/greeting?name=${encodeURIComponent(this.state.name)}`)
      .then(response => response.json())
      .then(state => this.setState(state));
  } 
```

Enter fullscreen mode Exit fullscreen mode

并将此表单添加到`render`函数中的 JSX:

```
 render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <form onSubmit={this.handleSubmit}>
            <label htmlFor="name">Enter your name: </label>
            <input
              id="name"
              type="text"
              value={this.state.name}
              onChange={this.handleChange}
            />
            <button type="submit">Submit</button>
          </form>
          <p>{this.state.greeting}</p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中打开 React 应用，填写姓名并提交。问候语显示您的 React 应用程序现在正在与您的代理服务器对话。

### 这只是开始

*创建 React 应用*在让 React 应用启动方面做得很好，但是如果你也需要一个服务器端组件，那可能会很复杂。在这篇文章中，你已经看到了如何使用`proxy`选项和使用类似`npm-run-all`的工具运行 Express 服务器。

你可以在 GitHub repo 中查看这篇文章中的所有代码，如果你想用 Express API 构建一个 React 应用程序，可以把它作为一个起点。另外，如果你想用 React 创建一个 [Twilio Video](https://www.twilio.com/docs/video) 或 [Twilio Chat](https://www.twilio.com/docs/chat) 应用程序，Twilio 分支将返回[访问令牌](https://www.twilio.com/docs/iam/access-tokens)。只需遵循自述文件中的说明。

使用[这个模板](https://github.com/philnash/react-express-starter)，构建由 Express 服务器支持的 React 应用程序会更快更容易。希望它给你一个很好的搭建自己想法的平台；我等不及要看你造的东西了！