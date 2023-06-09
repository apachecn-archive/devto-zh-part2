# 爬行投票:帮助移动中的团体选择下一个地点

> 原文：<https://dev.to/nholden/crawl-vote-helping-groups-on-the-move-pick-a-next-spot-11o9>

# 我造了什么

爬行投票帮助移动中的团体选择下一个地点。无论他们是在蒙特利尔寻找 poutine 还是在蒂华纳寻找 tacos，Crawl Vote 都会返回一些建议供大家一起投票。

## 演示链接

[https://crawlvote.com](https://crawlvote.com)

## 链接到代码

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ nholden ](https://github.com/nholden) / [爬行 _ 投票](https://github.com/nholden/crawl_vote)

### 帮助移动中的小组选择下一个地点

<article class="markdown-body entry-content container-lg" itemprop="text">

# 爬行投票

帮助移动中的小组选择下一个地点

## 装置

```
git clone git://github.com/nholden/crawl_vote
cd crawl_vote
brew install redis postgresql heroku
bundle install
yarn
cp .env.example .env
bundle exec rake db:reset 
```

## 入门指南

在 https://pusher.com/signup 的[注册一个推送频道应用，在 https://www.yelp.com/developers/documentation/v3](https://pusher.com/signup)的[注册一个 Yelp API 密钥，然后更新`.env`。](https://www.yelp.com/developers/documentation/v3)

在单独的终端窗口中启动这些长时间运行的进程:

```
heroku local
webpack-dev-server 
```

爬网投票应该可以在 [http://localhost:3345](http://localhost:3345) (或您在`.env`中指定的任何端口)访问。

## 测试

```
bundle exec rspec 
```

## 贡献的

欢迎大家投稿！请随意提出请求或使用 GitHub 问题来帮助入门、报告错误或提出功能请求。

## 许可证

这个项目是由尼克·霍尔登创建的，并获得了麻省理工学院的许可。

</article>

[View on GitHub](https://github.com/nholden/crawl_vote)

# 我是如何建造的

抓取投票在后端使用 [Ruby on Rails](https://rubyonrails.org/) 在前端使用 [Vue.js](https://vuejs.org/) 。我使用了 [Webpacker](https://github.com/rails/webpacker) ，它带有一个 [Vue 集成](https://github.com/rails/webpacker#vue)，来编译和捆绑我的 JavaScript、CSS 和图像。我使用 [Tailwind](https://tailwindcss.com/) 快速原型化我的设计，没有写任何 CSS。

我以几种方式使用了[推送通道](https://pusher.com/channels)。当用户第一次提交他们要去的地方和他们要找的东西时，Crawl Vote 会创建一个新的“爬网”，给它一个唯一的名称，并为用户订阅一个同名的推送频道。然后，应用程序启动一个 [Sidekiq](https://sidekiq.org/) 任务，在后台查询 [Yelp 的 Fusion API](https://www.yelp.com/fusion) 。一旦作业获取了相关的业务并将它们持久化到 [PostgreSQL](https://www.postgresql.org/) 数据库中，就会触发一个事件到 Pusher 通道。当用户的浏览器接收到该事件时，Vue 组件使用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 请求获取所有相关数据并显示在屏幕上。

一旦用户成功地创建了一个抓取并邀请了一些朋友，推送通道就允许每个人在不刷新浏览器的情况下实时看到投票。当用户单击一个点旁边的投票按钮时，它会向应用程序发出请求，该应用程序会保存投票并向推送通道触发一个事件。当所有用户的浏览器都收到该事件时，Vue 组件发出另一个请求来刷新屏幕上的数据。

[![Crawl Vote demo](img/fc7dcf7cb4ab0f9d69a6005aeb07b7b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uPCefLFH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x0f896noabygq0f0xhkz.gif)

我以前从未使用过 Vue 的[列表移动过渡](https://vuejs.org/v2/guide/transitions.html#List-Move-Transitions)，但我真的很惊讶，在很少的 CSS 下，Vue 添加了如丝般平滑的动画，作为在排名中上下移动的点。神奇！✨

为了识别用户，Crawl Vote 为每个人分配一个 UUID，并将其存储在用户的会话中。当用户第一次访问爬网或刷新页面时，应用程序直接从会话中识别用户，并在服务器端呈现数据。当用户的浏览器接收到 Pusher 事件，让它知道是时候获取更多数据时，UUID 作为令牌在[授权请求头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization)中发送。

爬行投票由[崔维斯 CI](https://travis-ci.com/) 持续部署到 [Heroku](https://www.heroku.com/) 并由 [Cloudflare](https://www.cloudflare.com/) 服务。我使用[滚动条](https://rollbar.com/)进行异常跟踪，使用 [Papertrail](https://papertrailapp.com/) 进行日志记录。我唯一花钱买的是域名，它来自 [Namecheap](https://www.namecheap.com/) 。

# 接下来是什么

我对 MVP 的结果很满意，但是我有一大堆事情想花更多的时间去尝试。我想增加测试覆盖面，包括至少一两个使用[无头 Chrome](https://developers.google.com/web/updates/2017/04/headless-chrome) 点击通过我的 Vue 组件的 [Rails 系统测试](http://guides.rubyonrails.org/testing.html#implementing-a-system-test)。

我也想继续改善 UX/用户界面，包括添加自动完成表单字段。Yelp 的[自动完成端点](https://www.yelp.com/developers/documentation/v3/autocomplete)返回的类别看起来可能对“查找”字段有用，我过去一直很高兴与谷歌的[位置自动完成](https://developers.google.com/places/web-service/autocomplete)合作。整体设计可以使用一点打扮，我认为一些深思熟虑的颜色使用可以帮助外观和感觉更好地反映应用程序的精神。

最后，如果能对 Crawl Vote 发送给 Yelp 的 API 的参数做一些调整，那就太好了。有[很多选项](https://www.yelp.com/developers/documentation/v3/business_search)，我不确定我是否想向用户展示太多，因为复杂性可能会让人们离开，但我喜欢做一些优化，以确保 Crawl Vote 为每个查询返回一个很好的选择。