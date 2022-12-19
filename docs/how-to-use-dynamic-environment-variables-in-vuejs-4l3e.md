# 如何在 VueJS 中使用动态环境变量

> 原文：<https://dev.to/djdany01/how-to-use-dynamic-environment-variables-in-vuejs-4l3e>

[*读西班牙文*](https://dev.to/djdany01/cmo-usar-variables-de-entorno-dinmicas-en-vuejs-59i6-temp-slug-502777?preview=803e036e9d1b1413804ac2b18fd25d9d4768517e2afc36eab840ef0ced8d798283dc1eea92a6b55b23fd750f08b0e2417930faa6a2d4637e7b7c4df9)

### 这是我在 DEV 上的第一篇文章。社区和第一个英语，所以我会尽我最大的努力:D

假设您需要一个基于 API 的应用程序来获取数据，我们有 3 个不同的开发阶段:

*   试验
*   偏差
*   刺

我们想要的是，根据我们所处的阶段，应用程序可以调用正确的 API，而不希望每次在阶段之间或更改 URL 时都部署我们的应用程序。
我们要做的是在我们的 statics 文件夹中创建一个新文件，这个示例结构是:

##### /静态/配置. json

默认情况下，示例文件将使用 URL:

```
{  "URL":  "https://bydefault.something"  } 
```

现在，在我们使用 URL 的 Vue 应用程序中，在 created()函数中声明一个对该文件的错误 API GET 调用，在本例中，我们使用 *axios* :

```
created: function() {
    axios.get("static/config.json").then(response => {
      this.URL = response.data.URL;
    });
  } 
```

现在，我们的应用程序将动态获取该 URL，我们可以在以后更改它，而不需要部署它。

现在，我们必须为我们需要的每个阶段创建一个文件，在本例中，我们有 3 个阶段，因此我们需要在环境目录中创建 3 个文件(加上之前创建的默认文件)，示例结构如下:

##### /环境/测试/配置. json

##### /环境/开发/配置. json

##### /环境/pro/config.json

因此，我们每个阶段都有 3 个文件(加上默认文件)，应用程序可以动态调用文件来获取 URL，现在我们必须确保在部署应用程序 *(npm 运行构建)*时将环境文件夹复制到相同的目标位置。我们可以通过多种自动形式(CopyWebpackPlugin，npm run script...)或者我们是否可以通过将文件夹复制到目标位置来手动完成，在本例中，我们将使用 CopyWebpackPlugin 插件，通过将以下内容添加到我们的配置文件中来自动复制文件，在本例中是*/build/web pack . base . conf . js*(根据您的配置进行更改):

```
output: {
    path: path.resolve(__dirname, '../dist')
  },
  plugins: [
    new CopyWebpackPlugin([
      {
        from: path.resolve(__dirname, '../static/js'),
        to: path.resolve(__dirname, '../dist/js')
      },
      {
        from: path.resolve(__dirname, '../static/config.json'),
        to: path.resolve(__dirname, '../dist/static')
      },
      {
        from: path.resolve(__dirname, '../config/environments/'),
        to: path.resolve(__dirname, '../dist')
      }
    ])] 
```

最后，部署后我们的结构将是:

##### /dist/index.html

##### /dist/static/config . JSON

##### /dist/environments/test/config . JSON

##### /dist/environments/dev/config . JSON

##### /dist/environments/pro/config . JSON

##### /距离/静态/js/...

现在，每次部署我们的应用程序时，我们都必须确保用正确的环境文件替换 */static/config.json* ，我们可以手动这样做，或者如果我们有任何 CI *(持续集成)*流程，如 Jenkins，我们可以将其配置为根据部署阶段自动复制正确的文件。

### 感谢整个 dev.to 社区！