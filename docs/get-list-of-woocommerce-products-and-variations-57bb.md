# 获取 WooCommerce 产品和变体列表

> 原文：<https://dev.to/elcotu/get-list-of-woocommerce-products-and-variations-57bb>

大家好。我刚刚匆忙地用 JavaScript 做了一些事情，我想看看其他人会如何解决它。只是为了比较，因为我不太擅长 JavaScipt，所以我认为答案会很丰富。

他们给了我一个 WooCommerce 网站，上面加载了产品和变化，我需要在 ERP 上列出一个清单来做其他事情。列表应该有:
1)产品 ID
2)变更 ID
3)变更 SKU

我不得不使用两个 WooCommerce 端点:

/wp-json/wc/v2/products

这将列出所有产品。每个产品都有一个 id 字段

/WP-JSON/WC/v2/products//变体

这列出了给定产品 id 的所有变体。每个变体都有一个 id 字段和一个 sku 字段。

这就是挑战，获取列表并将其保存为 CSV 文件。你会怎么做？

你好，