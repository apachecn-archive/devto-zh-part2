# 禁用预商店添加到购物车弹出菜单-逐步

> 原文：<https://dev.to/franken/disable-prestashop-add-to-cart-popup---step-by-step-1839>

[![](../Images/b7f8bd1a02450090cd3e0253be5549b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tmUGOK-v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prestasoo.com/images/stories/disable-prestashop-add-to-cart-popup.jpg)

许多人希望禁用用 ajax 向购物车添加产品时显示的弹出窗口，而是重定向到购物车摘要页面。我见过很多帖子指示在购物车模块中[禁用**添加到购物车弹出**](https://www.prestasoo.com/blog/disable-prestashop-add-to-cart-popup-step-by-step.html) ，但是只对 Prestashop 1.6 有效。

在 Prestashop 1.7 中，特性“将产品添加到购物车后重定向”不再存在，在这种情况下，您可能需要禁用属于购物车模块的 **Ajax Cart** 特性。不幸的是，如果您禁用此功能，弹出窗口将不会显示，但它不会将您的客户重定向到购物车摘要页面。

在这个 [Prestashop 教程](https://www.prestasoo.com/Blog/)中，我将向您展示如何**禁用 Prestashop 添加到购物车弹出菜单**，重定向到 Prestashop 1.7 上的购物车摘要。