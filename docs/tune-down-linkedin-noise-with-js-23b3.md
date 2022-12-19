# 用 JS 调低 LinkedIn 噪音

> 原文：<https://dev.to/hydroweaver/tune-down-linkedin-noise-with-js-23b3>

[![image](../Images/bdfe6ae0abfc80bb91bba8f277a5a09a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nmG1DWUj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/f/f6/White-noise-mv255-240x180.png)

大家好，

我最近意识到在我的 LinkedIn feed 上有多少无用的**喜欢、关注、分享**,于是决定改变一下，一次不关注几个人。然而 LinkedIn 不提供这个选项(至少我不知道)。这个方法相当简单，很多人已经这样做了，但这里是我的看法。

#### 探索

进入[管理关注者](https://www.linkedin.com/feed/followers/)页面，点击**旁边的 **x 关注**关注新鲜观点**。点击后的**检查上面的任何人。检查时，你会看到有太多信息的大 DOM。**

#### 讲的是什么？

这只是一个在跟随和不跟随之间切换的按钮。

#### 我们想做什么？

**假设你想取消关注所有人**。点击这个页面上所有这些**下面的**按钮，这就是 JS 派上用场的地方。当你跟随某人时，按钮将会是跟随的**类和不跟随**的**类。**

#### 我们要做什么？

在按钮后循环所有这些**。看看这个[链接](https://www.sitepoint.com/community/t/looping-through-li-elements-in-ul/6049)。**

#### 如何？

在控制台中，这是您在同一页面上所做的事情。这可能是一种愚蠢/幼稚的做法，但它确实有效。

```
//Create a variable that refers to the **CLASS FOLLOWING** buttons on this page.

var follow = document.getElementsByClassName("SEARCH THE CLASS ;)");

//get follow length, that is, how many buttons are there on the current scroll, since they increase when you go down, responsive web design or whatever that is.

var followLength = follow.length;

//loop over all the buttons and click them one by one, thereby unfollowing people.

for(var i =0;i<followLength;i++){
        follow[0].click();
    } 
```

#### 局限性

1.  因为我是一个 JS noob，当页面向下滚动时，跟随长度会更新，这可能会引起一些麻烦。
2.  如果你跟随着每个人，比如像我一样跟随着 500 个人，它可以让 chrome 停下来。
3.  坦白地说，我被 following/followers 页面弄糊涂了，因为 followers 页面也有相同的按钮，然后它自己重置，因为....我无法解释，但为什么 LinkedIn 不能做到这一点呢？！所以你可能想检查一下这种行为，看看它对几个人是如何起作用的。

我尝试这样做是因为我想取消关注所有人，如果你这样做了，你可能想关注与你的案例相关的一小部分人，否则你将再次手动检查/取消检查他们！T3】

哦，别忘了点击顶部的完成按钮来刷新你的订阅！:)

玩得开心！