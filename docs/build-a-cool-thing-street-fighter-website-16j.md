# 建立一个很酷的东西(街头霸王网站)

> 原文：<https://dev.to/annlin/build-a-cool-thing-street-fighter-website-16j>

[DEMO(你可以在这里看到现场直播。)](https://linxea.github.io)

## 今天怀旧深深地打击了我，

所以我做了一个关于我第一件很酷的事情的视频。谷歌搜索街头霸王的闪屏。整个构思都记录在下面的视频中。给我一些爱。<3

[![](img/cee39cbf10b75b4dae89d1994725b3d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K2AQu-Ia--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a5k775e84vmvka4orl2q.png) 
这个画面让我起鸡皮疙瘩。

[https://www.youtube.com/embed/hDIJ9xGjH1Q](https://www.youtube.com/embed/hDIJ9xGjH1Q)

## 加载动画

看看第一个加载动画，它试图模拟一个街机游戏屏幕上的启动效果。因此，我们做了 2px 的模糊和色调-旋转颜色到一个看起来不错的数字。做 800 毫秒，然后反方向做 500 毫秒。(我不知道我们可以倒着做。我什么都不知道。)

```
/* Stole this idea from twilioquest, it's freaking sick. */
.load {
  animation: load 800ms 500ms backwards;
}

@keyframes load {
  0% {
    filter: blur(2px) hue-rotate(-150deg);
  }
} 
```

## 街机复古画面叠加

谁会想到一行图像会引发怀旧之情，并成为一个很好的屏幕覆盖。我们实际上在屏幕上粘贴了一个半透明的背景图像，并使用`pointer-events: none`禁用其上的任何点击效果，这样它就不会阻止其他按钮的任何交互(不是说目前没有任何交互)。

Matthieu Pétel 给出了一个非常好的建议，使用`repeating-linear-gradient`代替 svg 来获得复古叠加效果。(我已经更新了代码，稍微调整了颜色)

```
/* Stole this from twilioquest too. ^^ */
body:before {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: auto;
  background: repeating-linear-gradient(#0D122B, #0D122B 2px, #565B73 2px, #565B73 4px);
  opacity: 0.2;
  z-index: 100;
  content: "";
  pointer-events: none;
} 
```

## 复古文字

谷歌字体是我们最好的朋友。我选择字体`Permanent Marker`，这是我能找到的最接近复制文本`Street Fighter`的字体，也是大多数复古游戏中使用的字体`Press Start 2P`。

### `SOFTWARE ENGINEER`

用一点`skew`和`rotate`和`translate`来倾斜文字`Software`和`Engineer`。我给它们单独的类名，因为我们想对它们进行稍微不同的调整。

```
.title-top {
  transform: skew(-10deg) rotate(-10deg) translateX(-2rem);
}
.title-bottom {
  transform: skew(-10deg) rotate(-10deg) translateX(1rem);
} 
```

裁剪背景看起来很酷，在 Safari、Chrome、Firefox 和 Opera 浏览器上都很好用。我很惊讶。

```
.title h1 {
  background: -webkit-linear-gradient(rgb(255, 18, 18) 30%, rgb(255, 215, 38));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
} 
```

我用`<span>`把一些字母设置得稍微大一点。所以看起来更像之前的闪屏。

### `PUSH START BUTTON`

使用文本阴影完成复古文本(`"PLAYER 1 OR PLAYER 2"`等)。一个文本阴影(在文本后面的右边和底部移动一点)在我看来足以复制街机风格的文本。我有两个文字阴影的信贷，使它“流行”。

```
.grey-with-red {
  color: rgb(192, 192, 192);
  text-shadow: 2px 2px 0 rgb(192, 11, 11);
}

.purple-with-blue {
  color: rgb(147, 154, 255);
  text-shadow: 2px 2px 0 rgb(47, 61, 255);
}

.yellow-with-darkyellow {
  color: rgb(226, 179, 24);
  text-shadow: 0 2px 0 rgb(107, 77, 11),
    2px 0 0 rgb(151, 109, 19),
    2px 2px 0 rgb(151, 109, 19);
} 
```

这里的源代码:
[https://codepen.io/toomuchome/embed/QZPYqm?height=600&default-tab=result&embed-version=2](https://codepen.io/toomuchome/embed/QZPYqm?height=600&default-tab=result&embed-version=2)

我将在下一篇文章中增加更多的互动。我现在要睡觉了。再见。

在[https://twitter.com/linxea_](https://twitter.com/linxea_)跟随我