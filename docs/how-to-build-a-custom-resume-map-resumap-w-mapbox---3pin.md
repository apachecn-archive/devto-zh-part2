# 如何使用映射框构建自定义简历映射🗺️

> 原文：<https://dev.to/mapbox/how-to-build-a-custom-resume-map-resumap-w-mapbox---3pin>

*我的同事[乔·克拉克](https://www.mapbox.com/about/team/joe-clark/)在 [Mapbox 博客](https://blog.mapbox.com/)上交叉发布了一个有趣的地图绘制活动，名为 [DIY Resumap](https://blog.mapbox.com/diy-resumap-cef7449e3293)* -

**什么**？Resumap！明白了吗？简历图……
T3】为什么是？从人群中脱颖而出-加上，测绘岩石。
**如何**？遵循此分步指南了解 web 制图简介

查看这里的演示，[https://mapbox-explorer.github.io/](https://mapbox-explorer.github.io/)

[![](img/d2804cccdc737f1a25ffe4d11ea44f3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5gDZ9tJj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_62E418C5B045FDF175E803788F0E70DDFB83A94380AD16621A9AE0428BEF1E0F_1524783098287_image.png)

## 1。登录到您的帐户

如果你刚刚加入我们，Mapbox 建立了开源软件，使地图制作变得容易。登录您的帐户或[在 4 秒内创建一个新帐户](https://www.mapbox.com/signup/)。

## 2。将上面的代码复制到文本编辑器中，如[崇高文本](https://www.sublimetext.com/)或[原子](https://atom.io/)

为了制作一个交互式 Resumap，我们将使用 Mapbox GL JS JavaScript 库中的代码。你可以阅读更多关于[它是如何工作的](https://www.mapbox.com/help/how-web-apps-work/#mapbox-gl-js)或者相信我们，它是高性能的+初学者友好的🙂我们已经定制了从“[飞到基于滚动位置](https://www.mapbox.com/mapbox-gl-js/example/scroll-fly-to/)的位置”的 Resumap 代码。

如果您有构建网站的经验，请随意复制这个片段，添加您自己的访问令牌，并在您想要的任何地方使用它。如果您是 web 开发新手，或者没有太多编码经验，请继续阅读！

```
<!DOCTYPE html>
<html>
<head>
    <meta charset='utf-8' />
    
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.tiles.mapbox.com/mapbox-gl-js/v0.44.1/mapbox-gl.js'></script>
    <link href='https://api.tiles.mapbox.com/mapbox-gl-js/v0.44.1/mapbox-gl.css' rel='stylesheet' />
    <style>
        body { margin:0; padding:0; }
        #map { position:absolute; top:0; bottom:0; width:100%; }
    </style>
</head>
<body>

<style>
#map {
    position: fixed;
    width:50%;
}
#features {
    width: 50%;
    margin-left: 50%;
    font-family: Tw Cen MT;
    overflow-y: scroll;
    background-color: #fafafa;
}
section {
    padding:  25px 50px;
    line-height: 25px;
    border-bottom: 1px solid #ddd;
    opacity: 0.25;
    font-size: 20px;
}
section.active {
    opacity: 1;
}
section:last-child {
    border-bottom: none;
    margin-bottom: 1000px;
}
</style>

<div id='map'></div>
<div id='features'>
    <section id='welcome' class='active'>
        <h3>Hey, there!</h3>
        <p>Welcome to a resumap! A resume can only say so much. <br><br>🤖 And they tend to feel robotic, don't they? <br><br>Resumaps are designed to fill the gaps in your resume. If you want to stand out, a resumap is a fun way to help people get to know you. After all, wouldn't you want to know who you're hiring? So if you want see more, sit back, relax, and enjoy the flight 🚀</p>
    </section>
    <section id='section1' class='active'>
        <h3>Section 1</h3>
        <p>Here's where you can add a narrative to take your resume to the next level. Where did you come from? What really brought you there? What made you leave? This is to let people get a glimpse of who the person behind the paper is. This is New Mexico...but you can center your map however you want.
    </section>
    <section id='section2'>
        <h3>Section 2</h3>
        <p>As your readers scroll through these sections, your resumap will fly them to the locations you've specified. A sense of place is something that a lot of people resonate with. Where you were when you did what you did is half of the story.<br><br>Add some line breaks to start a new paragraph if you want to feel fancy.</p>
    </section>
    <section id='section3'>
        <h3>Section 3</h3>
        <p>You can add pictures! <br><br> <img src="https://i.imgur.com/NF8af.jpg"><br><br></p>
    </section>
    <section id='section4'>
        <h3>Section 4</h3>
        <p>Or even gifs! <br><br><iframe src="https://giphy.com/embed/fpXxIjftmkk9y" width="224" height="240" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p></p>
        </p>
    </section>
    <section id='section5'>
        <h3>Section 5</h3>
        <p>Or videos! WHAT!<br><br><iframe width="560" height="315" src="https://www.youtube.com/embed/le0BLAEO93g" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe> </p>
    </section>
    <section id='section6'>
        <h3>Section 6</h3>
        <p>The most important thing is to tell <i>your</i> story. What makes you you?</p>
    </section>
    <section id='section7'>
        <h3>Time to make your own</h3>
        <p>Have fun!<br><br><iframe src="https://giphy.com/embed/lJNoBCvQYp7nq" width="300" height="300" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p></p></p>
    </section>
</div>
<script>
mapboxgl.accessToken = 'PASTE YOUR ACCESS TOKEN HERE';
var map = new mapboxgl.Map({
    container: 'map',
    style: 'mapbox://styles/mapbox/outdoors-v10',
        bearing: -0,
        center: [-108.866174, 49.272291],
        zoom: 2,
        speed: 0.8,
        pitch: 0
});
var chapters = {
    'welcome': {
        bearing: -0,
        center: [-108.866174, 49.272291],
        zoom: 2,
        speed: 0.8,
        pitch: 0
    },
    'section1': {
        bearing: 0,
        center: [-105.391504, 34.371086],
        zoom: 6.00,
        pitch: 0
    },
    'section2': {
        center: [-122.316235, 47.605958],
        bearing: 54.40,
        zoom: 12.59,
        speed: 0.6,
        pitch: 44.50
    },
    'section3': {
        bearing: 12.80,
        center: [-0.075681, 51.498018],
        zoom: 13.18,
        speed: 0.6,
        pitch: 0.00
    },
    'section4': {
        bearing: 60,
        center: [-134.408720, 58.300388],
        zoom: 16.57,
        speed: 0.6,
        pitch: 45
    },
    'section5': {
        bearing: 15.20,
        center: [29.027289, 41.013899],
        zoom: 10.56,
        pitch: 40.50,
        speed: 0.6
    },
    'section6': {
        bearing: 0,
        center: [-63.594167, -17.701427],
        zoom: 5.53,
        pitch: 0,
        speed: 0.6
    },
    'section7': {
        bearing: -0,
        center: [27.230526, 0.000000],
        zoom: 1.18,
        speed: 0.8,
        pitch: 0
    },
};
// On every scroll event, check which element is on screen
window.onscroll = function() {
    var chapterNames = Object.keys(chapters);
    for (var i = 0; i < chapterNames.length; i++) {
        var chapterName = chapterNames[i];
        if (isElementOnScreen(chapterName)) {
            setActiveChapter(chapterName);
            break;
        }
    }
};
var activeChapterName = 'baker';
function setActiveChapter(chapterName) {
    if (chapterName === activeChapterName) return;
    map.flyTo(chapters[chapterName]);
    document.getElementById(chapterName).setAttribute('class', 'active');
    document.getElementById(activeChapterName).setAttribute('class', '');
    activeChapterName = chapterName;
}
function isElementOnScreen(id) {
    var element = document.getElementById(id);
    var bounds = element.getBoundingClientRect();
    return bounds.top < window.innerHeight && bounds.bottom > 0;
}
</script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

## 3。从您的帐户添加一个[访问令牌](https://www.mapbox.com/help/define-access-token/)

复制您的[帐户仪表板](https://www.mapbox.com/account)顶部的访问令牌，并将其粘贴到您的代码中，您会看到:

```
mapboxgl.accessToken = 'PASTE YOUR ACCESS TOKEN HERE'; 
```

Enter fullscreen mode Exit fullscreen mode

一定要保留撇号！

## 4。定位您的地图

你会希望你的简历放在你的浏览者正在阅读的地方的中央。您可以使用 Mapbox Studio 的[样式编辑器](https://www.mapbox.com/studio/styles/)中的`Map position`菜单来获得您需要的确切的`longitude`、`latitude`、`zoom`、`bearing`和`pitch`:

[![](img/417b9e1c6df91b6b4e7eca44a01715a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pQnkUbix--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_5F1878FF0C2D60C2D208C17E2D26761CCCA2A8395B273B0AB9878B776D8AD47A_1524590500528_map%2Bposition.gif)

回到你的文本编辑器中，你会看到一个专门用来在你的代码中映射位置的部分。这是您想要输入缩放级别、经度、纬度、俯仰和方位的地方。看起来是这样的:

```
var chapters = {
    'welcome': {
        bearing: -0,
        center: [-108.866174, 49.272291],
        zoom: 2,
        speed: 0.8,
        pitch: 0
    },
    'place1': {
        center: [-71.007520, 42.340921],
        bearing: -150,
        zoom: 16.72,
        speed: 0.6,
        pitch: 45
    },
    'place2': {
        bearing: -45,
        center: [-68.899502, 46.030150],
        zoom: 15.99,
        speed: 0.6,
        pitch: 45
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个细目分类:

*   `center`是您用地图定位工具找到的经度和纬度(按此顺序)
*   你希望你的简历以多快的速度到达那个位置
*   按照相同的格式添加任意多个位置
*   摆弄数字，让`fly to`函数按照你想要的方式工作。当你的读者滚动到简历中的下一部分(或`chapter`)时，他们也会飞到你指定的位置。

## 5。将地图发布到您自己的网站

如果你已经有了一些简单的编码经验，请随意在任何地方发布你的简历。如果你没有建立网站的经验，那也没关系- [GitHub](http://github.com/) 让你轻松开始 web 开发！

*   为了[我的个人简历](https://joecclark.github.io/)，我按照 [GitHub 的教程](https://pages.github.com/)用 GitHub 页面建立一个网站。
    *   对于初学者来说，GitHub 的教程会让你使用一个免费的网站，它的 URL 看起来像:`**https://**` `***username***` `**.github.io**`
*   你可以将第 3 步和第 4 步中的代码添加到你的`index.html`文件

    ## 6 中。分享你的成果

既然你已经创建并运行了一个简历，是时候与全世界分享它了。在你的个人文件夹中使用它，把它寄给潜在的雇主，或者用它来代替你的求职信。一定要在推特上炫耀一下 **@Mapbox #builtwithmapbox**

[![](img/f1673edf18e172547e0ef1f4288d7d90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pMoLooIF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_5F1878FF0C2D60C2D208C17E2D26761CCCA2A8395B273B0AB9878B776D8AD47A_1524591933548_flyto.gif)

## 额外积分-可选定制

得到了你的简历，想要升级吗？专业动作-

**定制风格🎨**

*   点击“**添加此样式**，将[设计师地图样式](https://www.mapbox.com/designer-maps/)添加到您的账户中
*   或者使用[图表](https://www.mapbox.com/cartogram)创建自己的风格，拖放照片
*   你会在你的[风格页面](https://www.mapbox.com/studio/styles/)上看到这些——点击“**分享开发和使用**

**自定义图标✨**

*   用图标为你的地图创造超级创意是可能的——我把我的 bitmoji 脸做成一个**。使用 [Inkscape](https://inkscape.org/en/) 将 svg 图标**上传到 Mapbox Studio
*   上传。svg 文件到 Mapbox Studio 中，并使用它们作为你的地图的标记，本教程，[https://www.mapbox.com/help/markers/](https://www.mapbox.com/help/markers/)
*   查看[标记操场](https://www.mapbox.com/help/marker-playground/)寻找灵感

请留下您的问题或在 twitter 上联系我们@Mapbox:)