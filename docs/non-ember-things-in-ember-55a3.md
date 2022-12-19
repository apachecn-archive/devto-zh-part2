# 余烬里非余烬的东西

> 原文：<https://dev.to/gaiety/non-ember-things-in-ember-55a3>

Ember 社区很棒，并且[充满了优秀的 ember-cli 友好插件](https://www.npmjs.com/search?q=ember)来添加到您的项目中。使用 [Redux](https://github.com/reactjs/redux) ？看看[余烬-redux](https://www.npmjs.com/package/ember-redux) 。需要[快速点击](https://github.com/ftlabs/fastclick)？检验...好吧，实际上你最好的选择是[余烬-锤子时间](https://www.npmjs.com/package/ember-hammertime)但是你明白了。

但是有时候你只是需要 T2 加入一些还没有准备好的东西。今天，这比以往任何时候都容易！

## 给我看看

在这个例子中，我们将把`url-polyfill`添加到 Ember 应用程序中。*我知道`ember-url`是存在的，但是它缺少`url-polyfill`提供的一些功能。*

1.  `yarn add url-polyfill`或`npm install --save url-polyfill`
2.  在您的`ember-cli-build.js`中的`module.exports = function() {`内添加以下内容...

```
app.import('node_modules/url-polyfill/url-polyfill.js'); 
```

Enter fullscreen mode Exit fullscreen mode

你完了！

对于奖励积分，您甚至可以根据环境配置您使用的导入...

```
app.import({
  development: 'PATH/file.js',
  production: 'PATH/file.min.js',
}); 
```

Enter fullscreen mode Exit fullscreen mode

这也适用于 css 文件，以引入您喜欢的 CSS 框架、动画库或其他工具。如果您仍在使用 bower，只需将导入路径指向`bower_components/DEPENDENCY/FILE.EXT`。

### 但是，等等，我需要做一些设置！

那也很酷！初始化式可能是你想要的。

```
ember generate initializer myInitializer 
```

Enter fullscreen mode Exit fullscreen mode

然后在那里你可以运行任何你想要的初始化代码，它将在应用程序加载时运行。

## 想更进一步？

为你最喜欢的工具制作一个 ember-addon 并发布在 npm 上！社区会感激它，并喜欢它的简单。

去创造伟大的事物吧。或者...把伟大的事情结合成更伟大的事情！