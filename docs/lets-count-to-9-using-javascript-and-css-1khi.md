# 让我们用 javascript 和 CSS 数到 9

> 原文：<https://dev.to/heyarviind/lets-count-to-9-using-javascript-and-css-1khi>

我在 Instagram 上滚动，发现了一个很酷的数字计数器，我以为他们是用 javascript 制作的，但在看了标签后，我知道它是用 3D 软件制作的。我想，为什么不用 javascript 重新制作呢(只是为了好玩)。在我创建了动画计数器后，我在我的 [Instagram](https://instagram.com/heyarviind) 上分享了它，并获得了非常积极的评论，许多粉丝都在询问代码和教程。

## 创建结构

HTML 结构由包裹在一个 div 中的 <mark>15 个圆圈</mark>div 组成，每行包含 3 个圆圈 div，这样我们可以准确地显示数字

```
    <div class="container">

      <div class="circle"></div>
      <div class="circle"></div>
      ...

    </div> 

```

## 添加样式

我保持这段代码简单，这样读者可以很容易地理解它，在 CSS 中我所做的是圆的两个状态，一个是较小的灰色，代表<mark>非活动状态</mark>，第二个是<mark>。活动类</mark>，它使 div 的尺寸变大并着色，以指示圆圈的<mark>活动状态</mark>

```
    .circle{
          width: 60px;
          height: 60px;
          background-color: #536dfe;
          border-radius: 50%;
          display: inline-block;
          transform: scale(.2);
          background-color: #c4c4c4;
          transition: transform 1s ease-in-out;
        }

        .circle.active{
          transform: scale(1);
          background-color: #536dfe;
        } 

```

## 事件处理

首先，我们需要定义每个数字的模式，以便它可以相应地更改 DOM。我所做的是，制作一个数组并定义每个数字的模式，如下所示。

```
    var num_pattern = [
                  [ 
                    1, 1, 1,
                    1, 0, 1,
                    1, 0, 1,        //For 1
                    1, 0, 1,
                    1, 1, 1
                  ],
                  [
                    1, 1, 1,
                    0, 0, 1,
                    1, 1, 1,        //For 2
                    1, 0, 0,
                    1, 1, 1
                  ]
                  ...
            ]; 

```

**0** 表示<mark>未激活</mark>状态， **1** 表示<mark>激活</mark>状态

这里的主要概念是，模式中的每个 <mark>0</mark> 和 <mark>1</mark> 贡献 DOM 中的每个元素。因此，如果在一个数组的第三个索引中有 <mark>1</mark> ，第四个循环将被激活(这里不要混淆，数组从 0 开始)😜)

现在我们必须每秒改变一次数字，这样**settimeinterval**就会完成这项工作。每秒钟，我们增加数字，检查数组中的数字模式，并更新 DOM。

```
    var circles = document.querySelectorAll('.circle');
        var i = 0;

        setInterval(function(){
          incNum();
          // Increment the number at every 1 second
          i++;
        }, 1000);

        function incNum(){
          // Reset the counter when it reaches > 9
          if(i == 10) i = 0;

          for(z = 0; z < num_pattern[i].length; z++){
            if(num_pattern[i][z]){
              //If it has '1' then make the circle active
              //Check if the circle is already active
              if(!circles[z].classList.contains('active')){
                circles[z].classList.add("active");
              }
            } else {
              if(circles[z].classList.contains('active')){
                circles[z].classList.remove("active");
              }
            }
          }
        } 

```

注意:这里我们只在需要的时候更新 DOM，这也给了动画甜美的效果

[看这里的演示](https://demo.uicard.io/lets-count-to-9-in-javascript/)

**注:**这篇文章最初发表在我的[创业博客](https://blog.uicard.io/counter-in-javascript-css/)