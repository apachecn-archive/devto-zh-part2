# 目的地费用成条状

> 原文：<https://dev.to/mehraas/destination-charges-in-stripe--2lln>

以下是创建目的地费用
[https://stripe.com/docs/connect/destination-charges](https://stripe.com/docs/connect/destination-charges)的 stripe 文档的链接

```
// Set your secret key: remember to change this to your live secret key in production
// See your keys here: https://dashboard.stripe.com/account/apikeys
var stripe = require("stripe")("sk_test_############");

stripe.charges.create({
  amount: 1000,
  currency: "usd",
  source: "tok_visa",
  destination: {
            account: "{CONNECTED_STRIPE_ACCOUNT_ID}",
          },
}).then(function(charge) {
  // asynchronously called
}); 
```

什么是连接条带帐户 ID？我该怎么找回它？