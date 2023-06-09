# 如何使用 Webpack 将您的前端资产压缩并部署到亚马逊 S3

> 原文：<https://dev.to/wiaio/how-to-gzip-and-deploy-your-front-end-assets-to-amazon-s3-with-webpack-314p>

Gzipping 你的前端资产是一个很好的方式来大幅减少他们的文件大小，从而改善整体页面加载时间。如果您使用亚马逊 S3 来托管这些资产，这里有一个简单的方法来将 gzipping 和前端部署添加到您的生产构建步骤中。

如果你还没有建立一个基本的 Webpack 环境，请按照本教程进行操作。

本教程需要两个 Webpack 插件:

[压缩插件](https://github.com/webpack-contrib/compression-webpack-plugin)
和
[S3 插件](https://github.com/webpack-contrib/s3-plugin-webpack)

像这样安装插件:

`npm install --save-dev compression-webpack-plugin webpack-s3-plugin`

导入 Webpack 配置文件顶部的插件:

`const CompressionPlugin = require('compression-webpack-plugin');
const S3Plugin = require('webpack-s3-plugin');`

接下来，我们将初始化压缩插件。在下面的例子中，插件将应用于所有的 Javascript 和 CSS 文件。新的 gzipped 文件创建在相同的目录中，后缀为`.gz`，原始文件被删除。点击[这里](https://github.com/webpack-contrib/compression-webpack-plugin)获得更多插件选项。

```
module.exports = {
  ...,
  plugins: [
    new CompressionPlugin({
      test: /\.(js|css)$/,
      asset: '[path].gz[query]',
      algorithm: 'gzip',
      deleteOriginalAssets: true
    })
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

在压缩插件配置之后，我们将初始化 S3 插件:

```
module.exports = {
  ...,
  plugins: {
    new CompressionPlugin({
      test: /\.(js|css)$/,
      asset: '[path].gz[query]',
      algorithm: 'gzip',
      deleteOriginalAssets: true
    })
    new S3Plugin({
      s3Options: {
        accessKeyId: 'your-access-key', // Your AWS access key
        secretAccessKey: 'your-secret-key', // Your AWS secret key
        region: 'eu-west-1' // The region of your S3 bucket
      },
      s3UploadOptions: {
        Bucket: 'my-bucket', // Your bucket name
        // Here we set the Content-Encoding header for all the gzipped files to 'gzip'
        ContentEncoding(fileName) {
          if (/\.gz/.test(fileName)) {
            return 'gzip'
          }
        },
        // Here we set the Content-Type header for the gzipped files to their appropriate values, so the browser can interpret them properly
        ContentType(fileName) {
          if (/\.css/.test(fileName)) {
            return 'text/css'
          }
          if (/\.js/.test(fileName)) {
            return 'text/javascript'
          }
        }
      },
      basePath: 'my-dist', // This is the name the uploaded directory will be given
      directory: 'public/dist' // This is the directory you want to upload
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

点击[这里](https://github.com/webpack-contrib/s3-plugin-webpack)获得更多 S3 插件选项。

设置好 Webpack 配置后，运行 webpack 将会压缩您的资产，并将您的 dist 文件夹上传到您的 S3 存储桶。