# 自举爱好者遇上 CSS FlexBox

> 原文：<https://dev.to/aritdeveloper/bootstrap-lover-meets-css-flexbox-3f3p>

自从我知道了 Bootstrap，我就把这个框架融入到了我所有的前端工作中。我喜欢能够轻松配置完全响应的布局。所以当我听说 Flexbox 时(想想吧，因为所有主要的布局框架[咳咳，Bootstrap]都使用 Flexbox)，我并没有太兴奋去尝试它。

直到我决定重新设计我的投资组合网站。我特别希望将那些与我的技能/工具相对应的徽标显示为一个网格。我下意识地想到的当然是 Bootstrap，但是配置起来有点困难。我的 logo 描述总是相互重叠，我的网格在不同的手机/平板电脑屏幕上显得过于混乱。但我最终让它工作了。我的 HTML/CSS 代码和结果视频如下:

```
<!-- HTML -->
<section class="skills" id="skills">
        <h1 style="margin: 40px 0px;">Skills &amp; Tools</h1>
        <div class="container">
          <div class="row">
            <div class="col-12 col-sm-6 col-md-4 skills-list">
              <span><img src="img/htmlcss.png" /><br />
              HTML/CSS</span>
            </div>
            <div class="col-12 col-sm-6 col-md-4 skills-list">
              <span><img src="img/js.png" /><br />
              JavaScript</span>
            </div>

            <!-- some more similar code -->

            <div class="col-12 col-sm-6 col-md-4 skills-list">
              <span><img src="img/trello.png" /><br />
              Trello</span>
            </div>
            <div class="col-12 col-sm-6 col-md-4 skills-list">
              <span><img src="img/slack.png" /><br />
              Slack</span>
            </div>
          </div>     
        </div>
      </section> 
```

Enter fullscreen mode Exit fullscreen mode

```
/* CSS */
.skills-list {
  line-height: 75px;
  font-size: 20px;
  text-align: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://player.vimeo.com/video/296328699](https://player.vimeo.com/video/296328699)

现在，我最近想起了 Flexbox，并想知道它是否会使我的网格工作得和 Bootstrap 一样好，甚至更好。我惊喜地发现，我只需要定义 flexbox 容器和我自己的网格元素。当我操纵屏幕大小时，flex-wrap 属性使我的网格排列更加平滑(参见下面的代码和视频)。

```
 <section class="skills" id="skills">
        <h1 style="margin: 40px 0px;">Skills &amp; Tools</h1>
        <div class="skills-container">
          <div class="skills-list">
            <span><img src="img/htmlcss.png" /><br />
            HTML/CSS</span>
          </div>
          <div class="skills-list">
            <span><img src="img/js.png" /><br />
            JavaScript</span>
          </div>

          <!-- some more similar code -->

          <div class="skills-list">
            <span><img src="img/redhat.png" /><br />
            RedHat/Centos</span>
          </div>
          <div class="skills-list">
            <span><img src="img/wordpress.png" /><br />
            Wordpress</span>
          </div>   
        </div>
      </section> 
```

Enter fullscreen mode Exit fullscreen mode

```
.skills-list {
  line-height: 75px;
  font-size: 20px;
  text-align: center;
  width: 175px;
}

.skills-container {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://player.vimeo.com/video/296328665](https://player.vimeo.com/video/296328665)

CSS Flexbox 现在让我兴奋😆🤩感觉很直观，我期待着用它来配置我未来的更多布局。

**您是如何使用 Flexbox 的？比起其他布局框架，你更喜欢它吗？为什么或为什么不？**

(感谢阅读！👋🏾)