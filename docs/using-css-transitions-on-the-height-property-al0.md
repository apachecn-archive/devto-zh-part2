# 在 Height 属性上使用 CSS 过渡

> 原文：<https://dev.to/sarah_chima/using-css-transitions-on-the-height-property-al0>

对我们网页的小影响可以对网站的用户体验产生很大的影响。当一个元素的状态改变时，用一个视觉效果来显示一个动作的发生是非常酷的。由于 CSS 过渡，我们有了广泛的过渡效果，可以用在我们的 HTML 元素上。

元素的高度是一个经常需要转换的 CSS 属性。有时，我们希望元素的一部分被折叠起来，直到需要它的时候。也就是说，当单击按钮时，元素的高度会增加或减少。查看更多按钮和引导面板利用这种技术。一些框架，如 Bootstrap 和 JQuery，会带来转换效果。然而，CSS 转换在转换高度方面给了你很大的灵活性。此外，你不必因此在你的项目中加入另一个框架。所以在这篇文章中，我们将看到我们如何动画的高度属性和可能面临的限制。

### 转换高度

我们将用一个简单的例子来解释这一点。我们有一篇文章，一开始会崩溃。点击“查看更多”按钮将增加元素的高度，以显示文章的所有内容。我们将为此创建一个 CSS 类。当单击“查看更多”按钮时，这个类将使用 JavaScript 添加到 article 元素中。

首先，我们将向 HTML 文件添加一个 article 元素。

```
<body>
    <article id="article">
        <h3>Why You Should Care about the Ipsum</h3>
        <p>Tart jelly beans croissant toffee oat cake soufflé gingerbread. Toffee powder cheesecake soufflé bonbon tiramisu toffee powder gummi bears. Toffee tootsie roll powder soufflé apple pie. Fruitcake fruitcake soufflé sweet oat cake cotton candy lemon drops biscuit. Chupa chups fruitcake dessert icing halvah oat cake. Lollipop candy canes halvah bonbon marshmallow croissant. Wafer chupa chups cotton candy tart pudding pie cupcake. Candy canes gummies macaroon pudding cupcake cupcake pudding jujubes. Donut halvah pie chocolate. Sugar plum dessert pudding icing jelly-o cake. Gingerbread macaroon wafer. Caramels muffin jelly wafer carrot cake.</p>

        <p>Marshmallow candy cookie danish cake. Cupcake croissant gummi bears pastry wafer. Macaroon croissant bonbon wafer. Topping fruitcake topping biscuit. Tiramisu powder sesame snaps candy. Dessert donut cookie carrot cake dragée muffin. Lollipop oat cake cookie candy canes fruitcake. Candy croissant candy canes croissant bear claw cake brownie biscuit pie. Liquorice wafer wafer cookie lollipop gingerbread chocolate cake oat cake dessert. Pudding gingerbread croissant cheesecake soufflé. Muffin gummies chocolate chocolate cupcake pastry. Sweet roll fruitcake bear claw sweet caramels lemon drops lemon drops.</p>

        <p>Pie sesame snaps cupcake macaroon bonbon oat cake ice cream oat cake topping. Brownie dessert toffee brownie jelly-o chocolate jujubes halvah chocolate bar. Pudding gingerbread dessert. Bear claw tiramisu gummies pudding. Toffee marshmallow jelly beans pie marzipan caramels ice cream lollipop powder. Dragée sesame snaps sugar plum. Marshmallow sweet roll croissant tootsie roll icing. Dragée chocolate marzipan jelly cotton candy. Jujubes sweet chocolate bar candy sweet roll lollipop biscuit dessert. Danish lollipop caramels toffee pastry. Wafer candy canes cupcake chupa chups gummies lemon drops jujubes powder. Caramels danish marshmallow gummies. Jujubes muffin danish pie icing brownie.</p>

        <p>Toffee sweet tiramisu topping. Cookie fruitcake icing jelly-o sesame snaps. Caramels gingerbread ice cream pastry donut. Gummies liquorice carrot cake sesame snaps muffin toffee dragée marzipan oat cake. Chocolate bar lemon drops dessert. Sweet cupcake sesame snaps carrot cake dessert candy canes halvah tart ice cream. Jelly donut chocolate bar chupa chups tootsie roll soufflé carrot cake tootsie roll gummi bears. Pastry jujubes soufflé marshmallow toffee. Macaroon marshmallow oat cake jujubes caramels topping marzipan cupcake icing. Brownie jelly sweet tootsie roll brownie jujubes cupcake pie. Cookie lollipop ice cream tiramisu jelly-o chocolate gummies. Tart biscuit tiramisu biscuit cake tart danish topping cookie. Liquorice donut dragée tart. Dragée soufflé pudding halvah cookie marshmallow jujubes sweet roll.</p>

        <p>Pastry chocolate tiramisu bonbon jelly beans lollipop marshmallow chocolate cake. Icing carrot cake gummies cheesecake dragée. Cake fruitcake gummies. Halvah jujubes toffee pudding bonbon soufflé brownie cupcake candy. Icing biscuit cake jujubes. Chocolate bar candy canes caramels cupcake. </p>
    </article>
    <button id="seeMoreBtn">See More</button>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的 CSS

```
 article {
        max-width: 800px;
        height: 300px;
        overflow-y: hidden;
    }

    /* This class is added when button is clicked */
    article.expanded {
        height: 628px;
    }

    button {
        height: 41px;
        padding: 0 2rem;
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后一些 JavaScript 来启动这个过程

```
 const seeMore = document.getElementById("seeMoreBtn");
    const article = document.getElementById("article");

    seeMore.addEventListener("click", () => {
        article.classList.toggle("expanded");

        const expanded = article.classList.contains("expanded");
        if (expanded) {
            seeMore.innerHTML = "See Less";
        } else {
            seeMore.innerHTML = "See More";
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

让我们在 CSS 中添加一些过渡，使内容在按钮被点击时上下滑动。

我们将向 article 元素添加 transition 属性，这样 CSS 就变成了

```
 article {
        max-width: 800px;
        height: 300px;
        overflow-y: hidden;
        transition: height 0.4s linear;
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在文章上下滑动。

[![height with animation](img/100a2f8e943e18449425860f6ab23e48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w3jErQL6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://res.cloudinary.com/dvj2hbywq/image/upload/v1524084041/Apr-18-2018-9_33-PM_uzuwka.gif)

你可能认为这是我感谢你阅读这篇文章的部分。不，不是的。这种方法有一个局限性。让我们考虑一下。

### 局限性

当文章的高度已知时，我们上面所做的工作很好。当元素的高度未知时会发生什么呢？例如，在处理动态内容时？高度也可能随着屏幕尺寸的增大或减小而变化，这带来了挑战。

对于动态内容，元素的高度应该设置为`auto`。这样，元件高度的任何增加或减少都将被适应。挑战如下:**当一个元素的高度设置为自动**时，CSS 转换不起作用。这是你将得到的。

[![height without animation](img/ecb211d6c23651dc9caeb02845c0b3c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7iTPuqls--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://res.cloudinary.com/dvj2hbywq/image/upload/v1524024293/Apr-18-2018-4_36-AM_f0tzic.gif)

关于这一点的好消息是，有一种方法可以绕过这个问题，而不必求助于更多的 JavaScript。

### 解决方案

这个技巧是转换`max-height`属性，而不是`height`属性。首先，我们必须估计我们的元素可以达到的最大高度。然后，当元素展开时，我们设置元素的`max-height`大于我们的估计值。所以让我们再次重温我们的 CSS。这次我们将把我们的`height`属性改为`max-height`。

```
 article {
        max-width: 800px;
        max-height: 300px;
        overflow-y: hidden;

        /*Transition time is increased to accomodate the height */
        transition: max-height 0.7s linear;
    }

    article.expanded {
        max-height: 1500px;
    } 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，动画可以工作，我们仍然可以得到我们想要的效果。过渡时间可能需要根据您想要的效果进行调整。

[![height with animation](img/100a2f8e943e18449425860f6ab23e48.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w3jErQL6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://res.cloudinary.com/dvj2hbywq/image/upload/v1524084041/Apr-18-2018-9_33-PM_uzuwka.gif)

有什么问题或补充吗？请留言评论。

感谢您的阅读:)