# Shopoji - Pusher 存在渠道很酷

> 原文：<https://dev.to/itsasine/shopoji---pusher-presence-channels-are-cool-4nhg>

这个应用程序的最初设想是一个单人商店游戏，很少受到其他人在自己游戏中的影响。Pusher 公共频道似乎很适合这个用例。

尽管任何时候我想做一些更酷的事情，它都被锁定在 Pusher 存在频道后面。

因此...我想我正在重写我的应用程序逻辑。

如果我不能让它工作，我会提交我有的。它有坚实的 MVP ( *最小*可行的产品)功能，一个[信息网站](http://shopji.xyz)，和一个可播放的演示(现在指向一个中断的开发分支，所以没有敏感的 pls)，但有这么多，只是存在渠道会更好直观。

1.  我可以做客户端触发器
2.  我可以给我的触发消息提供更多一点的上下文，关于谁正在触发它们
3.  最重要的是，我可以通过知道在任何给定时间市场中有多少商店来修复我的全球市场中的一个相当大的逻辑错误

# 最新消息

非常

[![Screenshot demo](img/5d61b706fe2f52deddab53408c451419.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ir5MxXBH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://shopoji.xyz/public/assets/img/demo.png)

可以多买库存。你可以卖掉你的存货。可以赚钱。UI 现在是引导的，所以不太可怕。

# 还剩什么

一旦我重写了一些东西，让它更有存在感，我就称之为竞争。待办事项清单上还有更多，但我将处于收益递减点。

不过说真的，如果我不能在明天一个小时内让 auth 为在线频道工作，我现在就宣布它完成了😛范围 creeeeeeeeeeeep。