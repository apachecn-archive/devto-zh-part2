# 以卡带播放器为例解释基本的 HTML DOM

> 原文：<https://dev.to/straleb/basic-html-dom-explained-using-a-cassette-player-as-example-5999>

[![alt text](../Images/f944f2eb91a1445387708e1967115f3e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vkfhrb5---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s8d4ibo8vb4htibsr0el.jpg)

对于包括我在内的中年人来说，看到上一个时代音乐的载体总是令人愉快的。当你从 A 页翻到 B 页去听磁带上剩下的音乐时，总会有那种怀旧的感觉。所以我决定将这个项目献给那些时代。

## 简短的 HTML DOM 定义

有了 HTML DOM(文档对象模型),我们可以让 JavaScript 修改 CSS、HTML，添加或删除对象，移动对象等。今天我将向你展示我认为是基本的 HTML DOM 示例，我希望它能对你有所帮助。

## 设计和代码

对于这个项目，我决定使用一个带卡带播放器的音乐系统，在 HTML 代码中我已经放了左扬声器、右扬声器和主体，并且大部分都用 HTML 代码中的注释进行了解释。播放和暂停按钮是工作的，而其余的按钮是装饰性的。我为按钮使用了基本的键盘形状，是的，我可以用 CSS 来绘制它们，但是因为这是一个个人项目，所以我决定使用键盘按钮的形状。下面的 HTML 代码是这样的:

```
<div class="player-container" style="width:1050px ">
 <!--Left speaker of the music system-->
 <section class="left-speaker">
 <img src="https://preview.ibb.co/bY9thd/0018207_fpr_series_fpr12_02_12_inch_400w_rms_2_shallow_mount_car_audio_subwoofer.png" alt="left-woofer" height="130" width="130">
<img src="https://preview.ibb.co/n6mENd/GTO429_sq_001_1_3000x3000_300dpi_dv_HAMaster.png" alt="left-speaker" height="240" width="240">
     </section>
 <!--The main edit of the music system and its design-->
   <section class="main">
   <div class="left-border"></div>
   <img src="https://orig00.deviantart.net/2c76/f/2013/103/8/f/equalizer_gif_by_harlequinamidst-d61k0al.gif" alt="radio-menu" height="100" width="160" style="margin: 70px -80px; display: none; border: 1px solid black;">
        <img src="https://image.ibb.co/d7YVA8/equalizer_gif_by_harlequinamidst_d61k0al.jpg" alt="beginner-Phase"height="100" width="160" style="margin: 70px -80px; border: 1px solid black;" >
 <div class="outerCircle"><div class="inner-circle"></div></div>   
<div class="volume"><div class="volume-circle"></div></div>
<div class="right-border"></div>
<!--Decoration buttons-->
<div class="buttons">
       <button></button>
       <button></button>
       <button></button>
       <button></button>
             </div>
<!--Positioning the cassete player-->
    <div class="Casette-and-Buttons">
 <img src="https://preview.ibb.co/ghdUxd/compact_cassette_157537_960_720.png" height="85" width="130" alt="tape" style="margin:128px -62px">
 <img src="https://image.ibb.co/nwmv3J/roll_1.png" id="roll" height="15" width="15" style="margin:166px 11px;">
  <img src="https://image.ibb.co/nwmv3J/roll_1.png" id="scndroll" height="15" width="15" style="margin:166px -83px;">
  <img src="https://image.ibb.co/mSYCHd/boombox_wallpaper_HD3.png" alt="boombox_wallpaper_HD3" border="0" height="100" width="160" style="margin:120px 17px; box-shadow: 0px 1px 4px 0px black">
    <div class="tape-buttons">
    <button id="play"onmousedown="music.play()">></button>
     <button> << </button>
     <button> >> </button>
    <button  style="color: red;"> • </button>
    <button onmousedown="music.pause();" id="pause"> ||</button>
     <button style="height: 30px; font-size: 10px;">  ⬛ </button>
     </div>
     </div>

    </section>
   <!--Right speaker of the music system-->
 <section class="right-speaker">
  <img src="https://preview.ibb.co/bY9thd/0018207_fpr_series_fpr12_02_12_inch_400w_rms_2_shallow_mount_car_audio_subwoofer.png" alt="right-woofer" height="130" width="130">
  <img src="https://preview.ibb.co/n6mENd/GTO429_sq_001_1_3000x3000_300dpi_dv_HAMaster.png" alt="right-speaker"  height="240" width="240">
        </section>

            <!--Legs of the system-->
  <div class="legs" style="margin: 0px 10px;"></div>
  <div class="legs"  style="margin: -20px 230px;"></div>
  <div class="legs"  style="margin: 0px 320px;"></div>
  <div class="legs"  style="margin: -20px 680px;"></div>
  <div class="legs"  style="margin: 0px 770px;"></div>
  <div class="legs"  style="margin: -20px 990px;"></div>
    </div> 
```

## 造型

在卡带播放器的 CSS 设计中，我为卡带固定器添加了一些照片编辑，并在卡带上打了一个洞，这样我就可以在按钮激活时添加一些动画效果，我将在 JavaScript 代码中向您展示，我不会向您展示 CSS 的更大细节，因为主要主题是 HTML DOM，您可以在 Codepen 页面上找到更多细节，我将在页面末尾添加其链接。但首先向您展示一些 CSS 代码:

```
.left-speaker{
    float: left;
    background:rgb(48, 48, 48);
    height: 610px;
    width:300px;
    text-align: center;
    box-shadow: inset 1px 1px 10px 0px rgba(0, 0, 0, 0.548);
}

.main{
    width:440px;
    height: 610px;
    background:rgb(48, 48, 48);
    margin: 0 auto;
    box-shadow: inset 4px 1px 8px 0px rgba(0, 0, 0, 0.555);
   }

.right-speaker{
    float: right;
    background:rgb(48, 48, 48);
    height: 610px;
    width:300px;
    margin:-610px 0;
    text-align: center;
    box-shadow: inset 4px 2px 10px 0px rgba(0, 0, 0, 0.548);
} 
```

在定位和设计了播放器的两侧和中间之后，是时候将进一步的细节放入主板了，那就是装饰按钮、音量和收音机搜索轮，以及均衡器动画的定位。这里有一个关于 CSS 的简短观点

```
 .outerCircle{
    background: linear-gradient(rgb(117, 117, 117),rgb(117, 117, 117),white,rgb(117, 117, 117),rgb(117, 117, 117));
    height: 50px;
    width: 50px;
    border-radius: 80px;
    margin: -60px 198px;
    text-align: center;
    box-shadow: 1px 1px 8px 1px rgba(0, 0, 0, 0.658);
    border:1px solid black;
}

.inner-circle{
height:35px;
width: 35px;
border-radius:90px;
border:1px solid black;
margin:5px auto;
box-shadow: 1px 3px 5px 1px black;
background: linear-gradient(rgb(117, 117, 117),rgb(117, 117, 117),white,rgb(117, 117, 117),rgb(117, 117, 117));
}

.volume-circle{
height:50px;
width: 50px;
border-radius:90px;
border:1px solid black;
margin:5.5px auto;
box-shadow: 1px 3px 5px 1px black;
background: linear-gradient(rgb(117, 117, 117),rgb(117, 117, 117),white,rgb(117, 117, 117),rgb(117, 117, 117));
}

button{
    height:8px;
    padding:2px 3px;
    border-radius: 80px;
    box-shadow: 1px 1px 4px 0px black;
    background:linear-gradient(rgb(117, 117, 117),white);
    border:1px solid rgb(117, 117, 117);
    margin: 0px 2px;
     } 
```

在进一步设计了颜色和背景之后，音乐系统看起来应该是这样的:

[![alt text](../Images/d725c7fc41120cedb633cc64563f972c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WroSJ9hm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/dsSaQJ/vvzxvz.png)

最后是使用 HTML DOM 的时候了，有了 DOM 我们就可以使用动画和导入歌曲了。为了导入一首歌，我使用了 soundjay.com，它有很多免费的音乐效果和歌曲，请随意查看，在代码中我展示了用 JavaScript 导入声音的主要例子。
回到主题，我使用了 const 声明，并选择了我放在 HTML 中的元素和 ID。
通过导入声音，我设置了一个 playBtn 点击事件及其函数，我命名为 playClick。所以基本上当我点击播放按钮时，下面的事件将会发生，CSS 也将发生变化。

```
//Setting up the animation to react on the play button
const animation = document.querySelector("#roll");
const scndAnimation = document.querySelector("#scndroll")
const noEqualizer = document.querySelector('img[alt="beginner-Phase"]');
const equalizer = document.querySelector('img[alt="radio-menu"]');
const playBtn = document.querySelector("#play");

//Importing music
//Basic way of importing a song or a music tone
const music = new Audio();
music.src = "https://www.soundjay.com/free-music/sounds/iron-man-01.mp3";

//Triggering the click event 

playBtn.addEventListener("mousedown", playClick);

function playClick(){
    animation.style.animationName = "spin";
    scndAnimation.style.animationName = "spin";
    equalizer.style.display = "inline";
    noEqualizer.style.display = "none";

}

//Setting up the pause button

const pauseBtn = document.querySelector("#pause");

pauseBtn.addEventListener("mousedown", pauseClick);

function pauseClick(){
    animation.style.animationName = "none";
    scndAnimation.style.animationName = "none"
    equalizer.style.display = "none";
    noEqualizer.style.display = "inline";
} 
```

按下暂停按钮 pauseClick 功能将被激活，当按钮被激活时，事件和 CSS 更改将暂停，这样我们就激活了播放和暂停按钮。最后，完成的项目看起来是这样的:

[https://codepen.io/SBabic/full/yEJBgy/%20?height=500&default-tab=result&embed-version=2](https://codepen.io/SBabic/full/yEJBgy/%20?height=500&default-tab=result&embed-version=2)

**注意**这款笔针对台式机和笔记本电脑进行了优化，这里的屏幕很小，你可以滚动到按钮，或者在 CodePen 上查看完整的项目，我强烈推荐这款笔🙂

感谢您花时间阅读这篇文章，我真诚地希望您喜欢它，如果您在理解 DOM 时有任何困难，希望这篇文章能够以任何方式帮助您。🙂