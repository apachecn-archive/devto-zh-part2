# 结构化 Node.js 项目

> 原文：<https://dev.to/vukhanhtruong/structuring-nodejs-project-204e>

如果您正在寻找基于构建 RESTful API 的最佳实践的 NodeJS 项目结构。可能对你们有帮助。

该存储库中遵循的一些良好实践:

*   代码风格实践通过[节点最佳实践](https://github.com/i0natan/nodebestpractices)
*   ES6 支持。
*   摩根·洛格。
*   错误处理。
*   通过 [apidocjs](http://apidocjs.com/) 实现的开放 API 规范。
*   JWT 认证。
*   Joi & Express 验证。
*   通过`.env`文件的环境变量。
*   更漂亮的林挺。
*   安全(头盔，CORS，快递蛮)。
*   Husky 作为林挺的 git 钩子&在提交前运行单元测试。
*   用 Jest 测试单元和 E2E。
*   [背包](https://github.com/jaredpalmer/backpack)建造系统

## 先决条件

1.  NodeJS
2.  节点包管理(`yarn`或`npm`)
3.  码头工人

## 安装

1.  克隆项目`git clone git@github.com:vukhanhtruong/nodejs-api-boilerplate.git`。
2.  安装依赖关系`yarn install`或`npm i`
3.  将`.env.example`复制到`.env`文件。

## 运行 Docker

`docker-compose up -d`。

## 渡鸦日志

在[岗哨](https://sentry.io/)创建账户，然后把你的 url 放到变量`SENTRY_DSN`的`.env`文件中。

## Api 文档

Api doc his 主持的[浪涌](https://surge.sh/)。要更改 url 并拥有自己的文档，只需修改`.env`文件中的链接。运行以下命令发布您的文档。

```
# Generate document
yarn doc
# Deploy document to surge.sh
yarn doc:deploy

# or
npm run doc
npm run doc:deploy 
```

## 预提交挂钩

在提交前使用`husky`林挺您的代码&在推送前运行单元测试。

## 脚本

### 开发

```
yarn dev

# or

npm run dev 
```

### 调试

使用 VSCode 进行调试。参见 [VSCode 自动附加](https://code.visualstudio.com/updates/v1_22#_node-debugging)

```
yarn debug

# or

npm run debug 
```

### 测试

```
yarn test

# or

npm run test 
```

**注意:**如果您对 ENOSPC 有疑问，请运行以下命令以避免:

```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p 
```

#### 测试手表

```
yarn test:watch 
```

### 覆盖范围

```
# run jest coverage
yarn test:cover

# show html report
yarn test:cover:open

# or

npm run test:cover

npm run test:cover:open 
```

### JSDOC

```
# generate documentation
yarn doc

# Publish documentation to surge.sh
yarn doc:deploy

#or

npm run doc

npm run doc:deploy 
```

点击了解更多

编码快乐！