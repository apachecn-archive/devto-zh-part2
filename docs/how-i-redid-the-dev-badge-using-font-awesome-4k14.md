# 我如何使用字体牛逼重做开发徽章[更新]

> 原文：<https://dev.to/themindfuldev/how-i-redid-the-dev-badge-using-font-awesome-4k14>

我刚刚重做了我的投资组合。

对于这个项目，我选择了[盖茨比](https://www.gatsbyjs.org)，因为我是[贾姆斯塔克](https://jamstack.org)和[反应](https://reactjs.org)的粉丝。

为了节省时间，我选择了一个 [Gatsby starters](https://gatsbyjs.org/docs/gatsby-starters/) ，这样我就可以专注于内容和风格的定制，而不是从头开始构建整个系统。

我偶然发现了一个主题，它装载了[字体牛逼](https://fontawesome.com)，确实牛逼！

> 编辑:字体牛逼现在有了一个[开发](https://fontawesome.com/icons/dev?style=brands)的图标！写这篇文章的时候，图标还不可用，所以我使用分层功能从头开始构建它，这就是我下面演示的只是为了说明的目的，我只是不建议使用它来添加 DEV 到您的网站，而是使用字体真棒图标。

然后我想把我的社交资料放在一起。我想为我的[开发者添加一个徽章来描述](https://dev.to/themindfuldev)，我发现有一个我们可以使用的 SVG，就像[上指示的那样将开发者徽章添加到你的个人网站](https://dev.to/p/badges)。

然而，我注意到它不符合其他字体图标的外观和感觉，特别是因为我的主题有不同的颜色和大小。

我几乎认为这个事实是理所当然的，当我偶然发现字体 Awesome 5 的一个新功能:[分层](https://fontawesome.com/how-to-use/on-the-web/styling/layering)。

我立刻接受了挑战，用字体 Awesome 构建了一个开发图标，这样它看起来就像我作品集上的其他图标。

我使用的是[操纵杆](https://www.dafont.com/joystick.font)字体， [@ben](https://dev.to/ben) 告诉我，我们购买了一个许可证，因此任何人都可以使用它来重新创建我们的标志。

所以你需要把它包含在你的代码中，就像我下面做的，我把它放在 GitHub 的投资组合回购上， [gatsby-tiagorg](https://github.com/themindfuldev/gatsby-tiagorg) :

```
/* CSS */
@font-face {
  font-family: "Joystick";
  src: url("https://raw.githubusercontent.com/themindfuldev/gatsby-tiagorg/master/src/assets/fonts/Joystick.otf");
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是我在 React 中使用`FontAwesomeIcon`组件的代码，通过 [react-fontawesome](https://github.com/FortAwesome/react-fontawesome) :

```
// JSX 
<span className="fa-layers fa-4x">
  <FontAwesomeIcon icon="square" style={{ transform: "scaleX(1.4)" }} />
  <span className="fa-layers-text" style={{ 
    fontFamily: "Joystick", 
    fontSize: "0.6em", 
    color: "white", 
    padding: "2px 0 0 2px"
  }}>DEV</span> </span> 
```

Enter fullscreen mode Exit fullscreen mode

如果你不使用 React，你可以用普通的 HTML 和 CSS 实现同样的事情:

```
/* CSS */
@font-face {
  font-family: "Joystick";
  src: url("https://raw.githubusercontent.com/themindfuldev/gatsby-tiagorg/master/src/assets/fonts/Joystick.otf");
}

.icon-bg {
  transform: scaleX(1.4);
}

.icon-fg {
  font-family: Joystick;
  font-size: 0.6em;
  color: white;
  padding: 2px 0 0 2px;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- HTML -->
<span class="fa-layers fa-4x">
  <i class="fas fa-square icon-bg"></i>
  <span class="fa-layers-text icon-fg">DEV</span>
</span> 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这个代码笔中看到它的工作原理:

[https://codepen.io/themindfuldev/embed/MqmxdP?height=600&default-tab=result&embed-version=2](https://codepen.io/themindfuldev/embed/MqmxdP?height=600&default-tab=result&embed-version=2)

PS:你需要使用 SVG + JS 版本的字体 Awesome 才能做到这一点。