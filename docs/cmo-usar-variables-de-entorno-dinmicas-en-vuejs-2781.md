# 在 vuejs 中使用动态环境变量

> 原文：<https://dev.to/djdany01/cmo-usar-variables-de-entorno-dinmicas-en-vuejs-2781>

[*英文读作*](https://dev.to/djdany01/how-to-use-dynamic-environment-variables-in-vuejs-4l3e)

想象一下，您需要制作一个依赖 API 收集数据的应用程序，我们的开发分为三个部署阶段:

*   试验
*   偏差
*   刺

我们希望实现的目标是，根据我们所处的阶段，应用程序可能会调用相应的服务，而我们不希望每次 URL 更改或阶段更改时都部署应用程序。
我们要做的是在我们的静态文件夹中创建 json 文件，示例的结构如下:

###### /静态/配置. json

示例文件将包含默认使用的 URL:

```
{  "URL":  "https://pordefecto.algo"  } 
```

现在，在我们使用 url 的 este 应用程序中，我们将在 created()函数中声明对文件的虚假 GET 调用，在此示例中，我们将使用“*axios”*:

```
created: function() {
    axios.get("static/config.json").then(response => {
      this.URL = response.data.URL;
    });
  } 
```

这样，我们的应用程序将动态检索该 url，以后可以对其进行修改，而无需重新构建。

现在，我们需要为每个阶段定义一个文件，在本例中为 3 个阶段，即 environments 文件夹中的 3 个文件(加上我们之前在“*static*”中定义的默认文件)，其结构如下所示:

###### /环境/测试/配置. json

###### /环境/开发/配置. json

###### /环境/pro/config.json

因此，我们已经有了每个阶段的 3 个文件(加上默认值)，应用程序将动态调用该文件来获取 URL，现在我们必须确保，当部署应用程序时，[NPM run build]t1]也将 environments 文件夹复制到目标位置。我们可以通过多种方式自动执行此操作(copywebpackplugin，npm run script-我...。)或我们手动复制相应的文件夹，例如，我将使用 web pack copy web package plugin 插件，该插件允许我们通过将以下内容添加到我们的配置文件中来复制文件，在这种情况下，*/build/web pack . base . conf . js*(根据您的配置进行更改):

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

最后，我们构建后的结构将如下所示:

###### /dist/index.html

###### /dist/static/config . JSON

###### /dist/environments/test/config . JSON

###### /dist/environments/dev/config . JSON

###### /dist/environments/pro/config . JSON

###### /距离/静态/js/...

现在，每次部署我们的应用程序时，我们都会确保用相应阶段的应用程序替换‘t0//static/config . JSON’，这样我们就可以手动替换它，或者，如果我们有像 Jenkins 这样的 CI *(持续集成)*过程，我们可以将它配置为 copie

### 感谢整个 dev.to 社区！