# 反馈工作日志#3 -网络服务和更多设置

> 原文：<https://dev.to/horia141/retrofeed-work-log-3---web-serving--more-setup-4cn5>

在本系列的第二部分中，我们看了我们可以做的最基本的事情——包设置、一些基本脚本、公共集成等等。我们将在第三篇文章中介绍如何实际设置一个真正的应用程序，它可以做一些最简单的事情，下一篇文章将介绍基础设施和公开项目。事实证明，这里也有很多内容需要讨论，一篇文章无法公正地描述它。因此，我在这里将只讨论初始的应用程序行为、一些测试和更多的集成，我们将在下一部分推迟像会话和认证这样的事情。

当前文件结构为:

```
.
├── .dockerignore
├── .gitignore
├── .nvmrc
├── .travis.yml
├── Dockerfile
├── LICENSE.txt
├── README.md
├── config
│ ├── env.local
│ └── env.test
├── cypress.json
├── docker-compose.yml
├── knexfile.js
├── migrations
│ └── 0001.create-auth-schema.js
├── package.json
├── scripts
│ ├── lint.sh
│ ├── migrate.sh
│ ├── ngrok-expose.sh
│ ├── send-coverage.sh
│ ├── serve-watch.sh
│ ├── serve.sh
│ ├── setup-env.sh
│ ├── test-e2e-watch.sh
│ ├── test-e2e.sh
│ ├── test-watch.sh
│ └── test.sh
├── src
│ ├── app-controller.ts
│ ├── app-module.ts
│ ├── app-service.ts
│ ├── common.ts
│ ├── config.ts
│ └── index.ts
├── test
│ ├── app-controller-test.ts
│ ├── app-service-test.ts
│ ├── common-test.ts
│ └── config-test.ts
├── test-e2e
│ └── index.ts
├── tsconfig.e2e.json
├── tsconfig.json
├── tslint.json
├── tsnyc.json
└── yarn.lock 
```

这比上次的那个大了一堆。新增加的是`test-e2e`目录、`migrations`目录，以及更多的源文件和脚本文件。

最重要的事情是建立 NestJs。从广义上讲，这是一个秉承 Ruby On Rails 或 Django 精神的 web 框架，但也从 Angular 吸取了很多东西。所以更多的是“包括电池”而不是“做自己的事”，像 Express 就是。虽然它仍然在引擎盖下使用快递。它都是在 TypeScript 中构建的，所以它有很好的类型和对注释的强烈支持。它也有一些合理的默认设置，但是它并没有要求特定的 ORM 或视图引擎。事实上，它实施的最大的东西是它自己的依赖注入框架。

`index.ts`文件变成了:

```
import { Module } from "@nestjs/common";
import { NestFactory } from "@nestjs/core";

import { AppModule } from "./app-module";
import { ConfigModule, ConfigService } from "./config";

@Module({
    imports: [
        AppModule,
        ConfigModule,
    ],
})
class MainModule {}

async function bootstrap() {
    const app = await NestFactory.create(MainModule);
    const config = app.get(ConfigService);
    await app.listen(config.port);
}

bootstrap(); 
```

它的目的只是启动服务器，并通过`MainModule`进行“中央”配置。在这里，我们将列出应用程序的所有其他模块，并设置中间件、日志、错误处理等。我们需要这么做。不过现在还很简单。

为了让*真正做*一些事情，你需要运行`yarn serve`或者`./scripts/serve.sh`(或者，为了开发`yarn serve:watch`)。这本质上就是`ts-node src/index.ts`。这将启动服务过程，之后你可以开始向服务器发送请求`curl localhost:10001`将显示一条`Hello World`类型的消息。现在只有索引路径，但是我们在下一篇文章中还需要更多。

需要注意的是`ConfigModule`，它是一个`Config`对象的提供者。在 [config.ts](https://github.com/horia141/retrofeed/blob/v0.0.3/src/config.ts) 中检查一下，因为放在这个帖子里面有点大。最佳实践是将应用程序的配置分离到一个单独的文件中，并为不同的环境提供不同的值。 [12 因子应用](https://12factor.net/)建议用环境变量举例。我们将使用`./config`目录中的`env.$ENV`文件来保存配置信息。目前只有`local`和`test`(即 Travis)环境的值，但是`staging`和`live`将在我们到达那里后出现。

文件看起来像:

```
ENV=TEST
HOST=retrofeed.retrofeed
PORT=10001
INSPECT_PORT=9229
POSTGRES_HOST=postgres.retrofeed
POSTGRES_PORT=5432
POSTGRES_DATABASE=retrofeed
POSTGRES_USERNAME=retrofeed
POSTGRES_PASSWORD=retrofeed 
```

`Config`负责将它翻译成应用程序可以使用的东西，比如`config.port`或`config.env`。它还做了一些“解析”和检查，尽管我对它是如何完成的不是 100%满意。也许雷诺也能帮上忙。它的使用方式是，需要一些配置值的类将在构造函数中注入`Config`，Nest 将负责提供这些值。使测试变得更加容易，也清楚地标记了代码的依赖性。

现在在`./scripts`中，一个特殊的`setup-env.sh`脚本在其他脚本开始时被调用。它的目的是在项目的根目录下写一个`.env`文件，该文件链接到环境特定版本的配置文件。这是供第三方工具使用的，*希望*该文件存在于某个地方，或者至少在不同的环境中是相同的(看着你`docker compose`)。

正如在第一篇文章中提到的，我们将使用 [knex.js](https://knexjs.org/) 作为轻量级 ORM。 [`knexfile.js`](https://github.com/horia141/retrofeed/blob/v0.0.3/knexfile.js) 作为这个东西的全局配置，应用程序代码和迁移工具都可以访问，它依赖于环境中可见的`.env`文件的内容。目前还没有任何应用程序代码，但是移植已经差不多准备好了。`./migrations`目录以 Knex 的方式将它们保存为`.js`文件。单次迁移如下所示:

```
exports.up = (knex, Promise) => knex.schema.raw(
    'CREATE SCHEMA auth'
);

exports.down = (knex, Promise) => knex.schema.raw(
    'DROP SCHEMA IF EXISTS auth'
); 
```

这里，`up`被执行以应用更改，而`down`被执行以防止`up`出错，我们需要恢复更改。更改本身通过`yarn migrate`或`./scripts/migrate.sh`应用。它们所应用的数据库再次通过`.env`和`POSTGRES_*`变量进行配置。`docker-compose up postgres`足以启动一个实例进行本地测试，而 Travis 提供的实例用于测试。

说到测试，它现在要复杂得多。在`./tests`下还有一堆测试要进行。如前所述，我们使用了一个标准的设置[摩卡](https://mochajs.org/)、[柴](http://www.chaijs.com/)和[西农](http://sinonjs.org/)。强调所有这些东西的用法以及测试 NestJs 的控制器(甚至作为单元测试)的一个例子是 [`./tests/app-controller-test.ts`](https://github.com/horia141/retrofeed/blob/v0.0.3/test/app-controller-test.ts) 。我在这里转载了一部分:

```
// Bunch of imports
describe("AppController", () => {
    let module: TestingModule;

    beforeEach(async () => {
        module = await Test.createTestingModule({
            imports: [AppModule, ConfigTestingModule],
        }).compile();
    });

    describe("root", () => {
        it("should work", async () => {
            const appService = module.get<AppService>(AppService);
            const appController = module.get<AppController>(AppController);

            const fake = sinon.fake.returns("Hello World - TEST");
            sinon.replace(appService, "root", fake);

            expect(await appController.root()).to.be.eql("Hello World - TEST");
        });
    });
}); 
```

此外，我还通过 [Cypress.io](https://dashboard.cypress.io/#/projects/a4ib2i/runs) 设置了端到端测试。测试代码驻留在`./test-e2e`下，可以通过`yarn test:e2e`或`./scripts/test-e2e.sh`运行。它有一点不同的设置，因为 Cypress 没有与 TypeScript 很好地集成(至少在我的经验中)。所以有一个`tsconfig.e2e.json`供它使用，还有一个显式的构建步骤，它获取源文件`.ts`并生成 Cypress runner 可以在`.build`中使用的东西。

不过在本地运行这些测试的好方法是通过`yarn test:e2e:watch`，它启动了 Cypress 应用程序——一种小规模的浏览器，在这里你可以与应用程序进行许多强大的交互。

Travis 上的运行也向 Cypress 发送测试的捕获，你可以在这里查看它们。下面截图:

[![Retrofeed 4.1](img/fe27a08822e060d62f63ed6a2efee48a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2jw-_upp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./assets/retrofeed-4-1.png)

最后，一个非常有用的大规模手工测试工具是 [ngrok](https://ngrok.com/) 。使用`yarn ngrok`启动服务，并按照它的指示临时在线发布应用程序。

这部分到此为止。正如承诺的那样，我们对问题的实质进行了更深入的探讨，尽管还有很多问题需要解决。我们将在下一部分看到如何设置会话支持、公共中间件和 Auth0+GitHub 集成。

点击查看系列[中的所有文章。](//https//horia141.com/retrofeed.html)