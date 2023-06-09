# 我使用的一个键依赖被弃用了，我学到了什么？

> 原文：<https://dev.to/jbull328/a-key-dependency-i-was-using-was-depreciated-what-did-i-learn-2kmf>

嗯，我花了一些时间，摸索了一些错误的开始，尝试了一些不起作用的东西，最终我再次进入了应用程序迁移的最佳状态。感觉棒极了，再次坐在驾驶座上，愉快地埋头写代码，让我的应用程序变得栩栩如生。所以我学到的是不要使用第三方认证 API，自己写一个就行了，这将会节省大量的时间和精力。

因此，我有一个社交媒体类型的应用程序，用户可以登录创建一个帐户，添加一个博客，并发布他们已经建立的项目的例子。这工作得很好，但为了让它与我正在使用的旧身份验证 API 一起工作，我必须做几个变通办法来让一些东西工作，这些变通办法导致我必须有一些糟糕的代码，现在回头看，我可以说。当时我想，“这将工作，现在，我将在应用程序的数据库中为每个用户创建一个自定义字段，本质上是将 id 从我的 mongo 数据库复制到第三方数据库，然后我可以检查它是否与我的一些功能匹配。我当时不知道 passprt.js，所以这看起来是一个公平的交易，现在我已经实现了 passport.js，我可以通过 passport.js 的内置方法检查用户。这节省了大量的时间，我发现我做了很多这样的小事，这让我意识到我在使用这个当时看起来合理的捷径时做了很多额外的工作。

让我们比较一下两条 get 路线，它们本质上做的是同样的事情。

第一个是老方法。这并不是说他们有太多的代码，而是一直在排除故障和测试额外的步骤和页面，而最终却有了一个更简单的方法。

```
router.get(‘/showUser/:id’, function(req, res) {
        FccUsers.findById(req.params.id).populate(‘projects blogs’).exec(function(err, userRef) {
            if (err) {
                console.log(err);
            } else {
                Project.find(function(err, projects) {
                if (err) {
                    console.log(err);
                } else {
                    Blog.find(function(err, blogs) {
                    if (req.user &amp;&amp; userRef._id === req.user.customData.authUserID) {
                            res.render(“showUser”, {userRef: userRef, projects: projects, blogs: blogs,});
                        } else {
                            res.render(“showUserPublic”, {userRef: userRef, projects: projects, blogs: blogs,});
                        }
                    });
                }
            });
        }
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是我能够重构的。

```
router.get(‘/showUser/:id’, function(req, res) {
    User.findById(req.params.id).populate(‘projects blogs’).exec(function(err,                 
        userRef) {
            if (err) {
                console.log(err);
            } else {
        Project.find(function(err, projects) {
            if (err) {
            console.log(err);
            } else {
                Blog.find(function(err, blogs) {
                  res.render(“showUser”, {userRef: userRef, projects: projects, blogs: blogs,});
                });
            }
        });
      }
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

之前，我必须检查用户是否登录，并检查它是否与我的外部用户 id 匹配。我不得不这样做，因为在用户登录之前，我不能引用用户对象，除非有一组复杂的路由和回调来检查外部系统对用户的引用。它更简洁，不像是把任何理智的人逼到边缘的回调地狱。我很高兴我不用再那样做了。Passport.js Yall。

这只是一个例子，但是在代码库中有很多小东西，现在已经变得非常小了。

起初，我的进度很慢，花了几个小时才在这个项目上站稳脚跟。我想过放弃。重写了整件事。但我很高兴我坚持下来了。经过几个小时的摆弄，我回到了正常的，稳定的步伐。当然，一个学习的经历，当她回来的时候我会发布一个纲要。

我知道所有这一切的问题是，这都是在回调，这对于这个项目来说很好，但可能会有点地狱，所以在未来，它将是承诺和异步等待前进。

如果你想看的话。

[http://ec2-13-57-104-16.us-west-1.compute.amazonaws.com/](http://ec2-13-57-104-16.us-west-1.compute.amazonaws.com/)

我的作品集
[https://jbull.co](https://jbull.co)