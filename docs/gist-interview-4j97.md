# 要点面试-编程问题模拟器

> 原文：<https://dev.to/mariorodeghiero/gist-interview-4j97>

# < / >要诀面试

Gist Interview 是一个用 ReactJS 构建的应用程序，任何人或公司都可以使用它来模拟编程问题，只需创建“db.json”文件并填充其 Gist-id，最后生成一个仪表板。

### Gif 示例

[![Gif Example](img/3423d4d97d0a0a192bcd39205fe890fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fd0NzNzA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rayj0viuigg54g4jbgwm.gif)

## 建有

*   [反应堆](https://reactjs.org)
*   [反应-渲染-要点](https://github.com/marjoballabani/react-render-gist#readme)
*   [json-server](https://github.com/typicode/json-server)

### 安装

首先，通过 git 克隆回购:

```
git clone https://github.com/mariorodeghiero/react-gist-interview.git your-project-name 
```

Enter fullscreen mode Exit fullscreen mode

然后用 yarn 安装依赖项。

```
$ cd your-project-name
$ yarn 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:如果不能使用[纱](https://github.com/yarnpkg/yarn)，运行`npm install`。

## 运行

安装 json-server:

```
$ npm install -g json-server 
```

Enter fullscreen mode Exit fullscreen mode

启动文件“db . JSON”:

```
$ json-server --watch db.json --port 3001 
```

Enter fullscreen mode Exit fullscreen mode

示例文件【db . JSON】
[![Db](img/145cea7faa94bbf6aa4c06e620953bdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---lJiX6T6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/74rxh2cq74srn7qzf4gh.png)

在当前目录下打开一个新的标签页，在`dev`环境下启动 app:

```
$ npm run start 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$ yarn start 
```

Enter fullscreen mode Exit fullscreen mode

运行后，浏览器在[自动打开窗口 http://localhost:3000/](http://localhost:3000/)

## 部署

运行下面的命令进行部署，最小化所有文件。

```
$ npm run build 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$ yarn build 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我创建这个应用程序是为了测试和分享在 ReactJS 研究中获得的知识，也是为了让任何人或公司都可以使用它来模拟关于编程的问题，只需简单地创建“db.json”文件并用相应的 Gist-ID 填充它。
如果你喜欢这个项目，贡献改进或给我一个星⭐️在 [GitHub。](https://github.com/mariorodeghiero/react-gist-interview.git)

我希望你喜欢✌️
谢谢！

## 图标由

*   [Freepik](http://www.freepik.com)
*   [Smashicons](https://www.flaticon.com/authors/smashicons)
*   [载体市场](https://www.flaticon.com/authors/vectors-market)