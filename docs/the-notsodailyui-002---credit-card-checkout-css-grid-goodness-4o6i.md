# NotSoDailyUI #002 -信用卡结帐(CSS 网格善良！)

> 原文：<https://dev.to/fleepgeek/the-notsodailyui-002---credit-card-checkout-css-grid-goodness-4o6i>

欢迎来到本系列的第二部分。如果你错过了第一部分，这里有链接:[第一部分](https://dev.to/fleepgeek/the-notsodailyui-001---signup-ne6)

在这个例子中，我们将为信用卡结账页面编写 UI 代码。
让我们来看看最终产品:

[https://codepen.io/fleepgeek/embed/GXJpJJ?height=600&default-tab=result&embed-version=2](https://codepen.io/fleepgeek/embed/GXJpJJ?height=600&default-tab=result&embed-version=2)

正如我在第一部分中所说的，由于这是一个代码教程，我将专注于代码，而不是从头开始设计 UI。我从 Andrea Pilutti 的这个行为帖子中获得了这个设计的灵感。

好吧，让我们编码。

# 标记

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    Credit Card Checkout
    <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,400,600" rel="stylesheet">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.2.0/css/all.css" integrity="sha384-hWVjflwFxL6sNzntih27bfxkr27PmbbK/iSvJ+a4+0owXq79v+lsFkW54bOGbiDQ"
        crossorigin="anonymous">
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <div class="container">
        <div class="checkout-flow">
            <div class="checkout-nav">
                <a href="#">Delivery & Pickup Options</a>
                <a href="#">Shipping Address</a>
                <a href="#" class="active">Payment</a>
            </div>

            <div class="checkout">
                <div class="product">
                    <img src="https://raw.githubusercontent.com/fleepgeek/dailyui/master/002-checkoimg/beats.png" alt="product">
                    <div class="info">
                        <p class="name">Beats by Dr.Dre</p>
                        <p class="type">Studio Wireless</p>
                        <h2 class="price">$375</h2>
                    </div>
                </div>

                <div class="form-wrapper">
                    <h2>Credit Card Checkout</h2>
                    <form class="payment-form">

                        <div class="input-control">
                            <label for="cardholder">Cardholder's Name</label>
                            <input type="text" name="cardholder" id="cardholder">
                        </div>

                        <div class="input-control">
                            <label for="cardholder">Card Number</label>
                            <div class="icon-input">
                                <input type="number" name="cardnumber" id="cardnumber">
                                <i class="fab fa-cc-mastercard"></i>
                            </div>
                        </div>

                        <div class="control-group">
                            <div class="input-control">
                                <label for="cardholder">Expiration Date</label>
                                <div class="icon-input">
                                    <input type="text" name="expiration" id="expiration" placeholder="MM/YY">
                                    <i class="far fa-calendar"></i>
                                </div>
                            </div>

                            <div class="input-control">
                                <label for="cardholder">Cvc code</label>
                                <input type="text" name="cardholder" id="cardholder">
                            </div>
                        </div>

                        <input type="submit" class="btn-order" value="Place Order">
                    </form>
                </div><!-- end form-wrapper -->

            </div><!-- end checkout -->
        </div><!-- end checkout-flow -->
    </div><!-- end container -->
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

html 标记非常简单，但是在这里的结构中有一些事情需要注意。所有`input`的包装`div`都具有`input-control`的类别，不包括`submit`的`input`类型。这个类用于样式化我们的输入。

# CSS(style . CSS)

我们将使用移动优先的方法来设计页面。这意味着我们首先要为移动设备设计页面样式，然后使用媒体查询来为更大的屏幕设计页面样式。此外，我们将使用 css 网格来定位我们的布局。

## 全局样式

就像我们在第一部分中所做的一样，我们首先设置我们的 css 变量:

```
/*-- Variables --*/

:root {
    --bg-color: #25263B;
    --light-blue: rgb(53, 98, 220);
    --grey: grey;
    --light-grey:#E9E8ED;
    --green: rgb(19, 170, 70);
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们为元素设置全局样式:

```
/*---- Global ----*/
* {
    margin: 0;
    padding: 0;
    border: 0;
    outline: 0;
    font-family: 'Open Sans', sans-serif;
    font-size: 100%;
    vertical-align: bottom;
    text-decoration: none;
    box-sizing: border-box;
}

html, body { 
    height: 100%;
    width: 100%;
    background-color: var(--bg-color);
}

a {
    color: var(--grey);
    text-transform: uppercase;
    font-size: 12px;
    font-weight: 700;
}

a.active {
    color: #ffffff;
}

label {
    font-size: 12px;
    font-weight: 600;
    text-transform: uppercase;
    color: var(--grey);
    display: block;
    margin-bottom: 5px;
}

input {
    padding: 10px;
    border: 2px solid var(--light-grey);
    width: 100%;
    font-weight: 600;
    border-radius: 3px;
}

input:focus {
    border-color: var(--green);
} 
```

Enter fullscreen mode Exit fullscreen mode

我来解释一些部分。我们刚刚给所有的`input`设置了 2px 的灰色边框，当它被聚焦时，我们将边框颜色改为绿色。此外，我们使用`*`选择器给所有元素一些默认值。

## 主要款式

```
/* main */

.container {
    display: grid;
    grid-template-columns: .25fr 4fr .25fr;
    grid-template-areas: ". m .";
    width: 100%;
    padding: 40px 0;
}

.checkout-flow {
    grid-area: m;
    width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是 css 网格优势的来源。我们给`.container`一个`display: grid`,告诉浏览器这个容器将会是一个网格(是的是的，我知道这很明显)。不像 flex 系统集中在一维排列项目(行或列取决于`flex-direction`属性)，网格布局具有控制二维(行和列)项目排列的能力。

我们使用`grid-template-columns`定义列。这里我们定义了三列`.25fr`、`4fr`和`.25fr`。`fr`简单的意思是可用空间的小数单位。使用`fr`将可用空间分成分数(Lol！！你们中那些在高中翘了数学课的人)。所以，`4fr`的意思是:占用 4 部分可用空间。`.25fr`意思是:占据 1/4 的可用空间。
接下来，我们使用`grid-template-areas: ". m ."`定义希望这些列占据的区域。这里`m`是一个名字(我们可以使用任何单词或字母)。`m`表示中间一列，即`4fr`。圆点表示未命名的列(您很快就会明白这一点)。

为了使我们刚刚解释的一切有用，我们必须将元素分配给这些定义的区域。我们通过给出规则`grid-area: m`来为`.checkout-flow`做这件事。这告诉网格系统这个元素应该在网格系统中占据 m 的面积。其余标有圆点的区域将作为空白区域，或者如果你愿意，你可以称之为空白区域。

* *所有这些压力只是为了将一个 div 以相等的边距放置在中间？
静下心来。我们可以这样做:`margin: 0 auto`(在其左右自动边距)在`.container`上。现在，如果我们想让`.checkout-flow`覆盖整个宽度(即 100%)。我们必须给`checkout-flow`负的边距来迫使它覆盖宽度，但是用 css 网格我们只需要:`grid-area: m m m`就可以了。这只是一个新的选择，在某些设计场景中可能是有益的。在你给定的情况下，使用最适合你的方法。

> 我知道，太多了，对吧？如果这对初学者来说太难了，我可以做一个完整的 css 网格教程。请在评论区告诉我。

### 结账-导航

这种风格适用于屏幕顶部的步骤导航:

```
.checkout-nav  {
    display: flex;
    justify-content: space-between;
    flex-direction: column;
    margin: 30px 0;
    height: 70px;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结账

这些是包含产品和表单包装的主结帐容器的样式。

```
 .checkout {
    background-color: #fff;
    border-radius: 3px;
    width: 100%;
    min-height: 550px;
    display: grid;
    grid-template-columns: repeat(12, 1fr);
    grid-template-areas: "p p p p p p p p p p p p"
                         "f f f f f f f f f f f f";
}

.product {
    grid-area: p;
    position: relative;
    min-height: 350px;
}

.form-wrapper {
    grid-area: f;
    padding: 40px;
    background-color: var(--light-grey);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用与我们刚刚学习的相同的原理，我们在具有类`checkout`的父类`div`上定义列和区域。
我们没有手动定义列，而是使用了`repeat`函数，该函数创建了 12 列，每列占用了可用空间的一部分。简单地说，我们刚刚创建了一个 12 列的网格。
接下来，我们将刚刚创建的区域分配给`.product`和`.form-wrapper`。记住，css 网格具有二维的能力，所以只定义了一个 12 列 2 行的网格。

在移动视图中，产品将位于表单包装器的顶部(稍后，我们将使用媒体查询来控制较大屏幕的区域)。打开你的浏览器，看看你的进度。

### 产品

让我们继续设计我们的产品部分。

```
.product img {
    width: 100%;
    height: 100%;
    object-fit: contain;
    padding-bottom: 40px;
}

.product .info {
    position: absolute;
    bottom: 0;
    padding: 30px;
}

.product .info .name {
    color: var(--grey);
    font-weight: 700;
    font-size: 20px;
}

.product .info .type {
    font-size: 25px;
}

.product .info .price {
    font-size: 36px;
    font-weight: 300;
    margin-top: 10px;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 表格

```
.form-wrapper {
    grid-area: f;
    padding: 40px;
    background-color: var(--light-grey);
}

.form-wrapper h2 {
    font-size: 24px;
    font-weight: 300;
    color: var(--light-blue);
    margin-bottom: 40px;
}

.input-control {
    margin-bottom: 20px;
}

.icon-input {
    position: relative;
}

.icon-input input {
    padding-right: 40px;
}

.icon-input > i {
    position: absolute;
    bottom: 0;
    right: 0;
    padding: 15px;
    font-size: 20px;
    color: var(--grey);
}

.btn-order {
    border: none;
    background-color: var(--green);
    color: #ffffff;
    text-transform: uppercase;
    margin-top: 20px;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您阅读了本系列的第 1 部分，您会发现在我设计表单元素样式的方法中没有什么新东西。嗯，除了一些表单输入上可爱的字体图标。好的，让我们来分析一下，但是首先让我们来看看这个元素的 html 标记，作为一个快速的提示:

```
<div class="input-control">
    <label for="cardholder">Card Number</label>
    <div class="icon-input">
        <input type="number" name="cardnumber" id="cardnumber">
        <i class="fab fa-cc-mastercard"></i>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个特殊的图标输入类，叫做`icon-input`。在这个样式中，我们设置了`position: relative`，这样我们就可以给出图标元素:`position: absolute`，并将其放置在输入的右上角。此外，我们给了`input`一个合适的填充，这样它的文本就不会覆盖图标。

你的页面现在应该看起来很棒了。去检查一下。我们只需要为更大的屏幕添加媒体查询，这就是我们接下来要做的。

### 更大屏幕的媒体查询

```
@media (min-width: 1024px) {
    .container {
        display: grid;
        grid-template-columns: .5fr 2fr .5fr;
        grid-template-areas: ". m .";
    }
    .checkout {
        grid-template-areas: "p p p p p f f f f f f f";
    }
    .checkout-nav {
        flex-direction: row;
        justify-content: space-evenly;
        height: 20px;
    }
    .control-group {
        display: flex;
        justify-content: space-between;
    }
    .control-group .input-control {
        flex: 0 0 48%;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于`.checkout`类，我们重新安排了`grid-template-areas`来并排布局 p 和 f。随着这种重新安排，产品和形式包装将并排在大屏幕上。
接下来，我们将`.checkout-nav`的伸缩方向改为`row`，并使其均匀分布，这样链接就出现在一条水平线上。
我们给了`.control-group`一个`flex`的显示，这样输入(截止日期和 cvc 代码)将位于同一水平线上。

# 结论

就这样了，伙计们。这是[链接](https://github.com/fleepgeek/dailyui)到我的 dailyui github repo。
敬请关注下一部分！！