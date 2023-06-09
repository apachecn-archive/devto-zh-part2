# 在脸书即时游戏上发布 react 应用

> 原文：<https://dev.to/abhishekcode/publish-react-appcreate-react-app-on-facebook-instant-game--6j4>

**背景**:

我是前端开发人员，我在日常工作中使用 ReactJS，但在这之前我是一名游戏开发人员，我仍然喜欢制作游戏，特别是我可以快速制作的小型 html5 游戏。为此，我不使用游戏引擎，我通常更喜欢创建-反应-应用程序，这是制作小游戏的好方法。

所以我做了[这个游戏](https://abhishekcode.github.io/word-tetris/)。我想在 facebook 上分享它，在搜索时我发现了 facebook 的即时游戏功能，你可以发布 html5 游戏，用户可以直接从 messenger 和 facebook 上玩游戏。所以我决定在那里发表。

**脸书即时游戏的设置和 CRA 版本的问题**

设置非常简单，按照官方文档[的要求](https://developers.facebook.com/docs/games/instant-games/getting-started/quickstart)只需包含 SDK 并初始化脚本，然后开始加载游戏。

但是默认情况下，create-react-app 会生成一个 index.html 和缩小的 JS 文件。其中 JS 文件被直接调用，所以默认情况下这在即时游戏上不起作用。

在即时游戏中，我们必须首先初始化 sdk，然后只有我们可以加载我们的应用程序的 javascript。

**解决方案**

所以我玩了一下由 create-react-app 生成的构建文件，并对 index.html 文件做了如下修改，使其能在即时游戏中运行。

1)删除加载缩小 JS 文件的脚本标签。
一般是在 index.html 的尽头。我的看起来像这样

```
<script type="text/javascript" src="/static/js/main.34f1754e.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

***PS:** `main.34f1754e.js`这是生成文件的名字，每次生成新的 build 都会不一样。*

**2)在 head** 中包含即时游戏 SDK

```
<script src="js/mock/fbinstant.6.0.mock.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

3)初始化并开始加载缩小的 JS。
我是这样做的，当我成功回调初始化时，我开始加载 create react 应用程序生成的缩小的 JS 包。

```
 <script>
    window.onload = function () {
        FBInstant.initializeAsync().then(function () {
            function loadScript() {
                return new Promise(function (resolve, reject) {
                    var s;
                    s = document.createElement('script');
                    s.src = "static/js/main.34f1754e.js";
                    s.onload = resolve;
                    s.onerror = reject;
                    document.head.appendChild(s);
                });
            }
            loadScript().then(res => console.log(res)).catch(err => console.log(err));
        }).catch(err => {
            console.log(err)
        });
    };

</script> 
```

Enter fullscreen mode Exit fullscreen mode

**结论**

这样我们就可以将 CRA 创建的 ReactJS 应用程序发布到 facebook 的即时游戏中。如果你对游戏开发感兴趣，并且发现学习游戏引擎很难(我实际上发现 phaser/Cocos 真的真的很难)，请从 CRA 开始，或者简单的 JS 也不错。CRA 的好处是你可以直接开始使用 ES6-:)。除非你打算制作图形密集型游戏，否则你并不需要游戏引擎

我没有将我的游戏发布到 FB Instant Game，因为后来我发现要真正发布，你需要提供 apple id，但我没有(:-
我认为 facebook 应该允许在 android 和 Web 上没有 apple Id 的即时游戏，我可以理解 iOS，但不管怎样，做这一切很有趣。

感谢您阅读本文。请在评论区告诉我你的想法。