# 受 BBC 今日比赛启发的 CSS 动画

> 原文：<https://dev.to/deadlybyte/css-animations-inspired-by-bbcs-match-of-the-day-192o>

<figure>

[![MOTD Match Stat's Animation Sequence](../Images/bb1beda79138fa9e36d3b5af542d4278.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7-zUzNv2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/deadlybyte/motd-css-animations/raw/master/motd-match-stats-animation.gif)

<figcaption><abbr title="Match of the Day">MOTD</abbr> Match Stats Animation</figcaption>

</figure>

我是一个狂热的足球迷🐺/ <abbr title="Athletic Football Club ">亚足联</abbr>伯恩茅斯🍒)没有什么比周六晚上坐下来看英国广播公司<abbr title="British Broadcasting Corporation">的</abbr>电视台<abbr title="Match of the Day">的【MOTD】电视台</abbr>播出的英超集锦更好的了。在一场比赛的集锦之后，球员和教练被采访，当这些采访发生时，一系列的统计数据和信息被显示给观众。这是通过使用吸引关键信息注意力的奇特动画技术实现的。我一直对这个印象深刻，它看起来超级酷。

受到启发，我想通过 <abbr title="Cascading Style Sheets">CSS</abbr> 重新创建一些动画序列，所以不用 JavaScript。这是一个练习和学习新技能的好机会。下面的指南将介绍创建动画的步骤。

# 初始项目设置

为了简单起见，我们将创建一个名为 index.html*的 HTML 页面，该页面将引用所有的 T4 HTML 和 T6 CSS，因此没有 JavaScript。*

在*index.html*文件中，我们将创建一个包含类**容器**的 div 元素，它将包含一个包含类 **motd-card** 的子 div 元素。这个 div 元素将包含两个 div 元素，一个用于标题，另一个用于卡片的内容。

```
<div class="container">
    <div class="motd-card">
        <div class="motd-card-title">Match Stats</div>
        <div class="motd-card-body">
        </div>
    </div>
</div> 
```

带有类 **motd-card-body** 的 body div 将包含另外两个 div 元素，一个是带有类 **match** 的比赛细节，另一个是带有类 **attendance** 的参加比赛的人数。

```
<div class="container">
    <div class="motd-card">
        <div class="motd-card-title">Match Stats</div>
        <div class="motd-card-body">
            <div class="match">
                <!-- Add match markup here -->
            </div>
            <div class="attendance">Attendance: 30,544</div>
        </div>
    </div>
</div> 
```

具有类 **match** 的 div 将包含下面的标记来设置球队和得分数据的样式。

```
<span class="team">Brighton</span><span class="score">1-0</span><span class="team">West Ham</span> 
```

**注:**对不起西汉姆球迷，我用的例子是 2018 年 10 月 5 日布莱顿在美国运通球场 1-0 击败西汉姆的时候。

接下来，我们需要添加 <abbr title="Cascading Style Sheets">CSS</abbr> 样式，这样比赛统计数据的样式就正确了，看起来就像在<abbr title="Television">电视</abbr>上显示的那样📺。

```
html,
body {
    height: 100%;
}

body {
    background-color: #333;
    font-family: 'Ubuntu', Arial, Helvetica, sans-serif;
    margin: 0;
}

.motd-card {
    width: 275px;
}

.motd-card-title {
    background-color: #FFD130;
    color: #2B0400;
    font-weight: bold;
    padding: 8px 0 6px;
    margin: 0;
    overflow: hidden;
    text-align: center;
    text-transform: uppercase;
    white-space: nowrap;
}

.motd-card-body {
    background-color: #FFF;
    color: #2B0400;
    overflow: hidden;
}

.match {
    font-weight: bold;
    padding: 5px 0 0;
    text-align: center;
}

.team {
    text-transform: uppercase;
}

.score {
    background-color: #FFD130;
    padding: 2px 6px;
    margin: 0 6px;
}

.attendance {
    font-size: 0.75em;
    padding: 4px;
    text-align: center;
    text-transform: uppercase;
} 
```

确保在 <abbr title="Hypertext Markup Language">HTML</abbr> 的 head 标签中添加一个 link 元素，它链接到 [Google Font Ubuntu](https://fonts.google.com/specimen/Ubuntu) 。因此文本使用了与英国广播公司<abbr title="British Broadcasting Corporation">的</abbr>的 <abbr title="Match of the Day">MOTD</abbr> 相似的无衬线字体。

```
<link href="https://fonts.googleapis.com/css?family=Ubuntu" rel="stylesheet"> 
```

完成的*index.html*看起来应该如下图所示。

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <link href="https://fonts.googleapis.com/css?family=Ubuntu" rel="stylesheet">
        Match Of The Day CSS Animation
        <style>
            html,
            body {
                height: 100%;
            }

            body {
                background-color: #333;
                font-family: 'Ubuntu', Arial, Helvetica, sans-serif;
                margin: 0;
            }

            .motd-card {
                width: 275px;
            }

            .motd-card-title {
                background-color: #FFD130;
                color: #2B0400;
                font-weight: bold;
                padding: 8px 0 6px;
                margin: 0;
                overflow: hidden;
                text-align: center;
                text-transform: uppercase;
                white-space: nowrap;
            }

            .motd-card-body {
                background-color: #FFF;
                color: #2B0400;
                overflow: hidden;
            }

            .match {
                font-weight: bold;
                padding: 5px 0 0;
                text-align: center;
            }

            .team {
                text-transform: uppercase;
            }

            .score {
                background-color: #FFD130;
                padding: 2px 6px;
                margin: 0 6px;
            }

            .attendance {
                font-size: 0.75em;
                padding: 4px;
                text-align: center;
                text-transform: uppercase;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="motd-card">
                <div class="motd-card-title">Match Stats</div>
                <div class="motd-card-body">
                    <div class="match">
                        <span class="team">Brighton</span><span class="score">1-0</span><span class="team">West Ham</span>
                    </div>
                    <div class="attendance">Attendance: 30,544</div>
                </div>
            </div>
        </div>
    </body>
</html> 
```

[https://codepen.io/deadlybyte/embed/rqdXRq?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/rqdXRq?height=600&default-tab=result&embed-version=2)

# 创建动画序列

好了，现在基本的 <abbr title="Hypertext Markup Language">HTML</abbr> 和 <abbr title="Cascading Style Sheets">CSS</abbr> 已经写好了，我们现在可以专注于比赛统计动画序列。动画有几个部分。这些可以分为三个转变:

*   屏幕上的条目
*   显示初始数据
*   退出屏幕

## [屏幕上的](#entry-on-screen-animation)条目动画

屏幕上的输入是第一个过渡，也是动画进入显示的方式。因此，首先发生的事情是，酒吧的标题逐渐从左边增长，直到酒吧是全宽。我们需要首先对此进行动画制作，因此在 <abbr title="Cascading Style Sheets">CSS</abbr> 中，我们可以使用 <abbr title="Cascading Style Sheets">CSS</abbr> 规则**@关键帧**来创建动画关键帧。

CSS 关键帧描述了动画序列。在动画制作过程中， <abbr title="Cascading Style Sheets">CSS</abbr> 样式会按照关键帧选择器中的指定相应地改变。浏览器计算出中间所需的动画序列，以使动画平滑无缝。

您可以通过访问 [MDN web 文档- @keyframes](https://developer.mozilla.org/en-US/docs/Web/CSS/@keyframes) 了解更多关于@keyframes 规则的信息。

为了声明一个 <abbr title="Cascading Style Sheets">CSS</abbr> 关键帧规则，我使用了 **@keyframes** 以及一个我稍后可以引用的名称。对于我们最初的动画，我们称之为**资料卡标题**。

从到**到**的**关键帧选择器可用于控制动画如何开始和结束。最初标题是不可见的，所以 from 宽度应该设置为 0 像素宽。当设置为 0 时，不显示任何内容。结束状态需要是标题的全宽，即 275 <abbr title="pixels">px</abbr> 宽。**

将以下关键帧规则添加到在【index.html】文件中声明的 <abbr title="Cascading Style Sheets">CSS</abbr> 样式标签的末尾。

```
@keyframes expand-card-title {
    from {
        width: 0;
    }
    to {
        width: 275px;
    }
} 
```

上述关键帧规则将动画显示字幕的宽度，直到它达到全宽。

### 动画 <abbr title="Cascading Style Sheets">CSS</abbr> 属性

我们现在需要将关键帧规则连接到 <abbr title="Cascading Style Sheets">CSS</abbr> 类规则，这是通过**动画-** *属性完成的。两个重要的属性是**动画名称**和**动画延迟**。**动画名称**引用了关键帧规则，所以在上面的例子中**展开卡片标题**。**动画持续时间**需要一个以秒(s)或毫秒(ms)为单位定义的值，这是动画周期完成所需的时间。对于这个动画，我们将使用 0.8s (800 毫秒)作为值。

您可以通过访问 [MDN web docs - animation](https://developer.mozilla.org/en-US/docs/Web/CSS/animation) 找到更多关于 animation-*属性和接受值的信息。

找到 <abbr title="Cascading Style Sheets">CSS</abbr> 类**。motd-card-title** 并进行以下修改。

```
.motd-card-title {
    animation-duration: 0.8s;
    animation-name: expand-card-title;
    ...
} 
```

现在标题像预期的那样动画了，但是身体仍然可见，这不是我们想要的结果。

[https://codepen.io/deadlybyte/embed/zMBrzz?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/zMBrzz?height=600&default-tab=result&embed-version=2)

我们需要隐藏身体，所以我们将对 <abbr title="Cascading Style Sheets">CSS</abbr> 类**做一个小小的修改。motd-卡体**。因此我们需要添加属性 **max-height** 并将值设置为 0，这将使身体隐藏起来。

```
.motd-card-body {
    ...
    max-height: 0;
    ...
} 
```

一旦实现了以上内容，标题将会根据需要进行动画制作。

[https://codepen.io/deadlybyte/embed/OBZLmx?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/OBZLmx?height=600&default-tab=result&embed-version=2)

## 显示初始数据动画-第一部分

下一个转换是数据的初始显示。这是通过上移标题并显示下面的正文内容来实现的。首先，我们需要定义过渡，这将使标题上移。这是通过定义关键帧规则**为身体腾出空间**来实现的。在**展开-卡片-标题**规则之后添加新的关键帧规则。

```
@keyframes make-room-for-body {
    from {
        top: 28px;
    }
    to {
        top: 8px;
    }
} 
```

同样，我们使用了从和**到**的**关键帧选择器来控制过渡。在这种情况下，我们希望卡片上移以显示内容。**

接下来我们必须对**做一些修改。motd 卡**选择器。我们需要添加 position 和 top 属性来帮助卡片向上移动，这将为内容腾出空间。

```
.motd-card {
    position: absolute;
    top: 28px;
    ...
} 
```

请注意，top 被设置为与**为身体腾出空间**关键帧规则中 top 属性相同的值 28 <abbr title="pixels">px</abbr> 。这是因为动画使用相同的值，不会抖动，看起来不自然。

找到**。motd-card-title** 并进行以下修改。

```
.motd-card {
    animation-name: make-room-for-body;
    animation-duration: 0.4s;
    animation-delay: 1s;
    ...
} 
```

### 动画延迟 <abbr title="Cascading Style Sheets">CSS</abbr> 属性

这里我将介绍另一个动画属性叫做**动画-延迟**。这是动画开始前等待的时间，单位为秒或毫秒。

你可以通过访问[MDN web docs-animation-delay](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-delay)找到更多关于 animation-delay 及其可接受值的信息。

**腾出身体空间**的**动画延迟**需要高于**资料卡标题**动画序列的持续时间(即资料卡标题过渡必须在我们开始下一个过渡之前完成)。因此，将延迟设置为秒意味着**扩展卡标题**转换将已完成，因为其持续时间为 0.8 秒。这允许在下一个转换开始之前有 0.2 秒的延迟(0.8 秒+0.2 秒= 1 秒)。

在我们继续之前，我们需要解决这样一个问题，即标题如预期的那样向上移动，但随后又突然回到原来的位置。

[https://codepen.io/deadlybyte/embed/bmMbvM?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/bmMbvM?height=600&default-tab=result&embed-version=2)

它这样做是因为转换完成了，top 属性被设置为 8 <abbr title="pixels">px</abbr> ，而初始的 **motd-card** <abbr title="Cascading Style Sheets">CSS</abbr> 类的 top 属性被定义为 28 <abbr title="pixels">px</abbr> 。过渡完成后，默认的 <abbr title="Cascading Style Sheets">CSS</abbr> 样式被恢复，top 属性被重置为初始值 28 <abbr title="pixels">px</abbr> 。这就是导致震动的原因。

### 动画填充模式 <abbr title="Cascading Style Sheets">CSS</abbr> 属性

这个问题的解决方案是**动画填充模式**属性。该属性可以与值 forwards 一起使用，这意味着在最终过渡中定义的 <abbr title="Cascading Style Sheets">CSS</abbr> 样式将被保留，并阻止属性值被还原为其初始值。

你可以通过访问[MDN web docs-animation-fill-mode](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-fill-mode)找到更多关于 animation-fill-mode 及其接受值的信息。

```
.motd-card {
    ...
    animation-fill-mode: forwards;
    ...
} 
```

[https://codepen.io/deadlybyte/embed/NErdBe?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/NErdBe?height=600&default-tab=result&embed-version=2)

## 显示初始数据动画-第二部分

现在我们可以进入显示初始数据转换的第二阶段，这包括显示卡体/内容。我们将通过增加 max-height 属性来显示卡片主体，直到所有内容都完全可见。

下一步，我们需要设置一个关键帧规则。这次就叫它**揭示-卡-体**。在其他关键帧规则之后添加此关键帧规则。

```
@keyframes reveal-card-body {
    from {
        max-height: 0;
    }
    to {
        max-height: 100%;
    }
} 
```

我们现在必须将关键帧规则添加到**中。使用**动画-** *属性的 motd-卡体**选择器。所以找到了**。motd-card-body** 并添加以下属性。

```
.motd-card-body {
    animation-delay: 1s;
    animation-duration: 1s;
    animation-fill-mode: forwards;
    animation-name: reveal-card-body;
    ...
} 
```

我们使用 **animation-fill-mode** 属性，因为我们不希望 max-height 属性返回到值 0，这会使内容消失。使用带有 forwards 值的**动画填充模式**会将 max-height 值保持为 100%，从而在过渡发生后保持内容可见。

我们再次使用了一秒钟的动画延迟，因为我们希望屏幕上的第一个过渡条目在这个过渡开始之前完成。

[https://codepen.io/deadlybyte/embed/xyjbZz?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/xyjbZz?height=600&default-tab=result&embed-version=2)

### 过渡计时功能 <abbr title="Cascading Style Sheets">CSS</abbr> 属性

然而，动画仍然不平滑，所以我们可以使用另一个属性来使这个过渡更平滑，这被称为**过渡-计时-函数**。

默认情况下，该值设置为“减缓”。这是一种过渡效果，开始缓慢，然后加速，慢慢结束。这没有给出我们需要的平滑过渡，因此，我建议我们改变它，使用渐出值，它将以缓慢的开始和结束过渡。

您可以通过访问[MDN web docs-transition-timing-function](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function)了解更多有关 transition-timing-function 及其接受值的信息。

```
.motd-card-body {
    ...
    transition-timing-function: ease-in-out;
    ...
} 
```

为了使它更加平滑，我们可以在。具有缓出值的 motd-card <abbr title="Cascading Style Sheets">CSS</abbr> 类。

```
.motd-card {
    transition-timing-function: ease-out;
} 
```

[https://codepen.io/deadlybyte/embed/wYjBdG?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/wYjBdG?height=600&default-tab=result&embed-version=2)

## 重构

在我们继续之前，我们需要考虑重构，并使下一阶段变得更容易，这就是退出屏幕。

与大多数 <abbr title="Cascading Style Sheets">CSS</abbr> 属性一样，还有一个 <abbr title="Cascading Style Sheets">CSS</abbr> 属性简写。你可能以前见过这些，一个常见的简写属性是背景属性，你可以在一个方便的属性中指定多个与背景相关的 <abbr title="Cascading Style Sheets">CSS</abbr> 属性。例如:

```
background: no-repeat url('../../media/examples/star.png'); 
```

**animation-** *属性也有一个简写版本，它允许我们用一种简短的方式来声明我们的动画。这种简写也允许我们为 <abbr title="Cascading Style Sheets">CSS</abbr> 类指定过渡计时功能和多个动画。

你可以通过访问 [MDN web docs - animation](https://developer.mozilla.org/en-US/docs/Web/CSS/animation) 找到更多关于动画速记及其可接受值的信息。

让我们重构一下**。motd-card-title** <abbr title="Cascading Style Sheets">CSS</abbr> 类，去掉 animation-*属性，换成下面的:

```
animation: expand-card-title 0.8s; 
```

[https://codepen.io/deadlybyte/embed/MPGYRG?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/MPGYRG?height=600&default-tab=result&embed-version=2)

这样更简洁易读，现在让我们重构 **motd-card** <abbr title="Cascading Style Sheets">CSS</abbr> 类，再次移除 animation-*属性和 transition-timing-function 属性，替换为以下内容:

```
animation: make-room-for-body 0.4s ease-out 1s forwards; 
```

让我们快速解释一下每个值代表什么。

| 价值 | 描述 |
| --- | --- |
| 为身体腾出空间 | 关键帧规则的名称 |
| 0.4s | 动画的持续时间 |
| 放松 | 过渡时间函数效应 |
| 1s | 动画延迟 |
| 向前 | 动画填充模式 |

这些属性的顺序至关重要，因为第一个时间值是持续时间，第二个时间值是用于过渡的延迟。

[https://codepen.io/deadlybyte/embed/PyewMb?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/PyewMb?height=600&default-tab=result&embed-version=2)

我们现在将重构**。motd-card-body** <abbr title="Cascading Style Sheets">CSS</abbr> 类，去掉 animation-*和 transition-timing-function 属性，换成下面的:

```
animation: reveal-card-body 1s ease-in-out 1s forwards; 
```

同样，这些值表示动画名称(展示卡体)、动画持续时间(1s)、过渡计时功能效果(渐出)、动画延迟(1s)和动画填充模式(向前)。

[https://codepen.io/deadlybyte/embed/WaJvbX?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/WaJvbX?height=600&default-tab=result&embed-version=2)

## 退出离屏动画

如前所述，在使用动画速记 <abbr title="Cascading Style Sheets">CSS</abbr> 属性的重构中，我们现在可以指定多个动画，这很好地实现了退出转换。

我们已经让卡片出现了，但是如何移除它并制作动画呢？我们很幸运，不，我们不需要定义更多的关键帧规则，因为幸运的是 <abbr title="Cascading Style Sheets">CSS</abbr> 动画规范支持反转过渡。我们如何做到这一点？那么**动画方向**属性就是答案。

### 动画方向 <abbr title="Cascading Style Sheets">CSS</abbr> 属性

动画方向可以设置动画是向前播放、向后播放还是前后交替播放。当我们想要反转动画时，我们可以使用反转值。

你可以通过访问[MDN web docs-animation-direction](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-direction)找到更多关于 animation-direction 及其接受值的信息。

但是我们不就是重构了吗，所以我们不必使用 animation-*属性，而是使用动画速记 <abbr title="Cascading Style Sheets">CSS</abbr> 属性。是的，我们做到了，这只是更容易解释，如果您需要任何参考和支持，您可以很容易地找到文档。

我们现在可以开始从页面上移除卡片。在我们这样做之前，我们必须考虑时间，因为我们希望内容在被删除之前是可读的。没有必要一显示就删除它，因为用户没有机会阅读它。

我们知道完成所有动画大约需要一秒钟，我们希望在开始退出过渡之前显示四秒钟的内容。因此，在我们开始退出转换之前，需要五秒钟的延迟。考虑到这一点，我们需要逆向工作，将这个延迟应用到最后一个过渡。于是，找到了**。motd-card-body** <abbr title="Cascading Style Sheets">CSS</abbr> 类，并为退出过渡添加另一个动画。

```
.motd-card-body {
    animation:
        reveal-card-body: 1s ease-in-out 1s forwards,
        reveal-card-body: 1s ease-in-out 5s forwards reverse;
} 
```

动画通过逗号分开，我们把它们放在不同的行上，这样更容易阅读和维护，但是它们可以放在同一行上。别忘了用分号(**；**)来标记动画属性的结束。

关于上述退出过渡中的第二个动画，我们希望保留过渡的属性值(在本例中，最大高度值为 0)。在**关键帧**规则**显示-卡体**内从选择器开始到结束。

[https://codepen.io/deadlybyte/embed/JmvdqR?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/JmvdqR?height=600&default-tab=result&embed-version=2)

然后，我们需要将标题向下移动到 28 像素的原始顶部起始位置。所以找到**。motdf-card** <abbr title="Cascading Style Sheets">CSS</abbr> 类，并为退出过渡添加另一个动画。

```
.motd-card {
    animation:
        make-room-for-body 0.4s ease-out 1s forwards,
        make-room-for-body 0.4s ease-out 6s forwards reverse;
    ...
} 
```

那些眼尖的人会注意到延迟被设置为 6 秒，尽管之前我们提到应该是 5 秒。我们将它设置为 6 秒，因为这将允许 **reveal-card-body** exit 转换首先完成。这将为我们提供标题向下移动到其原始起始位置的预期效果。

[https://codepen.io/deadlybyte/embed/QZrbXY?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/QZrbXY?height=600&default-tab=result&embed-version=2)

最后，我们需要缩小标题并将其从视图中移除。为此，找到**。motd-card-title** <abbr title="Cascading Style Sheets">CSS</abbr> 类并相应修改:

```
.motd-card-title {
    animation:
        expand-card-title: 0.8s,
        expand-card-title: 0.8s 6.4s forwards reverse;
    ...
} 
```

我们将退出过渡延迟了 6.4 秒，因此上面的退出过渡**在我们触发此过渡之前完成，为身体腾出空间**。我们在这里使用了 forward，因此保留了过渡属性，导致卡片从视图中移除。

[https://codepen.io/deadlybyte/embed/MPGajY?height=600&default-tab=result&embed-version=2](https://codepen.io/deadlybyte/embed/MPGajY?height=600&default-tab=result&embed-version=2)

现在已经完成了，你可以试试颜色、大小和速度。作为进一步的步骤，您还可以添加 JavaScript 来使动画动态化，并删除硬编码的值。

就是这样！你已经制作了一个 <abbr title="Match of the Day">MOTD</abbr> 启发 <abbr title="Cascading Style Sheets">CSS</abbr> 动画。下面是 index.html 文件的最终版本*。* 

```
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <link href="https://fonts.googleapis.com/css?family=Ubuntu" rel="stylesheet">
  Match Of The Day CSS Animation
  <style>
    html,
    body {
      height: 100%;
    }

    body {
      background-color: #333;
      font-family: 'Ubuntu', Arial, Helvetica, sans-serif;
      margin: 0;
    }

    .container {
      align-items: center;
      display: flex;
      height: 100%;
      justify-content: center;
      margin: 0;
    }

    .motd-card {
      animation:
        make-room-for-body 0.4s ease-out 1s forwards,
        make-room-for-body 0.4s ease-out 6s forwards reverse;
      position: absolute;
      top: 28px;
      width: 275px;
    }

    .motd-card-title {
      animation:
        expand-card-title 0.8s forwards,
        expand-card-title 0.8s 6.4s forwards reverse;
      background-color: #FFD130;
      color: #2B0400;
      font-weight: bold;
      padding: 8px 0 6px;
      margin: 0;
      overflow: hidden;
      text-align: center;
      text-transform: uppercase;
      white-space: nowrap;
    }

    .motd-card-body {
      animation:
        reveal-card-body 1s ease-in-out 1s forwards,
        reveal-card-body 1s ease-in-out 5s forwards reverse;
      background-color: #FFF;
      color: #2B0400;
      max-height: 0;
      overflow: hidden;
    }

    .match {
      font-weight: bold;
      padding: 5px 0 0;
      text-align: center;
    }

    .team {
      text-transform: uppercase;
    }

    .score {
      background-color: #FFD130;
      padding: 2px 6px;
      margin: 0 6px;
    }

    .attendance {
      font-size: 0.75em;
      padding: 4px;
      text-align: center;
      text-transform: uppercase;
    }

    @keyframes expand-card-title {
      from {
        width: 0;
      }

      to {
        width: 275px;
      }
    }

    @keyframes make-room-for-body {
      from {
        top: 28px;
      }

      to {
        top: 8px;
      }
    }

    @keyframes reveal-card-body {
      from {
        max-height: 0;
      }

      to {
        max-height: 200px;
      }
    }
  </style>
</head>

<body>
  <div class="container">
    <div class="motd-card">
      <div class="motd-card-title">Match Stats</div>
      <div class="motd-card-body">
        <div class="match">
          <span class="team">Brighton</span><span class="score">1-5</span><span class="team">West Ham</span>
        </div>
        <div class="attendance">
          Attendance: 30,544
        </div>
      </div>
    </div>
  </div>
</body>

</html> 
```

也可以从 [GitHub](https://github.com/deadlybyte/motd-css-animations) 获取最新的源代码。