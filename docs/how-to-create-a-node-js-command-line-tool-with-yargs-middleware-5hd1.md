# 如何用 yargs 中间件创建 node js 命令行工具

> 原文：<https://dev.to/khaled_garbaya/how-to-create-a-node-js-command-line-tool-with-yargs-middleware-5hd1>

[![macro-1452986 1920](img/5b3487b2aa8c426a78f53bdb4540d649.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y_TDaNi3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/3bc97k4uk5q7/1Oden9drYwQ2SkImweSUec/772d43a58924514a1fa1164bbcb57ce3/macro-1452986_1920.jpg)

我经常使用 Express.js ,在处理路线时，我一直是中间件方法的忠实粉丝。

当我开始构建 CLI 工具时，我注意到服务器端程序和命令行工具有很多相似之处。

把用户输入的命令想象成路由或 URL。例如，服务器环境中的`cli-tool project new`将是下面的 url `example.com/project/new`。

cli 世界中的`Request`对象可以是`stdin`，`Response`可以是`stdout`。

不久前，我向 [yargs，](https://github.com/yargs/yargs)介绍了中间件的概念，这是我用来构建 CLI 的主要框架。

如果你想检查代码，你可以检查[拉请求](https://github.com/yargs/yargs/pull/881)。

## 什么是中间件？

中间件是一个可以访问传入数据的功能，在我们的例子中是`argv`。它通常在 yargs 命令之前执行。

中间件功能可以执行以下任务:

*   执行任何代码。
*   对`argv`进行更改。
*   结束请求-响应循环。

```
 -------------- -------------- ---------
stdin ----> argv ----> | Middleware 1 | ----> | Middleware 2 | ---> | Command |
                        -------------- -------------- --------- 
```

Enter fullscreen mode Exit fullscreen mode

## 什么是 yargs？

[![yargs-logo](img/bb382008670134a403ac5939c8e94c8d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2NEl3_sl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/3bc97k4uk5q7/1Hfm8NHHUAMucykccM6e6s/94661dff604c33cdf1437cc27828f8a6/yargs-logo.png)

> Yargs 通过解析参数和生成优雅的用户界面，帮助您构建交互式命令行工具。

这是一个了不起的库，消除了解析命令行参数的所有痛苦，还提供了更多的功能，如:

*   命令和(分组)选项。
*   基于您的参数动态生成的帮助菜单。
*   bash-命令和选项的完成快捷方式。

还有[更](https://github.com/yargs/yargs/blob/master/docs/api.md)...

## 一个简单的 Node.js 命令行工具，带 yargs

[![bash-161382 1280](img/e796c1aaa686f4e8b7349b40a7ec1a86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nmbys6Gv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/3bc97k4uk5q7/2RTr7jXAiQIUemKiagQeoW/6ebc3034dcd6cb5881cf333bdc4aa053/bash-161382_1280.png)

让我们创建一个简单的命令行程序，验证用户将状态保存到一个名为`.credentials`的文件中，以便在接下来的命令中使用。

```
const argv = require('yargs')
const fs = require ('fs')

argv
  .usage('Usage: $0 <command> [options]')
  .command('login', 'Authenticate user', (yargs) => {
        // login command options
        return yargs.option('username')
                    .option('password')
      },
      ({username, password}) => {
        // super secure login, don't try this at home
        if (username === 'admin' && password === 'password') {
          console.log('Successfully loggedin')
          fs.writeFileSync('~/.credentials', JSON.stringify({isLoggedIn: true, token:'very-very-very-secret'}))
        } else {
          console.log('Please provide a valid username and password')
        }
      }
   )
  .command('secret', 'Authenticate user', (yargs) => {
    return yargs.option('token')
  },
    ({token}) => {
      if( !token ) {
          const data = JSON.parse(fs.readFile('~/.credentials'))
          token = data.token
      }
      if (token === 'very-very-very-secret') {
        console.log('the secret word is `Eierschalensollbruchstellenverursacher`') // <-- that's a real german word btw.
      }
    }
  )
  .command('change-secret', 'Authenticate user', (yargs) => {
    return yargs.option('token')
  },
    ({token, secret}) => {
      if( !token ) {
          const data = JSON.parse(fs.readFile('~/.credentials'))
          token = data.token
      }
      if (token === 'very-very-very-secret') {
        console.log(`the new secret word is ${secret}`)
      }
    }
  )
  .argv; 
```

Enter fullscreen mode Exit fullscreen mode

代码中的第一个问题是，每当您想要检查用户是否通过身份验证时，都会有大量重复的代码。

另一个可能出现的问题是当不止一个人在做这个的时候。添加另一个“secret”命令特性将需要有人关心认证，这并不理想。在每个命令之前调用一个身份验证函数，并将令牌附加到您的参数上，怎么样？

## 添加 yargs 中间件

[![building-674828 1280](img/4748549da3237938ef14da532a253fe7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ew2BH8cT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.ctfassets.net/3bc97k4uk5q7/6gOsorEKoowUwKoeeOYQQO/669f8d78ed8f9f113ca02c6c7d1123c7/building-674828_1280.jpg)T3】

```
const argv = require('yargs')
const fs = require ('fs')
cosnt normalizeCredentials = (argv) => {
  if( !argv.token ) {
          const data = JSON.parse(fs.readFile('~/.credentials'))
          token = data.token
      }
  return {token} // this will be added to the args
}
const isAuthenticated = (argv) => {
  if (token !== 'very-very-very-secret') {
    throw new Error ('please login using the command mytool login command')
  }
  return {}
}
argv
  .usage('Usage: $0 <command> [options]')
  .command('login', 'Authenticate user', (yargs) => {
        // login command options
        return yargs.option('username')
                    .option('password')
      },
      ({username, password}) => {
        // super secure login, don't try this at home
        if (username === 'admin' && password === 'password') {
          console.log('Successfully loggedin')
          fs.writeFileSync('~/.credentials', JSON.stringify({isLoggedIn: true, token:'very-very-very-secret'}))
        } else {
          console.log('Please provide a valid username and password')
        }
      }
   )
  .command('secret', 'Authenticate user', (yargs) => {
    return yargs.option('token')
  },
    (argv) => {  
        console.log('the secret word is `Eierschalensollbruchstellenverursacher`') // <-- that's a real german word btw.
    }
  )
  .command('change-secret', 'Authenticate user', (yargs) => {
    return yargs.option('token')
  },
    (argv) => {
        console.log(`the new secret word is ${secret}`)
    }
  )
  .middleware(normalizeCredentials, isAuthenticated)
  .argv; 
```

Enter fullscreen mode Exit fullscreen mode

有了这两个小变化，我们现在有了更清晰的命令代码。这将有助于你维护代码，特别是当你改变验证码。由于有了 [aorinevo](https://github.com/yargs/yargs/pull/1119) 的帮助，命令可以是全局的，也可以是特定于我工作过的部分的命令。

## 现在可以用 yargs 中间件了吗？

为了能够使用 yargs，你需要安装`@next`版本。可以用`npm i yargs@next`安装。