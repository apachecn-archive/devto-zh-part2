# 如何在 Prestashop 的顶部菜单中显示子类别图像

> 原文：<https://dev.to/franken/how-to-show-subcategories-images-in-prestashops-top-menu-4d5n>

[![](../Images/4bffb901332dccccb8af1222c727a73d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BEiW47FM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.prestasoo.com/images/stories/add-image-to-prestashop.root.gif)

几个月前，我写了一篇文章分享如何在 Prestashop 菜单上显示类别图片。

然后@Psdesigner 和@Damien Belgacig 问我如何放置子类别图片而不是主类别图片。对不起，两位，Disqus 没有告诉我你们的评论。

嗯，正如我以前的文章，如果你编辑 ps_mainmenu 模板文件(ps_mainmenu.tpl)并按照我的建议放置源代码，所有类别的图像都会显示在 [Prestashop 菜单](https://www.prestasoo.com/prestashop-modules/soo-magic-menu.html?utm_source=article&utm_medium=article&utm_campaign=subcategoriesimages)上。如果你只想允许子类别的图片，我们需要做一些改变。

## 首先，确保你上传了子类别图片

编辑您的子类别，并上传菜单的图像作为我的前一篇文章。如果你没有至少一张图片，你就不能展示这些图片，对吗？

## 好了，这是最重要的一步。

您需要确保主类别是根菜单，而类别是子菜单。我没有时间玩 Prestashop 默认菜单，所以我不知道我们是否可以设置一个子类别作为根菜单(我们有 SOO Magic 菜单-你还记得吗？我们非常喜欢它，希望人们每天都用它(:D)。

重新打开你的-website/themes/classic/modules/PS _ main menu/PS _ main menu . TPL 文件，找到第 27 行左右的代码。

请访问我的页面，了解如何使用您的 Prestashop 网站:[https://www . prestasoo . com/blog/how-to-show-subcategories-images-in-Prestashop-s-top-menu . html](https://www.prestasoo.com/blog/how-to-show-subcategories-images-in-prestashop-s-top-menu.html)