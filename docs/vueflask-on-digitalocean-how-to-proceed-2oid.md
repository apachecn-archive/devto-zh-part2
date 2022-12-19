# 数字海洋上的 Vue+Flask:如何进行？

> 原文：<https://dev.to/danroc/vueflask-on-digitalocean-how-to-proceed-2oid>

嗨！

继我之前的[帖子](https://dev.to/danroc/ready-to-deploy-vue--flask-but-where-to-deploy-it-to-57o3)之后，我现在正试图让我的应用程序在**单曲**数字海洋水滴上运行。

但是我应该如何接近它呢？

让我的 Vue app 对外服务，Flask API 在 droplet 本地服务，让我的 Vue app 通过`localhost:port`访问，这样做对吗？

或者我需要两个服务器块，一个用于 Vue 应用，一个用于 Flask API？

(该 API 仅供内部使用，我不打算为其提供公共端点)

谢谢你的帮助！
丹尼尔