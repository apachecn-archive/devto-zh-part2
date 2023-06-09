# Magento 2 的最佳地址自动完成功能

> 原文：<https://dev.to/tigren5/the-best-address-autocomplete-for-magento-2-36k2>

[![address autocomplete for magento 2](img/61f5106b43f9668a382717f81de8031a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GgueGxs4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9jtlwn4mn48iwa5az6v3.jpg)

2018 年 7 月 20 日，Tigren 发布了新的扩展:**[Magento 2](https://www.tigren.com/magento-2-extensions/magento-2-address-autocomplete/)T3 的地址自动完成。这个扩展是为了增强当前 Magento 2 的检验过程而开发的。更具体地说，它允许客户使用自动完成功能快速完成所需的送货字段。现在，用户只需选择其中一个字段，其余的字段将立即自动填充，而不是依次输入所有的地址字段(街道、城市、省/州、分区、邮政编码)。**

此外，Magento 2 扩展的这个地址自动完成(相对于其他类似的 Google 地址自动完成扩展)的最大优势之一是它不依赖于 Google API。管理员可以主动管理和更新地址数据库(输入和输出)。因此，他们不需要根据 API 的变化过于频繁地更新模块，也不需要按月向谷歌支付使用其 API 的费用。

**特色亮点:**

*   在结帐页和地址簿上应用自动完成功能
*   能够选择两种建议类型:自动完成和下拉菜单
*   将 subsidy 字段添加到 Shipping 部分以获得更准确的地址
*   将结账地址从第 2 步(查看和付款)移到第 1 步(发货地址下),以改善客户体验
*   主动将地址数据导入 Magento 2 数据库，而不使用 Google API

**注意:**如果选择下拉建议类型，工作流程就大不相同了。特别是，购物者必须按顺序选择他们的国家、州/省、城市和分区，然后邮政编码将自动填写。同样值得注意的是，选择州/省后，城市列表将立即缩小到与该州/省相关的选项。(对城市和分区重复该过程)。尽管下拉建议的流程不同于自动完成类型，但它们对于减少检查时间都非常有用。

查看 Magento 2 地址自动完成扩展的演示并在此 下载 **[。](https://www.tigren.com/magento-2-extensions/magento-2-address-autocomplete/)**