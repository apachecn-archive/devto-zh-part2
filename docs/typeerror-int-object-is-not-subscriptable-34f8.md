# type error:“int”对象不可订阅

> 原文：<https://dev.to/crhodes2/typeerror-int-object-is-not-subscriptable-34f8>

情况是这样的:我有一个 json 对象，是从第三方源请求的，它有一个条目列表。他们中的 14 个。

我可以通过计数来显示它们。0,1,2,3...13.这是我用来展示它们的。

x = my _ request[" JSON object "][" items "]
for ex in range(x):
print(ex)

但是这些项目中的每一个都有来自内部的附加属性，我想要访问至少一个项目的属性。假设我想访问的是“item_color ”,根据 jSON 文件，它嵌套了其他属性。

这是我在 x:
print(ex[" itemInfo "][" color "])中对 ex 的尝试:

但是我得到一个' int '对象是不可下标的。

有没有一种方法可以让我显示我想在控制台上显示的属性，就像我如何按计数显示所有项目一样？

请在下面找到我正在操作的 json 对象的截图，以便更好地理解。

[https://www . Dropbox . com/s/apao 25 r 9j 2b 22 IR/Screen % 20 shot % 202018-07-25% 20at % 204 . 07 . 36% 20pm . png？dl=0](https://www.dropbox.com/s/apao25r9j2b22ir/Screen%20Shot%202018-07-25%20at%204.07.36%20PM.png?dl=0)