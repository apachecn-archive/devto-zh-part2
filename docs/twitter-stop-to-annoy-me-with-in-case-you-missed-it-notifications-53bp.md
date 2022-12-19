# Twitter，不要再用“万一你错过了”的通知来烦我

> 原文：<https://dev.to/c33s/twitter-stop-to-annoy-me-with-in-case-you-missed-it-notifications-53bp>

不久前，我注意到我在 twitter 上收到了许多通知(是的，我并不真的使用 twitter)。登录并查看我的通知:没有转发，没有喜欢你的推文，没有新的关注者，...只有所有这些“万一你错过了”通知无法禁用它们。

谷歌了一下，只发现有人问如何禁用它们，但没有找到解决方案。感谢推特*不是*

这让我很恼火，所以我写了一个小的[油滑猴子](https://addons.mozilla.org/en-US/firefox/addon/greasemonkey/)脚本:

```
$('ol#stream-items-id > li[data-component-context="generic_activity"]').hide(); 
```

Enter fullscreen mode Exit fullscreen mode

这中断了旧通知的持续加载。解决方法是调整浏览器窗口的大小，然后触发加载。

当然，通知点指示器仍然显示这样的通知是新的，但至少它们不会弄乱一切。

*   链接:[https://www . Reddit . com/r/Twitter/comments/8 C2 DTV/how _ to _ remove _ in _ case _ you _ missed _ it _ notifications/](https://www.reddit.com/r/Twitter/comments/8c2dtv/how_to_remove_in_case_you_missed_it_notifications/)

*   封面图片由 Pexels:[https://pix abay . com/en/girl-grass-nature-summer-cute-1839623/](https://pixabay.com/en/girl-grass-nature-summer-cute-1839623/)