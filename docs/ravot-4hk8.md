# 实时仪表板

> 原文：<https://dev.to/franko4don/ravot-4hk8>

# 我造了什么

公司的实时仪表板

## 演示链接

[https://realtime.bluebecks.com/](https://realtime.bluebecks.com/)
为了进行测试，我删除了一个管理员用户的登录名和密码

【https://realtime.bluebecks.com/login
`email: franko4don@gmail.com`
`password: tomcat`

## 链接到代码

[https://github.com/RealDash/realtime-dashboard](https://github.com/RealDash/realtime-dashboard)

# 我是怎么建的(栈是什么？在这个过程中，我遇到了问题或发现了新的东西吗？)

*   项目概述

我们开发了一个实时仪表盘来显示任务、公告、未来的重要事件、当前正在播放的音乐等等。我们的仪表板是使用 Laravel、Vue 和 Pusher 构建的。我们的目的是在办公室靠墙的电视屏幕上展示这个仪表盘。

仪表板(实时仪表板端)将包含以下内容:

1.  管理员安排的即将举行的活动。向实习生/团队成员显示公告的公告区。
2.  一块肮脏的木板。
3.  Github 部分来显示我们在 Github 上众多公共存储库的统计数据。
4.  Bitbucket 部分。
5.  办公室里正在播放的音乐。
6.  带有当前日期和时间的时钟。

在浏览器第一次显示仪表板后，我们将不再刷新页面。正在利用 WebSockets 和 Vue 来更新这些部分。这样做可以避免刷新页面和闪烁屏幕。

每个部分都有自己的 Vue 组件。Laravel 的默认调度程序用于检查预定的事件，并在逻辑满足时触发一个广播给 pusher 的事件。当接收到数据时，向推送器触发一个广播事件。

这个强大的服务(Pusher)利用 websockets 将服务器事件实时传递给客户机。在客户端，我们使用了 Laravel 的 echo。JavaScript 库使得处理这些 Pusher 事件变得非常容易。仍然在客户端，每个 Vue 组件将监听新事件以更新显示的部分。

# 团队成员

我们有三个人从事这个项目。团队成员在 Github 上进行合作，项目开发于 2018 年 5 月 18 日星期五开始，这是在 2018 年 5 月 2 日首次提交之后。

以下是团队成员的姓名:

1.  恩万兹·富兰克林(弗兰克·罗斯福):队长
2.  蒂莫西·奥尼尤克(蒂莫林):小组成员
3.  nwankwo Gabriel(gfun):小组成员

# 它是如何建成的

我们首先对我们要设计的系统进行需求分析，这是一个具有实时反馈的小型任务管理系统。我们迭代了一些要使用的设计和技术，然后得到了我们的规格。

经过深思熟虑，我们得出了以下结论

*   规范

1.  Laravel，vuejs，Laravel Echo 和 Pusher 将用于该项目，Laravel 用于后端，Laravel Echo 和 Vuejs 用于前端，Pusher 作为实时更新的 websocket
2.  任务管理模块，供管理员创建任务，同时设置描述和计划的开始和完成时间。
3.  任务分配模块为用户谁将能够选择任务，对他们进行工作，并通过点击一个按钮，使其进入审查交付
4.  音乐管理模块，使管理员能够创建和删除音乐，以及设置当前音乐播放的实时仪表板视图只需点击一下实时
5.  公告管理模块，使管理员能够创建公司范围的公告。
6.  为用户提供的要点模块，任何人都可以用尽可能短的话说出最新的要点
7.  事件管理模块使管理员能够创建即将到来的事件，以便在实时仪表板上显示
8.  艺术家模块，用于管理平台上有音乐的艺术家
9.  类别管理，用于添加可以创建的任务类别
10.  用户模块由管理员管理平台上的用户，管理员可以删除用户以及查看用户
11.  允许用户管理其个人资料的个人资料模块

*流程

1.  数据库分析
2.  与 github 的版本控制集成
3.  迁徙和播种的创造
4.  仪表板模板集成(Gentelella alella)
5.  从 github 到测试服务器的简单自动部署设置
6.  用 HTML 和 CSS 设计实时 feed 仪表盘
7.  创建路线和控制器以及添加功能
8.  后端的推送器集成
9.  面向前端的 Laravel 回声集成
10.  使用 laravel mix 的 Vuejs 设置
11.  将 live feed dashboard 的 html 和 css 设计分解成 vue 组件
12.  为组件添加功能并为 pusher 事件设置监听器
13.  用假数据测试每个模块
14.  修复发现的错误
15.  部署

# 附加资源/信息

[https://blog . pusher . com/writing-real time-apps-with-laravel-5-and-pusher/](https://blog.pusher.com/writing-realtime-apps-with-laravel-5-and-pusher/)
[https://vuejs.org/](https://vuejs.org/)
[https://laravel.com/docs/5.5](https://laravel.com/docs/5.5)
所有这些都是占位符文本。使用这种格式或您选择的任何其他格式来最好地描述您的创作。

# 结论

感谢 dev.to 和 pusher 给我们这次探索实时应用的机会，这个应用稍后将用于我们正在建立的新技术中心([https://binaryhills.org](https://binaryhills.org))，用于管理任务和日常活动。