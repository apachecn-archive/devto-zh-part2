# Netlify 的 victor-hugo 样板文件的快速内容重建

> 原文：<https://dev.to/dzello/faster-content-rebuilding-for-netlifys-victor-hugo-boilerplate-4mod>

无论你只是在学习雨果，还是把你的雨果经验带到一个新项目中，你都很有可能会接触到 Netlify 的[维克多-雨果](https://github.com/netlify/victor-hugo)样板。这是 Hugo 文档中列出的第一个[初学者工具包](https://gohugo.io/tools/starter-kits/)，已经获得了超过 750 颗闪亮的 Github 星。

以下是样板文件的内容摘要:

*   [大口](https://gulpjs.com/)看文件和运行任务
*   [雨果](https://gohugo.io)用于构建`site`目录中的内容
*   [webpack](https://webpack.js.org/) 用于构建`src/js`到`dist/app.js`中的 JavaScript
*   [PostCSS](http://postcss.org/) 用于建立从`src/css`到`dist/main.css`的 CSS
*   [浏览器同步](https://browsersync.io/)用于实时重载开发中的浏览器

总的来说，关于 victor-hugo 和样板文件的伟大之处在于，在你开始使用它之前，你不需要确切地知道每一部分是做什么的。要启动并运行 victor-hugo，您只需要几个命令:

```
$ git clone git@github.com:netlify/victor-hugo.git

$ cd victor-hugo

$ npm install

$ npm start 
```

导航到 [http://localhost:3000/](http://localhost:3000) ，你会看到一个网页。

[![victor-hugo homepage](img/b81680574a39b2393a45394296692fb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--51lKzzUy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dzello/image/upload/c_pad%2Cf_auto%2Cq_auto%2Cw_1200%2Ch_630/v1524584290/blog/netlify-victor-hugo-boilerplate-plain)

## 重装修改

当您更改`src`文件夹中的资源或`site`文件夹中的 Hugo 内容时，gulp 任务将重建内容，浏览器将自动刷新。对于中小型网站来说，这一切都发生在几毫秒内，给开发者带来了非常愉快的体验。

下面是它在引擎盖下的工作原理。`server` gulp 任务观察`site/**/*`中的变化，然后产生一个`hugo`进程来构建站点，当它完成时退出。一旦 gulp 任务检测到 hugo 进程成功退出，它就通知 Browsersync 刷新页面。代码在`gulpfile.babel.js` :
中的`buildSite()`方法中

```
return spawn(hugoBin, args, {stdio: "inherit"}).on("close", (code) => {
  if (code === 0) {
    browserSync.reload();
    cb();
  } else {
    browserSync.notify("Hugo build failed :(");
    cb("Hugo build failed");
  }
}); 
```

## 加速内容的重新加载

然而，随着静态站点内容量的增长，这种方法可能无法像您希望的那样快速地构建更改。每个新的 hugo 进程都必须从磁盘中读取所有内容，并且随着站点的增长，读取时间也会增加。对于我的大约 90 页的网站来说，重建不再是即时的了。

有一种简单的方法可以让这个过程更快更有效。诀窍是不要在每次更改时都启动一个新的 Hugo 进程(进行完整的重建)，而是使用 Hugo 的内置功能来服务和监视具有增量重建的文件，也称为[快速渲染模式](https://gohugo.io/news/0.30-relnotes/)。

Hugo 有一个运行内置 HTTP 服务器的`server`子命令，并接受一个监视底层文件的`-w`标志。正如 Hugo docs 提到的，这具有[性能优势](https://gohugo.io/commands/hugo_server/):

> " **hugo server** 将避免将呈现和提供的内容写入磁盘，而是将其存储在内存中。"

## 泽罗/维克多-雨果

我为 victor-hugo 创建了一个[分支，增加了一个吞咽任务来利用 hugo 的这个特性。这个任务叫做`server-hugo`，它在观察模式下使用 Hugo HTTP 服务器，就像你运行`hugo server -w`一样。](https://github.com/dzello/victor-hugo)

下面是`server-hugo`任务的样子:

```
// Development server with hugo --watch
gulp.task("server-hugo", ["css", "js", "fonts"], (cb) => {
  browserSync.init({
    proxy: "localhost:3001",
    ui: { port: 3002 }
  });
  gulp.watch("./src/js/**/*.js", ["js"]);
  gulp.watch("./src/css/**/*.css", ["css"]);
  gulp.watch("./src/fonts/**/*", ["fonts"]);
  watchSite(cb);
}); 
```

下面是它调用的`watchSite`方法:

```
/*
* Run hugo in watch mode
*/
function watchSite(cb, options = {}, environment = "development") {
  const args = ["server"].concat(hugoArgsDefault).concat(options).concat(["-w", "-p", "3001"]);

  process.env.NODE_ENV = environment;

  return spawn(hugoBin, args, {stdio: "inherit"}).on("close", (code) => {
    if (code === 0) {
      cb();
    } else {
      cb("Hugo process exited");
    }
  });
} 
```

为了避免丢失原始`server`任务的任何实时重载功能，该任务使用 Browsersync 的代理特性。Browsersync(在端口 3000 上)位于 Hugo(在端口 3001 上)的前面，这样它可以将自己的 [LiveReload](http://livereload.com/) 实例注入页面。通过这种设置，浏览器将在 Hugo 和和资产重建后立即重新加载(或应用 CSS 更改)。

## --disableFastRender

从 0.3.0 开始，Hugo [通过只构建站点中发生变化的部分来加速实时重载](https://gohugo.io/news/0.30-relnotes/)。这是一个伟大的功能，正好符合雨果对速度的不懈需求的传统。然而，以我的经验来看，这可能会导致对一个模板或部分模板的更改不能重建包含它的每个页面。

如果您遇到同样的问题，您可以通过在`hugo server`命令中添加一个`--disableFastRender`参数来关闭增量构建。构建时间稍长，但好处是您永远不必担心陈旧的内容。

要将`--disableFastRender`添加到上面的吞咽任务中，只需修改如下代码行:

```
const args = ["server"].concat(hugoArgsDefault).concat(["-w", "-p", "3001", "--disableFastRender"]); 
```

快乐样板！