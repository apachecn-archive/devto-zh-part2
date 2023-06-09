# 使 WooCommerce 定制订单状态为应付

> 原文：<https://dev.to/jackharner/make-woocommerce-custom-order-status-payable-27hn>

你有一个自定义的 WooCommerce 订单状态，你希望能够接受付款吗？只需看看`woocommerce_valid_order_statuses_for_payment`滤镜。我今天花了大量的时间寻找这个过滤器。我知道我需要什么，但我不知道它叫什么。我知道有一个`wc_order_is_editable`过滤器，你也可以添加状态，所以我认为必须有一个能够要求支付定制订单状态。

## 使 WooCommerce 定制订单状态为应付

历经千辛万苦，我向你呈现我的发现:

```
function filter_woocommerce_valid_order_statuses_for_payment($array, $instance)
    {
        $my_order_status = array('cancelled', '<custom_order_status>');
        return array_merge($array, $my_order_status);
    }

    // add the filter
    add_filter('woocommerce_valid_order_statuses_for_payment', 'filter_woocommerce_valid_order_statuses_for_payment', 10, 2); 
```

Enter fullscreen mode Exit fullscreen mode

这段非常简单的代码将订单状态列表添加到可以访问结帐或表单支付的状态中。

## 为什么我想通了

我开发了一个工作流程，允许客户在定制订单上收取定金。它包括通过编程将订单状态设置为`deposit_paid`，但客户仍需要支付剩余的余额。通过将`deposit_paid`添加到付款的有效订单状态列表中，当订单准备发货时，客户可以回来支付发票的剩余部分。

> ## It took me a long time to figure it out. I don't know why.
> 
> # What's the latest thing that took you the longest time to figure out?

## 最近的帖子

*   最初发布于此:[使 WooCommerce 定制订单状态为应付](https://harnerdesigns.com/blog/make-woocommerce-custom-order-status-payable/)
*   [big commerce 中的传统 API 帐户](https://harnerdesigns.com/blog/legacy-api-accounts-in-bigcommerce/)
*   [操脸书的文字叠加工具](https://harnerdesigns.com/blog/fuck-facebooks-text-overlay-tool/)
*   [BigCommerce 营销条幅添加额外逗号](https://harnerdesigns.com/blog/bigcommerce-marketing-banners-adding-extra-commas/)