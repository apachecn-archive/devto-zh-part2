# 重新排序-寻找意见和贡献者！

> 原文：<https://dev.to/robbporto/reshort---looking-for-opinions-and-contributors-2f2p>

[Reshort -在你的动作创作者里少写！](https://github.com/robbporto/reshort)

## [T1】简介](#intro)

大家好！我开发了这个简单的小库(我的第一个库！)来帮助我减少我的动作创作者中的一些重复。我有一些新实现的想法，我只是想看看是否有人对此感兴趣，或者觉得这个项目有趣和有用。如果你有任何建设性的批评，我会很高兴听到它。如果你对这个项目感兴趣，并能提供帮助，那就太棒了！

## 问题:

假设我想获取一个书单。使用 redux，我必须创建三个动作创建器:

1.  第一个是“请求”；
2.  第二个是“成功”；
3.  第三个是“失败”。

现在假设我想获取一个作者列表。或者用户列表。或者书签列表...这种请求-成功-失败的模式会变得非常重复和冗长。

## 解

使用`reshort`你可以用一行代码创建一个“完整的动作创建器”,而不是三行。像这样:

```
import reshort from "reshort";

const productsActions = reshort("Products");

productsActions("request")
// {
//   type: "GET_PRODUCTS"
// }

productsActions("success", {test: 123})
// {
//   type: "GET_PRODUCTS_SUCCESSFUL",
//   payload: { test: 123 }
// }

productsActions("fail", {test: "error"})
// {
//   type: "GET_PRODUCTS_FAILURE",
//   payload: { test: "error" }
// } 
```