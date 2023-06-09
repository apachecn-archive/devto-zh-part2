# 将 PostCSS 添加到 Create-React-App

> 原文：<https://dev.to/puritanic/add-postcss-to-create-react-app--5hj8>

> 不弹射！

#### post CSS 小介绍

你们中的许多人都在花时间使用 CSS，所以我猜你们对 Less、Sass 和 Stylus 等预处理程序很熟悉。这些工具是当今 web 开发生态系统的重要组成部分，我无法想象在不使用嵌套、变量或混合等功能的情况下为网站编写样式，因为这可能会很麻烦、笨拙，有时还不够直观。

但是，传统的预处理程序有几个问题:

*   他们不遵循 CSS 标准。你可能会说每个预处理程序都已经成为了它自己的标准。遗憾的是，它们的目标不是与 W3C 标准兼容，这意味着它们不能将它们的特性用作新 W3C 标准早期测试的聚合填充。
*   它们是不可扩展的。无论您选择哪种预处理器，您都会受到它所提供的特性集的限制。如果您在此之上还需要任何东西，那么您需要将它作为构建过程中的一个单独步骤来添加。如果你想写你的扩展，你只能靠自己了。祝你好运。

这就是 PostCSS 的用武之地。

简而言之，PostCSS 和 LESS/SASS/SCSS 做的是一样的事情...但是用几个插件增强它，它可以做得更多。
*PostCSS 很像 JavaScript 的巴别塔。*
它解析你的 CSS，在幕后使用 Javascript，把它变成原始的 AST(抽象语法树)，然后对当今浏览器能够理解和呈现的 CSS 进行转换。另外，JavaScript 可以比其他处理器更快地改变我们的风格。

关于 PostCSS 已经说得够多了，让我们把注意力集中在这篇文章的目的上。经过一番搜索，我发现要让 PostCss 工作，你必须弹出 CRA 来编辑底层的 Webpack 配置，它处理不同文件类型的所有必要的加载器，我发现这是一个有点激烈的措施。最后，经过一点点尝试和错误，我找到了不同的解决方案。祝您身体健康

你可以在这里找到最终代码:[https://github.com/Puritanic/CRA-feat-PostCSS](https://github.com/Puritanic/CRA-feat-PostCSS)。

> 编辑:看来我的 google foo 没有我想象的那么好。这里有一个用由[@ Jonathan tnale](https://dev.to/jonathantneal)创建的 PostCSS 扩展 CRA 的[替代方法](https://github.com/csstools/react-app-rewire-postcss)

首先，创建一个新的 React app:

```
create-react-app postcss-cra
cd postcss-cra 
```

并安装 postcss-cli 和一些基本插件

```
yarn add --dev autoprefixer postcss-nested postcss-cli npm-run-all 
```

然后，在项目的根目录下，您需要创建一个名为`postcss.config.js`的文件，并添加以下代码:

```
module.exports = {
    plugins: [
        require('postcss-nested'),
        require('autoprefixer'),
    ],
}; 
```

快到了！
现在剩下的就是编辑`package.json` :
中的脚本了

```
 "scripts": {
        "build:css": "postcss src/styles/main.css -o src/index.css",
        "watch:css": "postcss src/styles/main.css -o src/index.css -w",
        "start": "npm-run-all -p watch:css start-js",
        "start-js": "react-scripts start",
        "build-js": "react-scripts build",
        "build": "npm-run-all build:css build-js",
        "test": "react-scripts test --env=jsdom",
        "eject": "react-scripts eject"
        }, 
```

在`src`中创建一个样式文件夹，我们的 CSS 将存放在这里:

```
mkdir src/styles
mv src/App.css src/styles/main.css 
```

为了测试 postcss，让我们稍微修改一下默认的 CRA 样式:

```
/* src/styles/main.css */
.App {
  text-align: center;

    &-logo {
        animation: App-logo-spin infinite 20s linear;
        height: 80px;
    }

    &-header {
        background-color: #222;
        height: 150px;
        padding: 20px;
        color: white;
    }

    &-title {
        font-size: 1.5em;
    }

    &-intro {
        font-size: large; 
    }
}
@keyframes App-logo-spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
} 
```

> 不要忘记从 App.js 中删除`import './App.css';'`，因为我们已经将该文件移动到 styles 并将其重命名为`main.css`。

关键时刻到了。现在运行:`yarn run start`。
在浏览器中，您应该会看到几乎与 CRA 相同的默认风格。现在让我们看看`src/` :
中的输出`index.css`文件

```
.App {
    text-align: center;
    display: flex;
    flex-direction: column;
}

.App-logo {
        -webkit-animation: App-logo-spin infinite 20s linear;
                animation: App-logo-spin infinite 20s linear;
        height: 80px;
    }

.App-header {
        background-color: #222;
        height: 150px;
        padding: 20px;
        color: white;
    }

.App-title {
        font-size: 1.5em;
    }

.App-intro {
        font-size: large;
    }

@-webkit-keyframes App-logo-spin {
    from {
        -webkit-transform: rotate(0deg);
                transform: rotate(0deg);
    }
    to {
        -webkit-transform: rotate(360deg);
                transform: rotate(360deg);
    }
}

@keyframes App-logo-spin {
    from {
        -webkit-transform: rotate(0deg);
                transform: rotate(0deg);
    }
    to {
        -webkit-transform: rotate(360deg);
                transform: rotate(360deg);
    }
} 
```

瞧啊。如您所见，我们的样式会自动添加供应商前缀以获得更好的支持，并且我们的嵌套代码会转换为浏览器可以理解的代码。

现在让我们做一些更高级的事情:

```
yarn add -D postcss-import postcss-preset-env 
```

之后，让我们把那些新插件添加到`postcss.config.js` :

```
module.exports = {
    plugins: [
        require('postcss-import'),
        require('postcss-preset-env')({
            stage: 1,
        }),
        require('postcss-nested'),
        require('autoprefixer'),
    ],
}; 
```

现在，在 styles 文件夹中创建一个测试文件:

```
touch src/styles/styles.css src/styles/styles1.css 
```

将所有内容从`src/styles/main.css`移动到 styles.css 并添加这个:

```
@import 'styles.css';
@import 'styles1.css'; 
```

现在，在`src/styles/styles1.css`中添加这个奇怪的晦涩难懂的:

```
@custom-media --viewport-medium (width <= 50rem);
@custom-selector :--heading h1, h2, h3, h4, h5, h6;

:root {
    --fontSize: 1rem;
    --mainColor: #12345678;
    --secondaryColor: lab(32.5 38.5 -47.6 / 90%);
}

html {
    overflow: hidden auto;
}

@media (--viewport-medium) {
    body {
        color: var(--mainColor);
        font-family: system-ui;
        font-size: var(--fontSize);
        line-height: calc(var(--fontSize) * 1.5);
        overflow-wrap: break-word;
        padding-inline: calc(var(--fontSize) / 2 + 1px);
    }
}

:--heading {
    margin-block: 0;
}

.App-header:matches(header, .main) {
    background-image: image-set('./img/background.jpg' 1x, './img/background-2x.jpg' 2x);
    background-size: contain;
}

a {
    color: rebeccapurple;

    &:hover {
        color: color-mod(var(--secondaryColor) b(15%) a(75%));
    }
} 
```

现在重新启动 CRA 服务器。一切都应该像预期的那样工作，除了几个(必须的)猫显示在标题现在:D

这只是表面上的一点点，PostCSS 在其插件中有巨大的力量，并且有一个很好的社区围绕着它。最棒的是，大多数 PostCSS 插件使用的是将来会被用作原生 CSS 语法的东西，所以你的代码是面向未来的。最后，我很享受使用它，这才是最重要的，我希望你也一样。感谢阅读！

部分资源:
[官方 postscs Github Repo](https://github.com/postcss/postcss)和[页面](https://postcss.org/)
[postscs-preset-env 游乐场](https://preset-env.cssdb.org/)
[牛逼 postscs 插件列表](https://github.com/jdrgomes/awesome-postcss)