# 打字稿和豆茎

> 原文：<https://dev.to/errietta/typescript-and-the-beanstalk-4fbj>

在我们开始之前，请注意这篇文章假设您已经有了 CircleCI/Beanstalk 集成。这是因为设置它本身是一个非常冗长的过程。我可能会制作一个关于它的视频，但同时会有一个非常好的[媒体帖子](https://medium.com/engineering-on-the-incline/continuous-integration-with-circleci-2-0-github-and-elastic-beanstalk-cf1a0b7f05c6)解释如何设置它。

此外，非常感谢 [Rokt33r](https://github.com/Rokt33r/aws-eb-typescript-app) 提供的示例回购，我无耻地复制了所有内容:)

### 为什么这个差别这么大？

因此，您可能已经看到了将节点应用程序部署到 Beanstalk 的例子，甚至让它们工作，但是现在您想要部署一个 typescript 应用程序。您可能想知道如何发布您构建的文件，因为不像普通的 node.js，仅仅发布您的源代码不足以让它运行。

因此，要记住的关键事情是，您部署的是您的*构建的*资产，而不是您的源代码。即使是普通的节点应用程序也是如此，但在这种情况下，构建和源代码是相同的。

那么，你是怎么做到的呢？好吧，假设你已经完成了设置 AWS、Beanstalk 和 CircleCI 的~~不可能的~~困难部分，剩下的实际上相当容易。

### 第一步:tsconfig.json

您应该已经设置好来编译您的代码。如果你已经有了，你不需要改变它，只要记下`outDir`是什么，因为你会需要它。如果你没有，或者想看例子，这里有一个。

```
{
    "compilerOptions": {
        "target": "es6",
        "module": "commonjs",
        "outDir": "dist",
        "sourceMap": true,
        "baseUrl": ".",
        "paths": {
            "*": [
                "node_modules/*",
                "src/types/*"
            ]
        }
    },
    "include": [
        "src/**/*"
    ],
    "exclude": [
        "node_modules"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:package.json

你应该已经有了`start`和`build`脚本，但如果没有，这里有一个例子:

```
 "scripts": {
    "build": "tsc",
    "start": "node dist/index.js"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

`build`是用来构建你的软件的命令；在这种情况下，需要`tsc`将类型脚本编译成 javascript。

`start`是你如何启动你的软件，显然这取决于你的应用，但它通常类似于`node dist/index.js`。注意，您指向的是编译后的 js 代码。地点可能不是`dist`；但它是你在`tsconfig.json`中设置为`outDir`的任何东西。

### 步骤三:`dist`步骤

如果你看过 Elastic Beanstalk for AWS 的 UI，你基本上是通过上传`.zip`文件来部署应用程序的。如果您使用命令行(`eb deploy`)，它会为您做到这一点。我们本质上想要做的是构建你的`dist`属性`.zip`文件来上传。怎么会？基本上通过压缩你的`dist`目录。

做一个名为`scripts/dist.sh`的文件，把下面的代码放进去:

```
# If the directory, `dist`, doesn't exist, create `dist`
stat dist || mkdir dist
# Archive artifacts
zip dist/$npm_package_name.zip -r dist package.json package-lock.json 
```

Enter fullscreen mode Exit fullscreen mode

如果您没有使用`dist`作为您的构建的目录名(还记得前面的`outDir`),您需要在上面更改它。

这将基本上为你建立你的拉链！
您可以手动测试:

```
errietta@Moltres [4] (git)-[master] ~/hyperbudget-backend % bash scripts/dist.sh
  File: 'dist'
  Size: 4096            Blocks: 8          IO Block: 4096   directory
Device: 801h/2049d      Inode: 16393011    Links: 6
Access: (0775/drwxrwxr-x)  Uid: ( 1000/errietta)   Gid: ( 1000/errietta)
Access: 2018-06-16 11:41:27.842696048 +0100
Modify: 2018-05-24 19:59:55.741014000 +0100
Change: 2018-05-24 19:59:55.741014000 +0100
 Birth: -
  adding: dist/ (stored 0%)
  adding: dist/app.js (deflated 70%)
  ....
  adding: dist/index.js (deflated 39%)
  adding: dist/index.js.map (deflated 58%)
  adding: package.json (deflated 65%)
  adding: package-lock.json (deflated 77%)
errietta@Moltres [4] (git)-[master] ~/hyperbudget-backend % 
```

Enter fullscreen mode Exit fullscreen mode

这实际上会生成`dist/.zip`——那是因为手动运行的时候，`$npm_package_name`没有设置。请随意查看该 zip 文件，并验证您构建的所有 javascript 都在其中。如果它是正确的，删除该文件，我们将告诉 npm 在下一步生成它！

### 步骤 4:设置 npm 分发脚本

非常简单，只需在您的`package.json`中添加另一个脚本。比如:

```
 "scripts": {
    "build": "tsc",
    "dist": "sh ./scripts/dist.sh",
    "start": "node dist/index.js"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以通过做`npm run dist`来跑你的`dist`步了！如果你运行它，现在你的`dist`文件夹中会有一个 zip 文件，它实际上是以你的应用程序命名的，你可以打开它，再次验证你已经编译好了所有的 javascript。

### 第五步:修改 CircleCI 配置

现在我们需要告诉 CircleCI 运行`npm run build`和`npm run dist`。这将取决于你的配置，但基本上编辑`.circleci/config.yml`，并确保`npm run build`和`npm run dist`在`eb deploy`之前运行。比如我有以下:

```
 - deploy:
          name: Deploy to Elastic Beanstalk
          command: |
            npm install && npm run build && npm run dist && eb deploy --staged MyApp-env 
```

Enter fullscreen mode Exit fullscreen mode

这将安装我所有的依赖项，将我的应用程序编译成 javascript，运行`npm run dist`生成 zip 文件，并部署到 Beanstalk。等等，我们如何告诉 Beanstalk 使用这个 zip 文件呢？

### 第六步:豆茎配置

创建或修改您的`.elasticbeanstalk/config.yml`文件。在那里，您需要添加以下内容:

```
deploy:
  artifact: dist/YOUR-APP-NAME.zip 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经设置了使用`$npm_package_name`的`dist.sh`脚本，那么 zip 的名称应该与您的 npm 包名称相同。你总是可以手动运行`npm run dist`,然后看到它生成的文件名。

如果你没有一个`elasticbeanstalk/config.yml`文件，这里有一个简单的适合我的:

```
branch-defaults:
  master:
    environment: MyApp-env
  staging:
    environment: MyApp-env
  dev:
    environment: MyApp-env
global:
  application_name: my-app
  default_platform: 64bit Amazon Linux 2017.03 v4.5 running Node 8.10
  default_region: eu-central-1
deploy:
  artifact: dist/my-app.zip 
```

Enter fullscreen mode Exit fullscreen mode

您应该更改您的分支名称和环境名称，以分别匹配您部署的分支和您的 Beanstalk 环境名称，并且如果您愿意，您可以更改区域和平台。重要的是`artifact`是正确的，这将告诉 beanstalk 部署您的 zip 文件

### 就是这样！

如果设置正确，circleci 现在应该会将您的 zip 文件部署到 beanstalk。您可以检查 CircleCI 构建信息，并验证它是否运行了`tsc`，压缩您的文件，并部署到 Beanstalk。下面是我的比较:

```
#!/bin/bash -eo pipefail
npm install && npm run build && npm run dist && eb deploy --staged MyApp-env

> ursa@0.9.4 install /home/circleci/app/node_modules/ursa
> node-gyp rebuild

make: Entering directory '/home/circleci/app/node_modules/ursa/build'
  CXX(target) Release/obj.target/ursaNative/src/ursaNative.o
  SOLINK_MODULE(target) Release/obj.target/ursaNative.node
  COPY Release/ursaNative.node
make: Leaving directory '/home/circleci/app/node_modules/ursa/build'
added 232 packages in 11.433s

> my-app@1.0.0 build /home/circleci/app
> tsc

> my-app@1.0.0 dist /home/circleci/app
> sh ./scripts/dist.sh

  File: ‘dist’
  Size: 4096        Blocks: 8          IO Block: 4096   directory
Device: 100016h/1048598d    Inode: 7272        Links: 4
Access: (0755/drwxr-xr-x)  Uid: ( 3434/circleci)   Gid: ( 3434/circleci)
Access: 2018-06-14 13:37:17.348967660 +0000
Modify: 2018-06-14 13:37:17.376967104 +0000
Change: 2018-06-14 13:37:17.376967104 +0000
 Birth: -
  adding: dist/ (stored 0%)
  adding: dist/App.js (deflated 51%)
  adding: dist/index.js (deflated 39%)
  adding: dist/script/ (stored 0%)
  adding: dist/App.js.map (deflated 64%)
  adding: package.json (deflated 56%)
  adding: package-lock.json (deflated 77%)
Uploading my-app/app-******.zip to S3\. This may take a while.
Upload Complete.

INFO: Environment update is starting.
INFO: Deploying new version to instance(s).
INFO: New application version was deployed to running EC2 instances.
INFO: Environment update completed successfully. 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到它运行了`npm install`、`tsc`，然后我的`./scripts/dist.sh`压缩了我所有构建的文件，然后成功部署到 EC2。

希望这对别人有帮助，感谢阅读:)