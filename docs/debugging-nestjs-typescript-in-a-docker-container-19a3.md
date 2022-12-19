# 在 Docker 容器中调试 Nest.js (TypeScript)

> 原文：<https://dev.to/_rema_lp/debugging-nestjs-typescript-in-a-docker-container-19a3>

# 在 Docker 容器中调试 Nest.js

这个菜谱展示了如何在 Docker 容器中运行和调试 VS 代码 [Nest.js](https://nestjs.com/) 、全栈类型脚本框架、项目。

该菜谱假设您安装了最新版本的 Docker。

这里是[仓库](https://github.com/rema424/nestjs-docker-vscode-debugging)。

## 第一步。创建新应用程序

使用以下命令，为应用程序创建目录(`nest-js-app`)和文件。

```
$ mkdir nest-js-app && cd nest-js-app
$ touch Dockerfile docker-compose.yml .dockerignore .gitignore 
```

应用程序目录结构应该是:

```
- nest-js-app
    - .dockerignore
    - .gitignore
    - docker-compose.yml
    - Dockerfile 
```

将以下内容添加到`Dockerfile`文件中:

```
FROM node:8.10.0

RUN mkdir -p /nest
ADD . /nest
WORKDIR /nest

RUN npm i -g @nestjs/cli 
```

将以下内容添加到`docker-compose.yml`文件中:

```
version: "3"

services:
  nest:
    build: .
    volumes:
      - .:/nest
    ports:
      - 3000:3000
      - 9229:9229
    tty: true 
```

将以下内容添加到`.gitignore`文件中:

```
# Logs
logs
*.log
npm-debug.log*

# Runtime data
pids
*.pid
*.seed
*.pid.lock

# Coverage directory used by tools like istanbul
coverage

# Dependency directories
node_modules/

# Output files
dist

# dotenv environment variables file
.env 
```

将以下内容添加到`.dockerignore`文件中:

```
.git
.github
.vscode
coverage
docker-compose.yml
README.md

# Node Files #
node_modules
npm-debug.log
npm-debug.log.* 
```

构建 docker 映像:

```
$ docker-compose build 
```

启动并登录到容器:

```
$ docker-compose up -d
$ docker-compose exec nest bash 
```

用`Nest CLI`搭建基础项目并安装依赖项:

```
# nest new .
# npm install 
```

运行应用程序:

```
# npm start 
```

打开浏览器并导航至`http://localhost:3000`。
你应该会看到`Hello world!`消息。

## 第二步。设置调试环境

使用以下命令，创建用于调试的目录(`.vscode`)和文件。

```
# mkdir .vscode && touch .vscode/launch.json nodemon-docker-debug.json 
```

应用程序目录结构应该是:

[![スクリーンショット 2018-11-11 5.03.17.png](../Images/ed960c6697d048a2f5886e3e5a359006.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kaHJjUbR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/237863/a59c4adc-645e-2cdd-1abd-c95a7e00fe68.png)

将以下内容添加到`launch.json`文件中:

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Docker: Attach to Node",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "address": "localhost",
      "localRoot": "${workspaceFolder}",
      "remoteRoot": "/nest",
      "protocol": "inspector",
      "restart": true
    }
  ]
} 
```

将以下内容添加到`nodemon-docker-debug.json`文件中:

```
{
  "watch": ["src"],
  "ext": "ts",
  "ignore": ["src/**/*.spec.ts"],
  "exec": "tsc && node --inspect=0.0.0.0 ./dist/main.js"
} 
```

将下面一行添加到`package.json`文件的脚本块中:

```
"scripts": {
  "debug": "nodemon -L --config nodemon-docker-debug.json",

} 
```

## 第三步。排除故障

对于测试操作，在`src/app.service.ts`中添加一些代码，并设置如下断点。

[![スクリーンショット 2018-11-11 4.39.50.png](../Images/dff3e81ef626c1380f3e296e81ddf212.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yjg7gjBC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/237863/a70ddee3-164a-677f-6586-3990e6de6e14.png)

在调试模式下运行应用程序:

```
# npm run debug 
```

[![スクリーンショット 2018-11-11 11.52.41.png](../Images/579b34e09b963f26f64b58181784908a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U-DHqvo4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/237863/931c42c5-26a8-2952-4eec-33087b543bae.png)

开始对 VScode 进行调试。

[![スクリーンショット 2018-11-11 4.44.01.png](../Images/353791e2a14ee6ae23ffaf6b0632cdff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sbjjWZb2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/237863/f32fc1ac-8ac5-f301-b4fb-feb01be524a4.png)

打开浏览器并导航至`http://localhost:3000`。
你应该看到程序在断点处停止。

[![スクリーンショット 2018-11-11 4.52.37.png](../Images/41565848e11c6f8d49439736ea7cc9fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2JFM3XE1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/237863/2d02ff7c-ef44-cc32-b204-232c388531b1.png)

编码快乐！

# 参考

[调试 Docker 容器中的 TypeScript](https://github.com/Microsoft/vscode-recipes/tree/master/Docker-TypeScript)
[nodemon](https://github.com/remy/nodemon#nodemon)
[有没有办法使用 npm 脚本运行 TSC-watch&&nodemon-watch？](https://stackoverflow.com/questions/38276862/is-there-a-way-to-use-npm-scripts-to-run-tsc-watch-nodemon-watch/38695935#38695935)