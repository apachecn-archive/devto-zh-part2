# SVG 和“所有”属性

> 原文：<https://dev.to/johnkreitlow/svg-and-the-all-property-30d7>

这是一个 bug，还是一个特性？我想用`all`属性重置 div 中的所有元素，但是我在 Chrome 和 Firefox 中得到不同的结果。Chrome 似乎认为`cx`、`cy`、`d`、`r`、`stroke`、`fill`(可能还有其他属性)都是 CSS 可控的。火狐似乎恰当地应用了`fill`，并没有触及其他属性。

[https://codepen.io/radium-v/embed/gKLONY?height=600&default-tab=result&embed-version=2](https://codepen.io/radium-v/embed/gKLONY?height=600&default-tab=result&embed-version=2)

Firefox 给了我多少有些期待的东西。它并不完美，但我真的不知道该期待什么:
[![green circle next to a green triangle on a pink square](img/e88c37c2e722cea1d8911bbf07d73f34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EaVguV34--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eao0r114d5zm2izqw0rg.png)

Safari 只显示三角形:
[![green triangle on a pink square](img/6e3ca5483dda3b0fdaab5a319853b012.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qMmODrr1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cm8beqdiysh3x3isbm0h.png)

Chrome 对待`all`非常非常认真:
[![pink square](img/057a35e1b67c52c7bfb72a6b80beca1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8YvTnsnW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3k9mile4tz0kit7o4p6u.png)

wat do? 🤷🏻‍♂️

# 更新

我查阅了 W3C 规范，发现了[这一节](https://drafts.csswg.org/css-cascade-3/#preshint)。我认为它属于这个问题，但我真的不知道如何破译它。