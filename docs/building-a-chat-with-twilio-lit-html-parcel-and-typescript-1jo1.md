# 使用 Twilio、lit-html、package 和 TypeScript 建立聊天

> 原文：<https://dev.to/dkundel/building-a-chat-with-twilio-lit-html-parcel-and-typescript-1jo1>

*[与 Twilio 建立聊天，lit-html，包裹和打字稿](https://www.twilio.com/blog/2018/05/building-a-chat-with-twilio-lit-html-parcel-and-typescript.html)原载于 2018 年 5 月 14 日 [Twilio 博客](https://www.twilio.com/blog)。*

* * *

在构建 web 应用程序时，您经常会遇到这样的情况，您必须尽可能高效地多次呈现类似的元素。这时，许多 web 开发人员开始接触 React、Vue 或 Angular 等框架和库。但是，如果我们只想进行高效的模板化，而不想要这些库的工具和代码开销，那该怎么办呢？

聚合物团队的 [`lit-html`就是一个旨在解决这个问题的库。它使用浏览器原生功能，如](https://github.com/Polymer/lit-html) [`<template/>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) 标签和[标签模板文字](https://css-tricks.com/template-literals/#article-header-id-3)来创建高效的模板。让我们通过用`lit-html`构建一个 [Twilio Chat](https://www.twilio.com/docs/chat) 应用程序来看看如何使用它。

### 先决条件

在我们开始之前，确保你已经安装了 [Node.js](https://nodejs.org/) 和 [npm](https://www.npmjs.com/) 。由于我们将在这个应用程序的某个地方使用 [Twilio Chat](https://www.twilio.com/docs/chat) ，所以您也想获得一个 Twilio 帐户。[免费报名](https://www.twilio.com/try-twilio)。

### 项目设置

首先为您的项目创建一个新文件夹，并初始化一个节点项目:

```
mkdir lit-twilio-chat
cd lit-twilio-chat
npm init -y 
```

在我们进入`lit-html`部分之前，我们需要配置两件事情。`lit-html`是一个`npm`模块，我们必须将其捆绑到应用程序中。为此，我们将使用名为 [`Parcel`](https://parceljs.org/) 的零配置捆绑器，但是如果您觉得使用 [Webpack](https://webpack.js.org/) 更舒服，欢迎您使用那个捆绑器。我们还将在这个项目中使用 [TypeScript](https://www.typescriptlang.org/) ，因为`lit-html`和 Twilio Chat 都非常支持 TypeScript。

安装以下依赖项以获得两个设置:

```
npm install -D parcel-bundler typescript
npm install -S tslib @types/events 
```

接下来，在项目的根目录下创建一个名为`tsconfig.json`的文件，并添加以下内容:

```
{  "compilerOptions":  {  "target":  "es5",  "module":  "ESNext",  "lib":  ["dom",  "es2015",  "es2015.collection"],  "importHelpers":  true,  "strict":  true,  "moduleResolution":  "node"  }  } 
```

然后打开`package.json`并修改`scripts`部分，添加一些新的基于`parcel`的命令:

```
"scripts":  {  "build":  "parcel build src/index.html",  "start":  "parcel src/index.html",  "dev":  "parcel watch src/index.html",  "test":  "echo \"Error: no test specified\" && exit 1"  }, 
```

为了测试我们的设置，在名为`src` :
的文件夹中创建一个`index.html`文件作为我们的包裹条目文件

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Chat with lit-html & TypeScript
</head>

<body>
  <div id="root"></div>
  <script src="./index.ts"></script>
</body>

</html> 
```

在那里，我们通过一个相对路径加载一个文件`index.ts`。package 将检测该文件，并尝试加载它和开始捆绑。在与`index.html`文件相同的目录下创建`index.ts`文件，并将以下代码放入其中:

```
const root = document.getElementById('root') as HTMLElement;
root.innerHTML = '<h1>Hello</h1>'; 
```

通过运行
测试设置是否有效

```
npm start 
```

这将在监视模式下启动`parcel` bundler，以确保每当我们改变某些东西时，文件都会被重新编译。打开浏览器，进入 [http://localhost:1234](http://localhost:1234) 。你应该有一句“你好”的问候:

[![Screenshot of Browser showing Hello](img/467fccf2127ca4fc1d63bd230779598a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hxuxm9eK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zoya5hv7z323912erkyk.png)

### 开始模板化的时间！

到目前为止，我们在没有使用`lit-html`的情况下将一些非常静态的东西渲染到页面上。让我们先从一个小任务开始尝试`lit-html`。让我们呈现与之前相同的内容，加上当前时间，并每秒更新一次。

在我们修改代码之前，请确保将`lit-html`作为依赖项:

```
npm install lit-html -S 
```

我们需要从`lit-html`为我们的代码导入两个函数。一个是`html`，另一个是`render`。`html`实际上是一个带标签的模板函数，意思是我们将这样使用它:

```
const templateResult = html`<h1>Hello</h1>`; 
```

带标签的模板函数的工作方式类似于 JavaScript 中的普通模板字符串，除了它会生成一个 HTML `<template />`并确保有效地重新呈现。这样我们可以在模板中使用变量，也可以嵌套模板。如果你想知道这是如何高效的，一定要看看去年贾斯汀·法格纳尼在 T2 的 Chrome 开发峰会上的演讲。

然后，`render`函数将负责将内容实际呈现到页面上。

相应地修改您的`index.ts`页面:

```
import { html, render } from 'lit-html';

function renderTime(time: Date) {
  return html`
    <p>It\'s: ${time.toISOString()}</p>
  `;
}

function renderHello(time: Date) {
  return html`
    <h1>Hello</h1> ${renderTime(time)} `;
}

const root = document.getElementById('root') as HTMLElement;

setInterval(() => {
  const view = renderHello(new Date());
  render(view, root);
}, 1000); 
```

如果您仍然运行着之前的 package dev 服务器，那么您应该能够在浏览器中切换回 [http://localhost:1234](http://localhost:1234) ，并看到更新了日期的页面。

[![Screenshot of browser showing Hello and time](img/15abde74f07730b189433da849117c2c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pY-Y39je--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bjxxh1wxm59nblytc1l7.png)

### 组件呢？

将事情分解成更小的组件总是重用代码并使其更易维护的好方法。我们可以通过创建呈现不同 HTML 结构和模板的函数来对`lit-html`做同样的事情。让我们为我们的聊天应用程序创建两个组件`ChatMessage`和`ChatMessageList`。创建一个新文件`src/ChatMessage.ts` :

```
import { html } from 'lit-html';

const ChatMessage = (author: string, body: string) => {
  return html`
    <div class="message">
      <p class="message-author">${author}</p>
      <p class="message-body">${body}</p>
    </div>
  `;
};

export default ChatMessage; 
```

这类似于 React 中的[无状态组件](https://reactjs.org/docs/components-and-props.html)。

创建另一个文件`src/ChatMessageList.ts`，并将下面的代码放入其中:

```
import { html } from 'lit-html';
import { repeat } from 'lit-html/lib/repeat';

import ChatMessage from './ChatMessage';

interface Message {
  sid: string;
  author: string;
  body: string; 
}

const ChatMessageList = (messages: Message[]) => {
  return html`
    <div class="message-list"> ${repeat(
        messages,
        msg => msg.sid,
        msg => ChatMessage(msg.author, msg.body)
      )} </div>
  `;
};
export default ChatMessageList; 
```

我们使用了一个来自`lit-html`的名为`repeat`的助手函数，它允许我们有效地遍历一个数组来呈现每个组件。该函数接收一个条目列表和两个函数，一个用于标识一个键，以检查更新了哪些条目，另一个用于呈现内容。我们使用`msg.sid`的唯一性，因为一旦我们整合了 Twilio 聊天，这将是每条消息的唯一键。

让我们通过呈现一些模拟消息来测试我们的两个组件。改变`src/index.ts` :

```
import { html, render } from 'lit-html';

import ChatMessageList from './ChatMessageList';

function renderMessages() {
  const messages = [
    { sid: '1', author: 'Dominik', body: 'Hello' },
    { sid: '2', author: 'Panda', body: 'Ahoy' },
    { sid: '3', author: 'Dominik', body: 'how are you?' }
  ];

  return html`
    <h1>Messages:</h1> ${ChatMessageList(messages)} `;
}

const root = document.getElementById('root') as HTMLElement;

const view = renderMessages();
render(view, root); 
```

切换回 [http://localhost:1234](http://localhost:1234) ，您应该会看到显示的 3 条消息:

[![Screenshot of mock messages displayed](img/41900726939a1a7b57bcbad408a7f796.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R4k0NwXQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5qlo3k56cfbu1dar65jf.png)

### 让我们聊聊天吧

现在我们知道了如何呈现消息列表，让我们添加 Twilio Chat 来创建一个真正的实时聊天。Twilio Chat 提供了一个 JavaScript/TypeScript SDK 来构建所有的消息传递逻辑，包括通道和事件。运行
安装 SDK

```
npm install -S twilio-chat 
```

让我们将整个聊天相关的逻辑移到一个中心位置，该位置将处理界面的呈现以及必要时的更新。

创建一个新文件`src/chat.ts`，并将以下代码放入其中:

```
import Client from 'twilio-chat';
import { Message } from 'twilio-chat/lib/Message';
import { Channel } from 'twilio-chat/lib/channel';
import { html, render as renderLit } from 'lit-html/lib/lit-extended';
import ChatMessageList from './ChatMessageList';

export class Chat {
  private messages: Message[] = [];
  private channel: Channel | null = null;

  constructor(
    private token: string,
    private channelName: string,
    private root: HTMLElement
  ) {
    this.messageSubmit = this.messageSubmit.bind(this);
  }

  private messageSubmit(event: Event) {
  }
} 
```

在这种情况下，我们将使用`lit-extended`而不是普通的`lit-html`来渲染视图。这使我们能够使用事件侦听器之类的功能。

为了使用 Twilio Chat SDK，我们需要使用一个客户端令牌。稍后我们会小心地生成它，但现在我们将把它传入我们想要加入的频道的`Chat`类和`channelName`的构造函数，以及我们想要呈现内容的`root` HTML 元素。

接下来创建一个`render`和`messageSubmit`方法。`render()`将负责从消息列表到输入表单呈现整个 UI。`messageSubmit`将在提交表单时触发(即发送新消息)。将以下函数添加到您的`Chat`类中:

```
 constructor(
    private token: string,
    private channelName: string,
    private root: HTMLElement
  ) {
    this.messageSubmit = this.messageSubmit.bind(this);
  }

  private messageSubmit(event: Event) {
    event.preventDefault();
    const form = event.target as HTMLFormElement;
    const msg: string = form.message.value;
    if (this.channel) {
      this.channel.sendMessage(msg);
    }
    form.message.value = '';
  }

  private render() {
    const view = html` ${ChatMessageList(this.messages)} <form on-submit="${this.messageSubmit}">
      <input type="text" name="message" placeholder="Type your message..." />
      <button type="submit">Send</button>
    </form>
    `;

    renderLit(view, this.root);
  } 
```

我们需要和 Twilio Chat 建立联系。为此，我们必须:

*   创建 Twilio 客户端
*   尝试获取我们想要加入的频道，或者创建一个不存在的频道
*   加入频道
*   检索现有消息
*   收听新的信息

我们将把所有这些功能捆绑到一个公共的`init()`函数和一个`registerChannelListeners()`方法中，如果有新消息进来，这个函数将负责更新 UI。将下面的代码添加到您的`Chat`类中:

```
 private render() {
    const view = html` ${ChatMessageList(this.messages)} <form on-submit="${this.messageSubmit}">
      <input type="text" name="message" placeholder="Type your message..." />
      <button type="submit">Send</button>
    </form>
    `;

     renderLit(view, this.root);
  }

  private registerChannelListeners() {
    if (!this.channel) {
      return;
    }

    this.channel.on('messageAdded', (msg: Message) => {
      this.messages.push(msg);
      this.render();
    });
  }

  async init() {
     const client = await Client.create(this.token);
     try {
       this.channel = await client.getChannelByUniqueName(this.channelName);
     } catch {
       this.channel = await client.createChannel({
         uniqueName: this.channelName
       });
     }
     if (this.channel.status !== 'joined') {
       client.on('channelJoined', () => {
         this.registerChannelListeners();
       });
       await this.channel.join();
     } else {
       this.registerChannelListeners();
     }
     this.messages = (await this.channel.getMessages()).items;
     this.render();
     return this;
   }
} 
```

在测试我们的代码之前，我们需要配置 Twilio Chat。登录到 [Twilio 控制台](https://www.twilio.com/console/chat/dashboard)。转到[聊天仪表板](https://www.twilio.com/console/chat/dashboard)并创建一个新的聊天服务。

对于每个用户，我们必须为聊天生成一个唯一的访问令牌。现在，我们将只使用一个测试令牌，您可以在[聊天测试工具](https://www.twilio.com/console/chat/dev-tools/testing-tools)中生成这个令牌。输入一个任意的“客户身份”(比如用户名)并生成令牌。

现在切换回`src/index.ts`，用以下代码替换代码:

```
import { Chat } from './chat'

async function init() {
  const root = document.getElementById('root') as HTMLElement;
  const token = 'INSERT_YOUR_ACCESS_TOKEN';
  const channel = 'demo';
  const chat = new Chat(token, channel, root);
  await chat.init();
}

init(); 
```

确保用您生成的令牌替换`INSERT_YOUR_ACCESS_TOKEN`。回到 [http://localhost:1234](http://localhost:1234) ，在第二个窗口中打开同一个页面。您应该能够在一个窗口中发送消息，而它们应该在另一个窗口中显示:)

[![gif showing two browsers send messages to each other](img/b7d75d4662d91aa502fc94bd47004dd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tWfgjX6Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0kn9bab3nmbgh0vtw257.gif)

### 提升体验

现在我们已经对访问令牌进行了硬编码，这并不理想。相反，您应该在服务器端动态地生成这个令牌，或者给人们分配不同的用户名，或者提示他们创建一个用户名。我们还在页面加载时加载了整个 Twilio Chat SDK。如果这个人访问了网页，但实际上并不想聊天，该怎么办？

让我们修改我们的代码，动态地获取一个令牌，并且如果用户真的想聊天，通过动态地加载聊天相关的代码来利用一个叫做代码分割的概念。

为了生成访问令牌，我们将使用 Twilio 函数，它允许您直接在 Twilio 基础设施上托管用 Node.js 编写的无服务器函数。

转到 Twilio 控制台的[功能部分，创建一个新的空白功能。指定一个路径，比如`/chat-token`，并将下面的代码放入其中:](https://www.twilio.com/console/runtime/functions/manage) 

```
exports.handler = function(context, event, callback) {  
  // make sure you enable ACCOUNT_SID and AUTH_TOKEN in Functions/Configuration
  const ACCOUNT_SID = context.ACCOUNT_SID;

  // you can set these values in Functions/Configuration or set them here
  const SERVICE_SID = 'CHAT_SERVICE_SID';
  const API_KEY = context.API_KEY || 'enter API Key';
  const API_SECRET = context.API_SECRET || 'enter API Secret';

  // REMINDER: This identity is only for prototyping purposes
  const IDENTITY = event.username || 'only for testing';

  const AccessToken = Twilio.jwt.AccessToken;
  const IpMessagingGrant = AccessToken.IpMessagingGrant;

  const chatGrant = new IpMessagingGrant({
        serviceSid: SERVICE_SID
  });

  const accessToken = new AccessToken(
        ACCOUNT_SID,
        API_KEY,
        API_SECRET
  );

  accessToken.addGrant(chatGrant);
  accessToken.identity = IDENTITY;

  const response = new Twilio.Response();
  response.appendHeader('Access-Control-Allow-Origin', '*');
  response.appendHeader('Access-Control-Allow-Methods', 'GET');
  response.appendHeader('Access-Control-Allow-Headers', 'Content-Type');
  response.appendHeader('Content-Type', 'application/json');
  response.setBody({ token: accessToken.toJwt() });
  callback(null, response);
} 
```

确保用您的聊天服务的 SID 替换`SERVICE_SID`的值。
保存 Twilio 函数，复制路径以备后用。

如果你是第一次使用 Twilio 函数，你还需要进入[配置部分](https://www.twilio.com/console/runtime/functions/configure)，启用`ACCOUNT_SID`并为`API_KEY`和`API_SECRET`添加条目，你可以[在这里生成一对](https://www.twilio.com/console/runtime/api-keys/create)。

[![screenshot of Twilio Functions configure section](img/1ca8e2e3e7904a254c909d350bf19ae8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lf9FpzGF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hfhh1omxhai3rfo5r2zz.png)

现在令牌生成端点已经准备好了，让我们开始使用它。首先让我们更新`src/index.html`文件，添加一个新的用户名输入框和一个开始聊天的按钮:

```
 <input type="text" id="username" placeholder="Username" />
  <button id="btn">Start Chat</button>
  <div id="root"></div>
  <script src="./index.ts"></script> 
```

接下来我们将修改`src/index.ts file`。我们不会立即初始化所有东西，而是等到按钮被点击后再初始化。我们还将使用`import('./chat');`作为动态导入。这将告诉`parcel`我们在后面的部分只需要这个模块中的所有东西，并且`parcel`将自动为我们执行代码拆分。相应地更新你的代码:

```
const TOKEN_URL = `YOUR_TOKEN_URL`;
const btn = document.querySelector('button') as HTMLButtonElement;
const input = document.querySelector('#username') as HTMLInputElement;
const root = document.querySelector('#root') as HTMLElement;

async function init() {
  const username = input.value;
  const tokenUrl = `${TOKEN_URL}?username=${username}`;
  const resp = await fetch(tokenUrl);

  if (!resp.ok) {
    console.error('Failed to request token');
    return;
  }

  const { token } = await resp.json();
  const { Chat } = await import('./chat');
  const channel = 'demo';
  const chat = new Chat(token, channel, root);
  await chat.init();
} 

btn.addEventListener('click', init); 
```

切换回您的浏览器，打开开发者工具并切换到网络窗格。如果您现在刷新页面，您应该会看到一个 JavaScript 文件正在加载。当您点击“开始聊天”按钮时，您应该会看到多个请求。一个是从 Twilio 函数中获取访问令牌，另一个是包含所有聊天功能相关代码的包。

您的聊天应该像以前一样工作，除了您现在可以让多个用户使用不同的用户名聊天。尝试一下同时打开两个窗口。

[![gif of two chat users interacting in separate browser windows](img/be70c241d4c8fb58e02746ab1dac9a91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0JjAooP7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w5jc33qzm90flzfy3rtl.gif)

### 现在聊开了！

就是这样。多亏了`lit-html`，我们构建了一个小巧轻便的聊天 UI，但仍能处理高性能的重新渲染。由于`parcel`的开箱即用支持，我们还能够通过仅加载初始页面加载时必需的内容来利用代码分割来进一步缩短页面加载时间。

如果您想将其部署到生产环境中，请运行:

```
npm run build 
```

这将在一个名为 dist 的文件夹中创建一堆文件，您可以从任何静态托管服务提供这些文件，或者上传到 [Twilio 运行时资产](https://www.twilio.com/console/runtime/assets/public)来提供服务。

在这里，您可以开始设计您的应用程序，或者查看 Twilio Chat 的其他功能，例如:

*   私人频道
*   打字和可达性指示器
*   bot 响应的 Webhooks
*   从服务器插入消息的 REST API

如果您有任何问题，或者您想向我展示您用 Twilio 或`lit-html`创建的很酷的东西，请随时联系我:

*   推特: [@dkundel](https://www.twitter.com/dkundel)
*   电子邮件:[dkundel@twilio.com](mailto:dkundel@twilio.com)
*   GitHub: [dkundel](https://github.com/dkundel)

* * *

*[与 Twilio 建立聊天，lit-html，包裹和打字稿](https://www.twilio.com/blog/2018/05/building-a-chat-with-twilio-lit-html-parcel-and-typescript.html)原载于 2018 年 5 月 14 日 [Twilio 博客](https://www.twilio.com/blog)。*