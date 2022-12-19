# 箭头功能

> 原文：<https://dev.to/zmiles17/arrow-functions-3fl9>

在我的 JavaScript 之旅中，我根本没有使用过箭头函数，但是我被引入了箭头函数继承或绑定“this”值的想法。在 ES6 之前，web 开发人员必须通过将“this”设置为一个变量或使用“bind”方法来绑定它。这是我在一次家庭作业中用到的一个函数的例子。

[![A submit listener without an arrow function](../Images/61541df823dd131e104162c6fb3b1dcd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jgpr3pYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8raqyckmfi56gs3rp9ep.png)

这只是一个基本的事件侦听器，一旦用户提交表单，它就会触发。我想让你注意的唯一一件事是，在初始函数中以及在。然后(function()"。

[![](../Images/ea8ba783f4edb24335f91b12ac65e387.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x_KKulma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x832k3hdnc7d9aj2cld9.png)

如你所见，“这个”有不同的定义。如果我们希望“this”的值延续到下一个函数，那么我们可以简单地改变。然后(function()"到"。然后(()= >”。为了避免冗余，我不会显示代码中的变化，但我想演示“this”现在将等于包含函数中的“this”。

[![](../Images/c44a5f29ec617973b3c30fddc2d33f0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--96wugrol--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gcw0qo29ujv4jsidxpuc.png)

箭头函数也不要求您使用 return 关键字，或者在只使用一个参数时使用括号。

[![](../Images/e8110aeb623aa1dcfc57c8537fddaede.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0blhDNfA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qebvohsm42xkrg1fj8nd.png)

如果我们需要一个以上的参数，那么括号是必需的。

## TL；速度三角形定位法(dead reckoning)

箭头函数绑定值“this”，隐式使用“return”关键字，如果只使用一个参数就不需要括号，使我们的代码更加简洁易读。需要注意的一点是，一旦你使用了一个箭头函数，你就不能在这个函数中解除“this”值的绑定。一般情况下，最好在全局范围内使用“函数”，避免使用箭头函数作为构造函数！