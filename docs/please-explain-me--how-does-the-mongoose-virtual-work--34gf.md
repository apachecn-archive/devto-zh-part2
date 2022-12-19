# 请解释一下😅mongoose 虚拟()是如何工作的？

> 原文：<https://dev.to/mandaputtra/please-explain-me--how-does-the-mongoose-virtual-work--34gf>

刚刚发现猫鼬有虚拟()选项。可以通过引用结合其他文档的方式吗？以及如何使用？我发现许多文章解释了如何使用它，但只是通过简单例子链接。
[futurestudio 博客](https://futurestud.io/tutorials/understanding-virtuals-in-mongoose)
[doc 本身](https://mongoosejs.com/docs/2.7.x/docs/virtuals.html)

```
UserSchema.virtual('companies', {
    ref: 'Company',
    localField: '_id',
    foreignField: 'users.user',
    justOne: false,
}); 
```

Enter fullscreen mode Exit fullscreen mode

在 github 上找到了这段代码，但仍然不明白它的意思...以及如何使用它。

[github 中的代码](https://github.com/brianalois/node_rest_api_mongo/blob/master/models/user.model.js)

Thanks ! ☺️