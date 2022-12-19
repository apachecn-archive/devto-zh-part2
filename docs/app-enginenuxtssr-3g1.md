# 在 App Engine 的标准环境中使用 Nuxt 进行免费 SSR

> 原文：<https://dev.to/dala00/app-enginenuxtssr-3g1>

似乎在谷歌应用程序引擎( gae )的标准环境中添加了 Node.js。 本来々Node.js 只能在收费的 Flexible 环境下使用，所以能免费使用我非常高兴。

原本可以通过々Firebase Hosting 或 Netlify 免费移动 Nuxt，但由于它们是静态托管服务，因此无法进行 SSR (服务器端渲染)。 (虽然好像可以通过使用 Functions 等方式实现，但是我想随着访问量的增加，费用也会增加。)

如果是 GAE 的话，由于 Node 在服务器上运行，所以 SSR 也可以正常进行，SEO 方面似乎也很好。 因此，首先在谷歌 app engine 上移动 Nuxt，尝试到了可以进行 SSR 的程度。

在 Windows Subsystem 的 Ubuntu 上移动了。

## 本地 Nuxt 项目的初始化

首先在本地创建 Nuxt 项目，使其可以工作。

```
vue init nuxt-community/starter-template nuxtproject
cd nuxtproject
yarn 
```

Enter fullscreen mode Exit fullscreen mode

总之先粗略确认一下这个能不能动。

```
yarn run dev 
```

Enter fullscreen mode Exit fullscreen mode

[只要访问 http://localhost:3000 并运行即可。](http://localhost:3000%E3%81%AB%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%81%97%E3%81%A6%E5%8B%95%E3%81%84%E3%81%A6%E3%81%84%E3%82%8C%E3%81%B0OK%E3%81%A7%E3%81%99%E3%80%82)

为了在 GAE 中部署内部版本，请先执行 Nuxt 项目的内部版本。

```
yarn run build 
```

Enter fullscreen mode Exit fullscreen mode

## 谷歌应用引擎用の設定

[谷歌应用引擎上的 nuxt . js(GAE)](https://qiita.com/SatoTakumi/items/4f418dc5c700b9d66d7d)

使用以上页面，在 package.json 的开始脚本中设置端口和主机设置。 (评论中好像写着不需要端口)

```
"start":  "HOST=0.0.0.0 PORT=8080 nuxt start", 
```

Enter fullscreen mode Exit fullscreen mode

也做 app.yaml。 如果不设置 handlers 缓存静态文件，内存可能会不足而停止。

```
runtime: nodejs8
env: standard
handlers:
- url: /_nuxt
  static_dir: .nuxt/dist
- url: /.*
  script: auto 
```

Enter fullscreen mode Exit fullscreen mode

我的话，不知什么时候有了`.gcloudignore`文件，所以我在那里设置了不部署的文件。 没有的话就做吧。 如果部署`.git`和`node_modules`，好像就不能很好地部署。

然后部署到 GAE。

```
gcloud app deploy 
```

Enter fullscreen mode Exit fullscreen mode

只是，我的情况出现了错误。 参考以下内容，安装 sqlite3 的库。 之后 Python 好像也需要重新安装。 因为我使用了 pyenv，卸载和重新安装很轻松，所以帮了大忙了。

[【ubuntu】导入错误:没有名为'_sqlite3'とでたときの的模块対処法【Python】](https://qiita.com/chatrate/items/bc37e3b7b303e834dd1e)

```
sudo apt-get install libsqlite3-dev libbz2-dev libncurses5-dev libgdbm-dev liblzma-dev libssl-dev tcl-dev tk-dev libreadline-dev 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

然后，在部署到 GAE 后，可以通过命令或 URL 直接访问来确认操作，如消息中所示。 如果能顺利显示 Nuxt 运行的画面就完成了。

如果你看一下源代码，你会看到 Vue 组件中的字符串等。

不过，谷歌 app engine 的标准环境似乎默认只有 128MB 的内存。 如果是免费限额的话就只能使用了，但是在使用 Nuxt 的 Node 环境下，能承受多大程度的动作还是个谜。 我想继续试试。