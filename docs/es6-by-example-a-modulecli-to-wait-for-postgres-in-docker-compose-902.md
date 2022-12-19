# ES6 示例:在 docker-compose 中等待 Postgres 的模块/CLI

> 原文：<https://dev.to/hugo__df/es6-by-example-a-modulecli-to-wait-for-postgres-in-docker-compose-902>

当使用 docker-compose 时，让任何依赖 Postgres 的东西在启动前等待它启动是一个好习惯。这避免了应用程序内部的连接问题。

这篇文章介绍了如何提供这种功能，既作为一个 CLI，又作为一个模块，既作为 CommonJS 模块(`require`)又作为 ES 模块，**，不需要编译**。

"一个快速、生产就绪、零依赖性的 ES 模块加载器，适用于 Node 6+！"是 [esm](https://github.com/standard-things/esm) 的承诺。从这个示例项目来看，它是有效的。

*   没有构建步骤编写 ES 模块🎨
*   [郑山默认了🗃](#sane-defaults)
    *   [试图析构空的或未定义的 0️⃣](#attempting-to-destructure-null-or-undefined)
    *   [‘null’保持不变，undefined 成为默认值🔎](#null-remains-undefined-gets-defaulted)
*   [等待异步/等待🛎的 postgres】](#waiting-for-postgres-with-asyncawait)
*   [作为 CLI 与`meow`集成😼](#integrating-as-a-cli-with-meow)
*   [包装和清理📤](#packaging-and-clean-up)
*   [临时演员](#extras)
    *   [用 np 发布到 NPM](#publishing-to-npm-with-np)
    *   [指向模块的 ESM 版本](#pointing-to-the-esm-version-of-the-module)
    *   [承诺等待 pg 实现](#a-promise-wait-for-pg-implementation)

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

# 编写 ES 模块，无需构建步骤🎨

首先我们安装`esm` : `npm install --save esm`。
接下来，我们需要为我们的模块`wait-for-pg.js` :
准备一个文件

```
export const DEFAULT_MAX_ATTEMPTS = 10;
export const DEFAULT_DELAY = 1000; // in ms 
```

Enter fullscreen mode Exit fullscreen mode

尝试使用节点运行此文件将引发:

```
$ node wait-for-pg.js
/wait-for-pg/wait-for-pg.js:1
export const DEFAULT_MAX_ATTEMPTS = 10;
^^^^^^

SyntaxError: Unexpected token export 
```

Enter fullscreen mode Exit fullscreen mode

`export`和`import`还不能在节点中工作(没有标志)，但是下面的运行:

```
$ node -r esm wait-for-pg.js 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想把它作为一个脚本来运行，比如说我们想让其他人通过`require`来使用它，我们需要一个`index.js`，包含以下内容:

```
require = require('esm')(module);
module.exports = require('./wait-for-pg'); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以将`index.js`作为脚本运行:

```
$ node index.js 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以`require`它:

```
$ node # start the Node REPL
> require('./index.js')
{ DEFAULT_MAX_ATTEMPTS: 10,
  DEFAULT_DELAY: 1000 } 
```

Enter fullscreen mode Exit fullscreen mode

为了告诉用户想要用节点`require`包，我们可以使用`package.json` :
中的`"main"`字段

```
{  "main":  "index.js",  "dependencies":  {  "esm":  "^3.0.62"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

# 郑山默认🗃

为了默认`databaseUrl`、`maxAttempts`和`delay`，我们使用 ES6 默认参数+参数析构。
让我们来看看一些我们希望避免的默认参数的陷阱:

1.  试图析构“null”或“undefined”
2.  ' null '保持不变，undefined 成为默认值

## 试图析构空的或未定义的 0️⃣

```
export const DEFAULT_MAX_ATTEMPTS = 5;
export const DEFAULT_DELAY = 1000; // in ms

export function waitForPostgres({
  databaseUrl = (
    process.env.DATABASE_URL || 
    'postgres://postgres@localhost'
  ),
  maxAttempts = DEFAULT_MAX_ATTEMPTS,
  delay = DEFAULT_DELAY
}) {
  console.log(
    databaseUrl, 
    maxAttempts,
    delay
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

下面的调用将抛出:

```
$ node -r esm # node REPL
> import { waitForPostgres } from './wait-for-pg';
> waitForPostgres()
TypeError: Cannot destructure property `databaseUrl` of 'undefined' or 'null'.
    at waitForPostgres (/wait-for-pg/wait-for-pg.js:4:19)
> waitForPostgres(null)
TypeError: Cannot destructure property `databaseUrl` of 'undefined' or 'null'.
    at waitForPostgres (/wait-for-pg/wait-for-pg.js:4:19) 
```

Enter fullscreen mode Exit fullscreen mode

为了避免这种情况，我们应该添加`= {}`来默认被析构的参数(`wait-for-pg.js` ):

```
export const DEFAULT_MAX_ATTEMPTS = 5;
export const DEFAULT_DELAY = 1000; // in ms

export function waitForPostgres({
  databaseUrl = (
    process.env.DATABASE_URL || 
    'postgres://postgres@localhost'
  ),
  maxAttempts = DEFAULT_MAX_ATTEMPTS,
  delay = DEFAULT_DELAY
} = {}) {
  console.log(
    databaseUrl, 
    maxAttempts,
    delay
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

它现在运行:

```
$ node -r esm # node REPL
> import { waitForPostgres } from './wait-for-pg';
> waitForPostgres()
postgres://postgres@localhost 10 1000 
```

Enter fullscreen mode Exit fullscreen mode

当没有传递参数时，这些值被成功默认。然而以下仍然错误:

```
> waitForPostgres(null)
postgres://postgres@localhost 10 1000
TypeError: Cannot destructure property `databaseUrl` of 'undefined' or 'null'.
    at waitForPostgres (/wait-for-pg/wait-for-pg.js:4:19) 
```

Enter fullscreen mode Exit fullscreen mode

## ‘null’保持不变，undefined 成为默认值🔎

```
$ node -r esm # node REPL
> import { waitForPostgres } from './wait-for-pg';
> waitForPostgres({ databaseUrl: null, maxAttempts: undefined })
null 10 1000 
```

Enter fullscreen mode Exit fullscreen mode

显式设置为`null`的值不会被默认，而显式的`undefined`和隐式的`undefined`会被默认，这就是默认参数的工作方式，并不完全像传统的写法:

```
export const DEFAULT_MAX_ATTEMPTS = 5;
export const DEFAULT_DELAY = 1000; // in ms

export function waitForPostgres(options) {
  const databaseUrl = (
    options && options.databaseUrl ||
    process.env.DATABASE_URL ||
    'postgres://postgres@localhost'
  );
  const maxAttempts = options && options.maxAttempts || DEFAULT_MAX_ATTEMPTS;
  const delay = options && options.delay || DEFAULT_DELAY;
  console.log(
    databaseUrl, 
    maxAttempts,
    delay
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这将产生以下结果:

```
$ node -r esm # node REPL
> import { waitForPostgres } from './wait-for-pg';
> waitForPostgres({ databaseUrl: null, maxAttempts: undefined })
'postgres://postgres@localhost' 10 1000 
```

Enter fullscreen mode Exit fullscreen mode

因为`null`和`undefined`一样虚假🙂。

# 用异步/等待🛎等待 Postgres

实施`wait-for-pg`的时间。
为了等待 Postgres，我们需要:

*   尝试连接到它
*   如果失败了
    *   请稍后再试
*   如果成功了
    *   结束

让我们安装一个 Postgres 客户端，`pg`使用:`npm install --save pg`

`pg`有一个`Client`对象，我们可以在实例化它时将一个数据库 URL 传递给它(`new Client(databaseUrl)`)。那个`client`实例有一个`.connect`方法，该方法返回一个承诺，该承诺在连接成功时解析，否则拒绝。
这意味着如果我们将`waitForPostgres`函数标记为`async`，我们就可以`await`调用`.connect`。

当`await`承诺时，拒绝将抛出一个错误，所以我们将所有的逻辑包装在一个`try/catch`中。

*   如果客户端连接成功，我们翻转循环条件，以便函数终止
*   如果客户端连接失败
    *   我们递增`retries`计数器，如果它超过最大重试次数(`maxAttempts`)，我们`throw`，因为我们在`async`函数中`throw`相当于做`Promise.reject`
    *   否则，我们调用另一个返回承诺(`timeout`)的函数，允许我们在循环体的另一次迭代之前等待
*   我们确保`export function waitForPostgres() {}`

`wait-for-pg.js` :

```
import { Client } from 'pg';

export const DEFAULT_MAX_ATTEMPTS = 10;
export const DEFAULT_DELAY = 1000; // in ms

const timeout = ms => new Promise(
  resolve => setTimeout(resolve, ms)
);

export async function waitForPostgres({
  databaseUrl = (
    process.env.DATABASE_URL || 
    'postgres://postgres@localhost'
  ),
  maxAttempts = DEFAULT_MAX_ATTEMPTS,
  delay = DEFAULT_DELAY
} = {}) {
  let didConnect = false;
  let retries = 0;
  while (!didConnect) {
    try {
      const client = new Client(databaseUrl);
      await client.connect();
      console.log('Postgres is up');
      client.end();
      didConnect = true;
    } catch (error) {
      retries++;
      if (retries > maxAttempts) {
        throw error;
      }
      console.log('Postgres is unavailable - sleeping');
      await timeout(delay);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 与`meow`集成为一个 CLI😼

[喵](https://www.npmjs.com/package/meow)是 Sindre Sohrus 的 CLI app 助手，安装:`npm install --save meow`
创建`wait-for-pg-cli.module.js` :

```
import {
  waitForPostgres,
  DEFAULT_MAX_ATTEMPTS,
  DEFAULT_DELAY
} from './wait-for-pg';
import meow from 'meow';

const cli = meow(`
    Usage
      $ wait-for-pg <DATABASE_URL>
    Options
      --max-attempts, -c Maximum number of attempts, default: ${DEFAULT_MAX_ATTEMPTS} --delay, -d Delay between connection attempts in ms, default: ${DEFAULT_DELAY} Examples
      $ wait-for-pg postgres://postgres@localhost:5432 -c 5 -d 3000 && npm start
      # waits for postgres, 5 attempts at a 3s interval, if
      # postgres becomes available, run 'npm start'
`, {
    inferType: true,
    flags: {
      maxAttempts: {
        type: 'string',
        alias: 'c'
      },
      delay: {
        type: 'string',
        alias: 'd'
      }
    }
  });
console.log(cli.input, cli.flags); 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`inferType`以便`maxAttempts`和`delay`的值被转换成数字而不是字符串。
我们可以使用
来运行它

```
$ node -r esm wait-for-pg-cli.module.js
[] {} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个模板字符串，它将把`${}`中的内容替换为相应表达式中的值(在本例中是`DEFAULT_MAX_ATTEMPTS`和`DEFAULT_DELAY`变量的值)

```
`
  Usage
    $ wait-for-pg <DATABASE_URL>
  Options
    --max-attempts, -c Maximum number of attempts, default: ${DEFAULT_MAX_ATTEMPTS} --delay, -d Delay between connection attempts in ms, default: ${DEFAULT_DELAY} Examples
    $ wait-for-pg postgres://postgres@localhost:5432 -c 5 -d 3000 && npm start
    # waits for postgres, 5 attempts at a 3s interval, if
    # postgres becomes available, run 'npm start'
`; 
```

Enter fullscreen mode Exit fullscreen mode

获取标志和第一个输入，`wait-for-pg-cli.module.js` :

```
import {
  waitForPostgres,
  DEFAULT_MAX_ATTEMPTS,
  DEFAULT_DELAY
} from './wait-for-pg';
import meow from 'meow';

const cli = meow(`
    Usage
      $ wait-for-pg <DATABASE_URL>
    Options
      --max-attempts, -c Maximum number of attempts, default: ${DEFAULT_MAX_ATTEMPTS} --delay, -d Delay between connection attempts in ms, default: ${DEFAULT_DELAY} Examples
      $ wait-for-pg postgres://postgres@localhost:5432 -c 5 -d 3000 && npm start
      # waits for postgres, 5 attempts at a 3s interval, if
      # postgres becomes available, run 'npm start'
`, {
    inferType: true,
    flags: {
      maxAttempts: {
        type: 'string',
        alias: 'c'
      },
      delay: {
        type: 'string',
        alias: 'd'
      }
    }
  });
waitForPostgres({
  databaseUrl: cli.input[0],
  maxAttempts: cli.flags.maxAttempts,
  delay: cli.flags.delay,
}).then(
  () => process.exit(0)
).catch(
  () => process.exit(1)
); 
```

Enter fullscreen mode Exit fullscreen mode

如果在本地主机上没有运行 Postgres 实例，那么下面的内容就不会打印出来`Here`，这要感谢`.catch`块中的`process.exit(1)`:

```
$ node -r esm wait-for-pg-cli.module.js -c 5 && echo "Here"
Postgres is unavailable - sleeping
Postgres is unavailable - sleeping
Postgres is unavailable - sleeping
Postgres is unavailable - sleeping
Postgres is unavailable - sleeping 
```

Enter fullscreen mode Exit fullscreen mode

# 包装和清理📤

我们可以使用`package.json`中的`"bin"`键来轻松运行命令:

```
{  "main":  "index.js",  "bin":  {  "wait-for-pg":  "./wait-for-pg-cli.js"  },  "dependencies":  {  "esm":  "^3.0.62",  "meow":  "^5.0.0",  "pg":  "^7.4.3"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

其中`wait-for-pg-cli.js`为:

```
#!/usr/bin/env node
require = require("esm")(module/*, options*/);
module.exports = require('./wait-for-pg-cli.module'); 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记运行`chmod +x wait-for-pg-cli.js`
`esm`允许我们使用顶级 await，这意味着在`wait-for-pg-cli.module.js`中，我们可以替换:

```
waitForPostgres({
  databaseUrl: cli.input[0],
  maxAttempts: cli.flags.maxAttempts,
  delay: cli.flags.delay,
}).then(
  () => process.exit(0)
).catch(
  () => process.exit(1)
); 
```

Enter fullscreen mode Exit fullscreen mode

同:

```
try {
  await waitForPostgres({
    databaseUrl: cli.input[0],
    maxAttempts: cli.flags.maxAttempts,
    delay: cli.flags.delay,
  });
  process.exit(0);
} catch (error) {
  process.exit(1);
} 
```

Enter fullscreen mode Exit fullscreen mode

运行 CLI 抛出:

```
$ ./wait-for-pg-cli.js
wait-for-pg/wait-for-pg-cli.module.js:36
  await waitForPostgres({
  ^^^^^

SyntaxError: await is only valid in async function 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在`package.json` :
中用`"await": true`加上`"esm"`

```
{  "main":  "index.js",  "bin":  {  "wait-for-pg":  "./wait-for-pg-cli.js"  },  "dependencies":  {  "esm":  "^3.0.62",  "meow":  "^5.0.0",  "pg":  "^7.4.3"  },  "esm":  {  "await":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这个现在有效:

```
$ ./wait-for-pg-cli.js -c 1
Postgres is unavailable - sleeping 
```

Enter fullscreen mode Exit fullscreen mode

# 加贺

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo) [订阅](https://buttondown.email/hugo)可以在你的收件箱里获得最新的帖子(比任何人都要早)。

## 用 np 发布到 npm

1.  运行:`npm install --save-dev np`
2.  确保在`package.json`中有一个有效的`"name"`字段，例如`"@hugodf/wait-for-pg"`
3.  `npx np`用于 npm v5+或`./node_modules/.bin/np` (npm v4 及以下)

## 指向 ESM 版本的模块

使用`package.json`
中的`"module"`字段

```
{  "name":  "wait-for-pg",  "version":  "1.0.0",  "description":  "Wait for postgres",  "main":  "index.js",  "module":  "wait-for-pg.js",  "bin":  {  "wait-for-pg":  "./wait-for-pg-cli.js"  },  "dependencies":  {  "esm":  "^3.0.62",  "meow":  "^5.0.0",  "pg":  "^7.4.3"  },  "devDependencies":  {  "np":  "^3.0.4"  },  "esm":  {  "await":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 一个承诺等待 pg 实现

```
import { Client } from 'pg';

export const DEFAULT_MAX_ATTEMPTS = 10;
export const DEFAULT_DELAY = 1000; // in ms

const timeout = ms => new Promise(
  resolve => setTimeout(resolve, ms)
);

export function waitForPostgres({
  databaseUrl = (
    process.env.DATABASE_URL ||
    'postgres://postgres@localhost'
  ),
  maxAttempts = DEFAULT_MAX_ATTEMPTS,
  delay = DEFAULT_DELAY,
} = {},
  retries = 1
) {
  const client = new Client(databaseUrl);
  return client.connect().then(
    () => {
      console.log('Postgres is up');
      return client.end();
    },
    () => {
      if (retries > maxAttempts) {
        return Promise.reject(error);
      }
      console.log('Postgres is unavailable - sleeping');
      return timeout(delay).then(
        () => waitForPostgres(
          { databaseUrl, maxAttempts, delay },
          retries + 1
        )
      );
    }
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

[马太·亨利](https://unsplash.com/@matthewhenry?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Matthew Henry")