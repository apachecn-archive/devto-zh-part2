# 使用中间件转换 Laravel 请求数据

> 原文：<https://dev.to/samolabams/transforming-laravel-request-data-using-middleware-2k7j>

[![](../Images/5eba7d22ed116afceb97b815811e6bf1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tNCTnO78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/384/1%2ACgNQ3ERM67ENaQTKSiG22A.jpeg)

有时候，我们需要格式化表单输入值，以使它们更加用户友好，比如使用输入屏蔽来格式化电话号码(XXX-XXXX-XXXX)或货币(X，XXX，XXX)。这取决于我们的数据层需求。这种值在传递给验证器或保存在数据库中之前，可能需要进行规范化或转换。

本教程假设读者掌握了大量的 Laravel 知识。如果你是第一次接触 laravel，你可能想要 [*查看文档*](https://laravel.com/docs/) *。*

### **我们的方法**

我最近发现的一种方法是创建一个中间件类，用于在控制器处理请求之前转换请求数据。由于中间件提供了一种方便的机制来过滤进入我们应用程序的 HTTP 请求，我们可以利用它来转换我们的数据。让我们这样做:

### 第一步:创建我们的中间件:

用 *make:middleware* artisan 命令创建一个新的中间件。

```
php artisan make:middleware TransformData 
```

Enter fullscreen mode Exit fullscreen mode

打开*app/Http/Middleware/transform data . PHP*文件，粘贴以下代码: