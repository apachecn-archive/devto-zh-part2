# 艾迪·奥斯马尼的 18 点网络绩效清单

> 原文：<https://dev.to/ben/addy-osmanis-18-point-web-performance-checklist-2e1>

今年夏天，我正在阅读艾迪·奥斯马尼的《问我任何事》。虽然这个帖子充满了智慧，但是这个评论却是 web dev 的黄金:

[![addyosmani profile image](img/b3bea65c3292b92e2ba253240eedd296.png) ](/addyosmani) [ Addy Osmani ](/addyosmani) • [<time datetime="2018-07-11T17:33:31Z" class="date-short-year"> Jul 11 '18 </time>](https://dev.to/addyosmani/comment/46n7) 

我检查的第一个性能改进是站点是否可以减少 JavaScript，同时仍然为最终用户提供大部分价值。如果你发送几兆字节的 JS，如果你的目标用户主要在桌面上，这可能完全没问题，但是如果他们在移动设备上，这通常会使其他资源的成本相形见绌，因为它可能需要更长的处理时间。

总的来说，我会试着浏览下面的列表，看看网站是否可以在一个或多个方面做得更好:

✂️派少 JavaScript(代码分解)
😴懒惰加载非关键资源
🗜努力压缩！
📦有效缓存(HTTP，服务人员)
⚡️ Minify &优化一切
🗼为关键来源预解析 DNS
💨预载关键资源
📲尊重数据计划
🌊流 HTML 响应
📡发出更少的 HTTP 请求
📰有网页字体加载策略
🛣基于路径的分块
📒库分片
📱PRPL 图案
🌴摇树(Webpack，RollUp)
🍽服务于现代浏览器 ES2015 (babel-preset-env)
🏋️‍♀️范围吊装(网袋)
🔧不要将开发代码交付给产品

网页性能对用户体验至关重要。在它成为一个问题之前优先考虑它。

快乐编码