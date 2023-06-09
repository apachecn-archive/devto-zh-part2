# [条目]约会日程安排

> 原文：<https://dev.to/dougblackjr/entry-appointment-scheduler-758>

# 我造了什么

[https://www.youtube.com/embed/LT6YtcJ6dZI](https://www.youtube.com/embed/LT6YtcJ6dZI)

此计划程序允许您创建安排在不同房间的约会。您可以创建房间，创建要直接添加到日程安排程序中的约会，在房间和日程安排程序上的时间段之间移动约会，安排以后不添加时间的约会(拖放它们)。

日程安排程序使用套接字与日程安排中的其他人进行通信，这样当您在那里安排日程或编辑约会时，约会时隙就会被锁定。

## 演示链接

项目目前住在这里:[开发调度:scheduler.dougblackjr.com](http://scheduler.dougblackjr.com/)

注意:这个应用程序是为桌面浏览而设计的。

## 链接到代码

回购:[https://github.com/dougblackjr/devto-scheduler](https://github.com/dougblackjr/devto-scheduler)

## 我是怎么建的(栈是什么？在这个过程中，我遇到了问题或发现了新的东西吗？)

### 堆栈:

PHP 7.2
[【laravel】](https://laravel.com/)
[视图。js](https://vuejs.org/)[重定向](https://redis.io/)[【放置](https://pusher.com/)

### 问题:

有两个挑战。

第一个是锁定日历上的时间段，没有为它创建后端模型。为了向大洋彼岸的人显示该时间段正在被编辑，并且为了避免重叠，我使用 Redis 创建了一个键，专门针对该特定资源的该时间段。

第二，也可能是更具挑战性的，是处理时区问题。当我过去尝试这样做时，我非常依赖浏览器来处理时间。但是如果有人在地球的另一边，时间会变得很奇怪。因此，为了处理时间问题，我把几乎所有的东西都扔在了服务器上。

## 附加资源/信息

以下是我的随机想法:

1.  Pusher 让这变得简单了一千倍。
2.  虽然我爱我在世界各地的朋友，如果你们能搬到东部标准时间，我会很感激。
3.  我创建这个是因为日程安排很难。我很感激已经创造出来的东西让这一切成为可能。请偷我的代码，让它变得更好。
4.  感谢 dev.to 成为一个积极的社区，并使这一切成为可能！