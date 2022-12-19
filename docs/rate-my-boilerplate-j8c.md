# 雷杜克，一个样板

> 原文：<https://dev.to/janpauldahlke/rate-my-boilerplate-j8c>

[img by Jase Ess](https://unsplash.com/@jaseess)

# 你好，亲爱的戴夫·托，

我在为[即时数据](http://www.instant-data.com/)工作。在大型广告公司环境中提供解决方案的小公司。主要是内部工具。

在玩了一会儿 create-react-app 后，我想与你分享我最新的样板文件，并征求你的意见或改进。你可以在 github 的这里来看我[。
在这方面，我遵循鸭子的模式，**在这种模式下，你试图保持所有的 redux 和存储在一个单一的文件**，但为了干燥，我会给你提供 README.MD。我感谢任何反馈；-)](https://github.com/janpauldahlke/react-ts-boilerplate-2018/)

它是通过 CLI 工具

`create-react-app <your-appname> --scripts-version=react-scripts-ts`

创建的，并通过运行

`npm eject`
根据我的需要定制

### 先决条件

已安装的节点和 npm 版本 8 或更高版本。

## 可用脚本

在项目目录中，您可以运行:

#### `npm start`

打开 [http://localhost:3000](http://localhost:3000) 在浏览器中查看。

#### `npm run build`

将生产应用程序构建到`build`文件夹中。

#### `npm run seed`认证种子

为了让样板文件从头开始工作，并模拟 Auth 流，我们放置了一个 JSON-Server。
当您实施自己的工作流程时，您应该删除整个 seed 文件夹，或者更好地将其添加到您的`.gitignore`中。简单的利用一下`npm run seed`。你会在`json-server --port 4000 --watch -- seed/db.json`得到服务。

#### 文件夹结构

创建后，您的项目应该如下所示:

```
my-app/
  README.md
  node_modules/
  package.json
  public/
    index.html
    favicon.ico
  seed/
    /* a helper fake backend for firsttimers */  
  src/
    index.tsx
    components/
      App.tsx
    containers/
      /* your connected containers here */
    ducks/
      /* your ducks here */
    services/
      /* put all your global helpers inside here */
    store/
      history.ts
      index.ts
      localStorage.ts
  types/
    /*your types go here */ 
```

#### 调试和开发工具

我强烈建议您安装并使用这些浏览器扩展。

*   [redux-devtools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)
*   [试剂-devtools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)

#### 材质-用户界面和装饰

装饰者的实验特性在`tsconfig.json`中被激活。通过这种方式，可以将带样式装饰器与`recompose package`的`compose`结合使用。
示例用法如下:

```
import { compose } from 'recompose';
import { withStyles } from '@material-ui/core/styles';

@(compose(withStyles(styles)) as any)
class Example extends React.Component<IExampleProps, IExampleState> {...} 
```

为了在 material-ui 中使用主题，我们需要将应用程序的内容包装在一个

```
<MuiThemeProvider theme={createMuiTheme()}>
  //any content here
</MuiThemeProvider> 
```

[阅读更多关于材料用户界面主题的信息](https://material-ui.com/customization/themes/)

### 环境

有可能提供 ENV 变量。为此使用`.env`文件。
请注意，他们需要遵循`REACT_APP_` `EXAMPLE=FOOBAR`的命名约定。
在项目内部，人们可以在`proccess.env.REACT_APP_EXAMPLE`前使用它们。在一个新的起点，它被设置为播种路径，所以你可能想在那里改变它。

### HTTP 请求

请使用 axios 处理您的请求。它支持承诺，也支持异步/等待。

还要确保你的服务器上允许 crossorigin-access。

### 鸭

我们遵循“鸭子”模式来构建我们的应用程序。点击此处了解详情
[https://github.com/erikras/ducks-modular-redux](https://github.com/erikras/ducks-modular-redux)
模式是将所有这些都放在一只鸭子里:

*   动作枚举
*   动作类型
*   动作创建者
*   减速器功能
*   主减速器
*   砰的一声
*   initialStore

### 消息组件

在这个样板文件中有一个基本的例子，展示了高级消息或通知组件的使用和消费。将 NofictationDuck 导入到您希望 NotificationStore 被捕获的 Ducks 中。

```
import NotificationDuck from '~/src/ducks/notification';

your.action()
  .then(() => /* success */)
  .catch((err) => {
      dispatch(NotificationDuck.throwNotificationWithError({text: 'your text', title: 'your title'}))
  }) 
```

但是您也可以将它们直接连接到组件。请记住使用`RESET_NOTIFICATION_STORE`操作重置 NotificationStore！

### 商店

使用文件夹`src/store`根据需要配置 redux 的存储。也有可能订阅部分存储到您的本地存储。为此，检查`index.ts`并简要查看一下商店订阅模式。

```
store.subscribe(throttle(() => {
  const storage = store.getState() as RootState;
  const AuthStore = storage.AuthStore;

  saveState({
    AuthStore
  } as RootState);
}, 300)); 
```

请使用`throttle` lodash-helper。`JSON.parse()/JSON.stringyfy`在 Javascript 中都很贵。

## 服务

任何你想重复消费的东西都属于服务。另一个词可能是全球帮助者。

*   apicontroller ->可以配置的 axois 实例。将它导入到你的鸭子中，制成动词。
*   authenticationService ->可以与 ducks 内部的 apicontroller 结合使用来验证 auth-Status

在你的鸭子里面 thunk 中的一个用法示例

```
import axios from '../../services/apicontroller';

public static getAuth() {
    return function (dispatch: any, getState:() => RootState): Promise<void> {
      dispatch(AuthDuck.getAuthAction());

      //return the axois object with header, pass the state by `getState()` to auth and GET the route with `get('/yourRoute')`

      return axios(getState()).get('/Authentication')
        .then((res) => {
          dispatch(AuthDuck.getAuthSuccessAction(res.data));
         })
        .catch((err: Error) => {
          /**
        });
    };
  } 
```

还有整整一个`react-router` v4 部分，我会在 50 :heart: :-)