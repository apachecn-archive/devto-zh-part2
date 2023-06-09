# 用 Node.js，哈比神和 Vue 做培根

> 原文：<https://dev.to/reverentgeek/makin-bacon-with-nodejs-hapi-and-vue-19d7>

[![Makin' Bacon](img/818eb63ae3bb46db880b74940832152a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lR3siD6P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8aa599e033a83lddv685.png)

我最近为我在几次会议上发表的 Node.js 速成讲座制作了一个新的演示。这个应用程序是一个使用 [Node.js](https://nodejs.org/) 、[哈比神](https://hapijs.com/)和 [Vue](https://vuejs.org/) 的“bacon ipsum”生成器。我知道我不是第一个想到“bacon ipsum”生成器的人，但是我认为在 Node.js 中创建一个这样的生成器会是一个有趣的项目。

API 要求:

*   使用培根名字和其他相关单词的列表...
*   返回指定数量的段落，最多 25 个
*   段落应该由 3 到 6 个句子组成
*   每个句子都应该大写
*   一个句子应该以随机的标点符号结尾，更多地使用句号
*   一个句子应该由 4 到 12 个不同的单词组成

用户界面要求:

*   顾客可以选择生产 1 到 21 磅的熏肉
*   客户可以轻松地将生成的文本复制到他们的剪贴板上

[看看它的实际效果！](https://node-bacon-generator.herokuapp.com/)

当你点击“做熏肉！”按钮，Vue 应用程序使用 [Axios](https://www.npmjs.com/package/axios) 来调用 bacon 的 API。当 API 调用返回时，Vue 应用程序用段落数组更新其状态。这将触发重新呈现 UI 以列出段落并显示“复制到剪贴板！”按钮。

```
makeTheBacon: function() {
  return axios
    .get( "/api/bacon/" + this.numberOfPounds )
    .then( res => ( this.paragraphs = res.data.paragraphs ) )
}, 
```

Enter fullscreen mode Exit fullscreen mode

### Vue 计算属性

Vue 应用程序使用一个计算属性`hazBacon`来显示/隐藏“复制到剪贴板！”按钮基于要显示的培根文本的任何段落。

将文本复制到剪贴板时，Vue 应用程序使用另一个计算属性`paragraphText`，将段落数组连接成一个字符串。

```
computed: {
  paragraphText: function() {
    return this.paragraphs.join( "\n\n" );
  },
  hazBacon: function() {
    return this.paragraphs.length > 0;
  },
  poundText: function() {
    return this.numberOfPounds == 1 ? "pound" : "pounds";
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

### 其他依赖关系

除了 Node.js、哈比神和 Vue 之外，下面是项目使用的依赖项和插件。其中一些是我当前构建 Node.js 应用程序的最爱。

*   [Vue-Clipboard2](https://www.npmjs.com/package/vue-clipboard2) -用于将文本复制到剪贴板的 Vue 组件
*   [Axios](https://www.npmjs.com/package/axios) -浏览器或 Node.js 的 HTTP 客户端
*   [fs-extra](https://www.npmjs.com/package/fs-extra) -基于承诺的文件系统模块
*   用于哈比神的对象模式验证插件
*   林挺
*   Nodemon -开发者工具，当源代码发生任何变化时，它会自动重启应用程序
*   [Boom](https://www.npmjs.com/package/boom) -返回错误的哈比神插件
*   hapi-pino -哈比神的 pino 日志插件
*   [惰性](https://www.npmjs.com/package/inert) -哈比神的静态资源插件
*   [实验室](https://www.npmjs.com/package/lab)和[代码](https://www.npmjs.com/package/code) -哈比神的测试和断言实用程序

[![](img/4a882d0a02e417c200b5a4eb2e1aaffa.png)T2】](https://github.com/reverentgeek/node-bacon-generator)

[github.com/reverentgeek/node-bacon-generator](https://github.com/reverentgeek/node-bacon-generator)

### 电脑化快乐！