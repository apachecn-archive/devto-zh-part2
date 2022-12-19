# 使用 HtmlWebpackPlugin 的多个 HTML 文件

> 原文：<https://dev.to/mariorodeghiero/multiple-html-files-with-htmlwebpackplugin-19bf>

# 用 HtmlWebpackPlugin 生成多个 HTML 文件

在过去的日子里，我在我的博客上做了一些修改，其中之一是停止使用 Gulp，而使用 Webpack。在配置过程中，我遇到了一些困难，其中之一是使用插件 HtmlWebpackPlugin 生成多个 HTML 文件的过程。

## 版本

```
"html-webpack-plugin": "^2.30.1",
"webpack": "^3.11.0" 
```

Enter fullscreen mode Exit fullscreen mode

我们假设您已经配置了 webpack，并且想要实现 HtmlWebpackPlugin 插件来生成多个 HTML 文件。因此，我们需要使用下面的命令来安装插件:

`$ npm i --save-dev html-webpack-plugin`
或
`$ yarn add --dev html-webpack-plugin`

## 默认 HtmlWebpackPlugin 配置

一旦你配置了需求，通常像这样使用插件

```
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin')

let HWPConfig = new HtmlWebpackPlugin({
  template: __dirname + "/index.html",
  file: "index.html",
  inject: "body"
});

module.exports = {
  .
  .
  .
  plugins: [
    HWPConfig
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢将插件设置存储在任何变量中，然后在插件中使用它

## 自定义 HtmlWebpackPlugin 配置

如果我们对每个新文件使用默认设置，并且根据文件的数量，会使我们的 webpack 配置文件非常庞大。所以让我们创建以下配置:

```
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin')

let HWPConfig = new HtmlWebpackPlugin({
  template: __dirname + "/index.html",
  file: "index.html",
  inject: "body"

let articlesHtmlPlugin = ['sass-react', 'chart-js', 'copy-right', 'object-literal', 'spread-operator'];

let multiplesFiles = articles.map(function(entryName) {
  return new HtmlWebpackPlugin({
    filename: entryName + '.html',
    template: __dirname + `/articles/{entryName}.html`
})

module.exports = {
  .
  .
  .
  plugins: [
    HWPConfig
  ].concat(articlesHtmlPlugin)
}; 
```

Enter fullscreen mode Exit fullscreen mode

1 -我们创建一个数组，包含 articles 文件夹中每篇文章的确切名称，但没有扩展名。

2 -我们创建一个“multiplesFiles”变量，在其中存储“articles.map”返回。它将遍历数组中的每一项，在文件名中添加名称+扩展名，然后用模板中的文件名通知目录。

3 -使用“concat (articlesHtmlPlugin)”在插件末尾连接我们的变量 articlesHtmlPlugin

## 结论

这是使用 HtmlWebpackPlugin 插件生成多个 HTML 文件的替代方法之一。现在，随着每个新文件的创建，只需在数组中插入确切的名称，剩下的就交给 map 了。

谢谢！！