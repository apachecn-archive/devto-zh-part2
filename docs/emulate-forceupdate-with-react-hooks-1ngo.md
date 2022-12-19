# 使用 React 挂钩模拟 forceUpdate

> 原文：<https://dev.to/dance2die/emulate-forceupdate-with-react-hooks-1ngo>

更新时间:2019-02-12

正如[在评论区**的**](https://sung.codes/blog/2018/11/08/emulate-forceupdate-with-react-hooks/?preview_id=1931&preview_nonce=5e2a9a7f1b&preview=true&_thumbnail_id=1949#comment-4333911452)和的[迪米塔·涅斯托罗夫的所指出的，这个帖子从 16.8.0 版开始就不再有效了。](https://disqus.com/by/dimitarnestorov/)

官方 React 文档[有类似 forceUpdate 的东西吗？](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-forceupdate)不鼓励使用`forceUpdate`。

*自我注释...*

今天我学到了你可以使用 [React Hooks](https://reactjs.org/hooks) 方法 [useState](https://reactjs.org/docs/hooks-reference.html#usestate) 中的第二个值来模拟 [forceUpdate](https://reactjs.org/docs/react-component.html#forceupdate) 。

Donavon 在 Twitter 上分享了一段代码，讲述了如何使用 React 钩子来模仿 [forceUpdate](https://reactjs.org/docs/react-component.html#forceupdate) 。

> ![donavon "spread love" west profile image](../Images/b95ad5bd81b74e9b20b344dbc8b70536.png)多纳文《传情》西[@多纳文](https://dev.to/donavon)![twitter logo](../Images/65e26e35707d96169ec8af6b3cbf2003.png)如何运作？“useForceUpdate”调用“useState”并返回 setter 函数。无论何时调用 setter，它都会强制重新渲染。该值(在本例中为“未定义”)不需要更改。
> 
> 奇怪，但很管用。🤷‍♂️2018 年 11 月 08 日下午 14:23[![Twitter reply action](../Images/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1060538400292421632)[![Twitter retweet action](../Images/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1060538400292421632)0[![Twitter like action](../Images/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1060538400292421632)2

多纳文在 forceUpdate 上的推特

下面是复制/粘贴的代码片段。