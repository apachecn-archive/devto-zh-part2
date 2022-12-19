# NotSoDailyUI #001 -注册

> 原文：<https://dev.to/fleepgeek/the-notsodailyui-001---signup-ne6>

# 简介

我决定参加来自 dailyui 的#dailyui 挑战。这个旅程将集中在接口的编码上，而不是如何设计它们。为了帮助我做到这一点，我会参考我收集设计灵感的作品。这将是初学者和前端开发人员学习如何创建通用接口的一个途径。

## 标题男是怎么回事？

呃...我不会每天都发这篇文章，因为我知道自己没有时间(我不是全职的代码讲师，但我真的很喜欢这条职业道路，因为我热爱教学)。

# 我们在建造什么

说够了，我们开始吧:

[https://codepen.io/fleepgeek/embed/zJGGdE?height=600&default-tab=result&embed-version=2](https://codepen.io/fleepgeek/embed/zJGGdE?height=600&default-tab=result&embed-version=2)

这个设计是我在谷歌上搜索“注册表单”时得到的第一张图片最初的[设计](https://www.tutorialrepublic.com/snippets/preview.php?topic=bootstrap&file=simple-sign-up-form-with-blue-background)是用 bootstrap 创建的，但我们将从头开始编码。

## Html

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    Simple SignUp
    <link href="https://fonts.googleapis.com/css?family=Open+Sans:400,600" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Pacifico" rel="stylesheet">
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <header>
        <h2>Teebo</h2>
    </header>
    <div class="container">
        <div class="signup-wrapper">

            <div class="signup">
                <div class="heading">
                    <h2>Sign Up</h2>
                    <p>Please fill in this form to create an account!</p>
                </div>
                <div class="divider"></div>
                <form class="signup-form">
                    <div class="form-group">
                        <input type="text" placeholder="First Name" name="firstName" id="firstName">
                        <input type="text" placeholder="Last Name" name="lastName" id="lastName">
                    </div>

                    <input type="email" placeholder="Email" name="email" id="email">
                    <input type="password" placeholder="Password" name="password" id="password">
                    <input type="password" placeholder="Confirm Password" name="confirmPassword" id="confirmPassword">

                    <div class="form-group">
                        <input type="checkbox" name="privacy" id="privacy">
                        <label for="privacy">I accept the <a href="#">Terms of Use</a> & <a href="#">Privacy Policy</a></label>
                    </div>

                    <input type="submit" value="Sign Up">
                </form>
            </div>

            <div class="login-link">
                <p>Already have an account? <a href="#">Login Here</a></p>
            </div>

        </div>
    </div>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

这是注册页面结构的基本 html 标记。需要注意的几件事之一是用于分组输入的`form-group`类。带有`form-group`类的`div`内的`input`将出现在较大设备的同一行上。

## CSS (style.css)

我们将使用 css flexbox 来定位元素。我们还将使用 css 变量，这只是一种定义 css 规则在我们的设计中使用的值的方法。css 变量的一个主要好处是我们可以很容易地更新变量的值，相应的规则也会随之更新。我们将在编码的实践中看到这一点。

首先，让我们创建这些变量:

```
/*-- Variables --*/

:root {
    --primary-color: #3598DC;
    --grey: grey;
    --grey-light: #f1eded;
    --grey-dark: rgb(48, 47, 47);
} 
```

Enter fullscreen mode Exit fullscreen mode

兄弟，双破折号是怎么回事？这就是创建 css 变量的方法。我们如何使用这些变量？冷静，我现在就告诉你，但在此之前，让我们为我们的设计定义全球风格:

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
}

body {
    background: var(--primary-color);
}

h2 {
    font-size: 36px;
    color: var(--grey-dark);
}

p {
    font-size: 16px;
    color: var(--grey);
}

input {
    background-color: var(--grey-light);
    padding: 15px;
    margin-bottom: 25px;
    box-sizing: border-box;
}

::placeholder {
    color: var(--grey);
} 
```

Enter fullscreen mode Exit fullscreen mode

你注意到什么了吗？我们已经使用了变量。我们使用像这样声明的 css 变量。我们还为其他元素创建了通用样式。你可以打开你的浏览器，看看你的修改。

让我们来设计标题和容器的样式`div` :

```
header h2 {
    font-family: 'Pacifico', cursive;
    color: #ffffff;
    padding: 0 0 20px 20px;
}

.container {
    display: flex;
    justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 报名表格

首先，我们将设计表单的包装器`div`、标题和分隔线。分隔线只是一个 div，看起来像一条水平线

```
/*SignUp form*/

.signup-wrapper {
    width: 500px;
}

.signup {
    background-color: #ffffff;
    border-radius: 5px;
}

.signup .heading {
    padding: 30px 30px 0 30px;
}

.signup .heading h2 {
    margin-bottom: 10px;
}

.divider {
    height: 1px;
    background: var(--grey-light);
    margin: 20px 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们来设计表单的样式:

```
.signup-form {
    padding: 0 30px 30px 30px;
}

.signup-form a {
    color: var(--primary-color);
}

.signup-form input {
    display: block;
    width: 100%;
}

.form-group {
    display: flex;
    justify-content: space-between;
}

.form-group input {
    flex: 0 0 48%;
}

.form-group input[type="checkbox"] {
    flex: 0 0 5%;
    margin: 0;
    align-self: center;
}

.form-group label {
    flex: 0 0 94%;
    color: var(--grey);
}

.signup-form input[type="submit"] {
    width: inherit;
    padding: 10px 40px;
    margin: 30px 0;
    background: var(--primary-color);
    color: #ffffff;
    font-weight: 600;
    border-radius: 3px;
} 
```

Enter fullscreen mode Exit fullscreen mode

放松点，这里没发生什么不寻常的事。让我们一起来分解一下:

1.  我们向注册表单添加一些填充
2.  我们让`input`成为一个块元素，这样每个`input`都在一个新行上，然后我们让它的`width` 100%填充水平空间。
3.  接下来，我们制作表单组的一个`flex`布局，并使用`space-between`规则使其内容之间有一个空格。
4.  然后，我们将表单组中的`input`设置为有一个`flex-grow: 0`、`flex-shrink: 0`和一个`flex-basis: 48%`。`flex: 0 0 48%`是规则的简写。值为 0 意味着`input`在调整屏幕大小时不会变大也不会变小。`flex-basis: 48%`的作用是相当明显的。对吗？

### 登录-链接

让我们来设计表单下面登录链接:

```
/*login-link*/

.login-link {
    margin: 20px;
    text-align: center;
}

.login-link p {
    color: #ffffff;
}

.login-link p a {
    text-decoration: underline;
    color: #ffffff;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 终于

再补充一些媒体查询善良:

```
@media (max-width: 650px) {
    .signup-wrapper {
        width: 90%;
    }
    .form-group {
        display: block;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当屏幕尺寸低于 650 像素时(适用于较小的屏幕)，此媒体查询开始。我们将`signup-wrapper`的宽度从`500px`改为`90%`。`form-group`现在显示 block(之前显示 flex ),然后让项目水平放置。

# 结论

就是这样。你完了。你可以在这里跟踪我的每日报告。还有一件事，试着改变 css 变量的值，看看 css 变量的好处。
非常感谢你的阅读。万事如意。