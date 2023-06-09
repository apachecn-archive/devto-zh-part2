# 使用 Elm 0.19 和 Furtive(使用 Sass)测试运行包

> 原文：<https://dev.to/peteraba/test-running-parcel-with-elm-019-and-furtive-with-sass-5e8d>

## 前提

所以[本](https://dev.to/ben)前几天开始了关于包裹的[讨论。我没有参与其中，主要是因为我从来没有听说过包裹。](https://dev.to/ben/im-having-some-not-this-again-feelings-with-parcel-how-should-i-be-feeling-about-this-tool-241p)

然而，我确实打开了[项目网站](https://parceljs.org/),它让我真正感兴趣有两个原因:首先，它似乎比 webpack 简单得多，其次，它承诺比 webpack 快得多。现在我的 webpack 体验仅略高于我所描述的高级初学者，尽管我已经用它开始了几十个项目，并在 Youtube 和 Udemy 上观看了许多使用它的课程。然而，大多数时候我只是从复制别人精心制作的东西开始，并根据我的需要进行调整。我不会说我不知道自己在做什么，但我肯定没有掌握这个工具。
我过去复制过一些真正有见识的人的 webpack 设置，但我总觉得最终的结果有些笨拙而且非常慢，因此我决定尝试一下 package。为了让事情变得更有趣，我决定尝试用[榆树](https://elm-lang.org/)T5】0.19 和[鬼鬼祟祟](http://furtive.co)来设置它

我今天早上刚刚安装了这个工具，所以我不会尝试评估它在实际项目中的性能，但是到目前为止，它确实感觉要快得多。然而我要说的是，即使对我来说，即使是这样一个奇怪的设置，设置它也是轻而易举的事情。让我带你看看:

## 路径#1:快速而肮脏的方式(< 2 分钟)

### 1。克隆我的回购

```
git clone https://github.com/peteraba/elm0.19-furtive-parcel.git 
```

Enter fullscreen mode Exit fullscreen mode

### 2。安装缺少的依赖项

```
yarn install 
```

Enter fullscreen mode Exit fullscreen mode

## 途径 2: DIY(约 10 分钟)

### 1。创建新的项目目录

```
mkdir path/to/your/project
cd path/to/your/project 
```

Enter fullscreen mode Exit fullscreen mode

### 2。初始化您的项目

```
yarn init -y 
```

Enter fullscreen mode Exit fullscreen mode

### 3。加偷偷摸摸

为了这个项目，我决定选择一个叫做 [Furtive](http://furtive.co) 的小型前端框架来保持速度。我可以只包含一个来自 CDN 的最小化 css，但是我想测试包，所以我决定添加 Sass 编译:

```
yarn add node-sass
yarn add furtive 
```

Enter fullscreen mode Exit fullscreen mode

### 4。添加 Elm 支持

*更新(2018-09-20)* :

这部分变得容易多了，因为 0.19 的支持现在被添加到[包-插件-榆树](https://github.com/ssuman/parcel-plugin-elm)中，简单地用 yarn 添加它:

```
yarn add parcel-plugin-elm 
```

Enter fullscreen mode Exit fullscreen mode

现在这部分有点棘手，因为我想用最新的 Elm 0.19，而包的主要 Elm 插件已经过时了。对于 0.19，有一个来自[本汉纳](https://twitter.com/benpaulhanna)的[拉请求](https://github.com/ssuman/parcel-plugin-elm/pull/10)，但是它还没有合并。此外，我不是一个真正的纱线专家，所以我必须查找如何使用他的代码，而不是官方的。好吧，虽然只花了 2 分钟，但是..

~~所以基本上我下载了本的代码，并将其解压到一个名为`outdated`的目录中，然后通过 yarn ( *yarn add file:。/过时的/parcel-plugin-elm* 。这应该提醒我，一旦变更被合并，就使用主回购。~~

### 5。初始化您的 elm 项目

package 还不知道 Elm，所以我们大概应该在本地安装:

```
yarn add elm@^0.19.0-bugfix2 
```

Enter fullscreen mode Exit fullscreen mode

然后我们也可以初始化我们的 Elm 项目:

```
yes | ./node_modules/.bin/elm init 
```

Enter fullscreen mode Exit fullscreen mode

#### *可选:*全局榆树安装

如果你没有安装 Elm 0.19，但想玩玩它，请在[官方指南](https://guide.elm-lang.org/)中找到[安装说明](https://guide.elm-lang.org/install.html)。

### 6。创建项目文件

#### `sass/main.scss`

```
$font-family: 'Lato', Helvetica, sans-serif;
$dark-gray: #222;
$text-color: $dark-gray;
$body-bg: #fafafa;

@import "node_modules/furtive/scss/all.scss";

.elm-container {
    margin: 5px 0;

    button, div {
        display: inline-block;
        margin: 2px;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### `src/Main.elm`:

```
import Browser
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)
import Html.Attributes exposing (class)

main =
  Browser.sandbox { init = 0, update = update, view = view }

type Msg = Increment | Decrement

update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1

view model =
  div [class "elm-container txt--center"]
    [ button [ onClick Decrement, class "btn btn--blue btn--s" ] [ text "-" ]
    , div [] [ text (String.fromInt model) ]
    , button [ onClick Increment, class "btn btn--green btn--s" ] [ text "+" ]
    ] 
```

Enter fullscreen mode Exit fullscreen mode

#### `index.html`:

```
<html>
    <head>
        Hello, Parcel!
        <script src="./index.js"></script>
    </head>
<body>
    <main></main>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

#### `index.js`:

```
import './sass/main.scss'

if (module.hot) {
    module.hot.dispose(() => {
        window.location.reload();
    });
}

import('./src/Main.elm')
    .then(({ Elm }) => {
        var node = document.querySelector('main');
        Elm.Main.init({ node: node });
    }); 
```

Enter fullscreen mode Exit fullscreen mode

*注意:*中间的 if 部分是包裹问题的变通办法，可能很快就会过时。

## 最终结果

好了，现在我们一切就绪，让我们试一试:

```
➤ parcel index.html
Server running at http://localhost:1234 
Success! Compiled 1 module.                                          
Success! Compiled 1 module.                                          
✨  Built in 2.23s.
^C

➤ parcel index.html
Server running at http://localhost:1234 
✨  Built in 724ms.
^C 
```

Enter fullscreen mode Exit fullscreen mode

没有缓存不到 3 秒，有缓存不到 1 秒，热重装按预期工作。然而更重要的是**我不需要编辑任何配置文件**。

## 结论

我不得不说，到目前为止，我真的对包裹印象深刻。它的入门指南很短，但它仍然让我实现了我想要的东西，一次也没有困惑或卡住。我想这可能只是一段美好友谊的开始...