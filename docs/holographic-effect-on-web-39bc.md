# Web 上的全息效果

> 原文：<https://dev.to/annlin/holographic-effect-on-web-39bc>

# [试玩](https://linxea.github.io/holographic-effect/)

## 全息效果超赞，

所以今天我要做自己版本的全息效果。

[https://www.youtube.com/embed/Ep6tvUKQ9MQ](https://www.youtube.com/embed/Ep6tvUKQ9MQ)

下面演示代码，在[手机浏览器](https://linxea.github.io/holographic-effect/)打开看看我的小成果。【T2[https://codepen.io/toomuchome/embed/VVpOOB?height=600&default-tab=result&embed-version=2](https://codepen.io/toomuchome/embed/VVpOOB?height=600&default-tab=result&embed-version=2)

## css

谷歌和颜色-随机挑选一张全息图片，为我们的`linear-gradient`选择可爱的颜色。将`background-size`设置为`200%`,这样我们可以稍后移动颜色。

```
#holographic {
    height: 100vh;
    width: 100vw;
    background-image: linear-gradient(
        to right,
        rgb(194, 255, 182),
        rgb(255, 163, 182),
        rgb(221, 169, 255),
        rgb(162, 209, 255)
    );
    background-size: 200%;
} 
```

## [探测装置方位](https://developer.mozilla.org/en-US/docs/Web/API/Detecting_device_orientation)

我使用`alpha`并将最大值`background-position`设置为`100%`，这样`linear-gradient`就不会从头开始重复。每个人都应该参加设备定位活动，它们非常有趣。

```
window.addEventListener("deviceorientation", handleOrientation, true);
      function handleOrientation(event) {
        const z = event.alpha; // rotation degrees from 0 to 360

        const holographicElement = document.getElementById("holographic");
        holographicElement.style.backgroundPosition =
          Math.min(Math.abs(z), 100) + "%";
} 
```

## 反馈

如果 kinnnndda 成功了，我想我们可以找到更好的计算方法，让它更流畅，更少错误。但现在，在我为自己设定的时间限制内，我对通过严格旋转或移动手机所实现的轻微全息效果相当满意。:>

是的，我在两个小时内做了一个视频。给我鼓掌。
[关注我](https://twitter.com/linxea_)，订阅我的 [YOUTUBE 频道](https://www.youtube.com/channel/UCA6sSSOlSJiQY2o10QF5KQw?view_as=subscriber)。
点这里点那里点到处点。