# 为什么不能在输入字段上使用多种类型？

> 原文：<https://dev.to/krantzinator/why-cant-we-use-multiple-types-on-input-fields-1c6k>

我尝试过，但没有找到一个纯粹的 CSS 方法来制作一个密码风格的输入字段，也可以在 Firefox 的移动设备上弹出一个数字键盘。(对于一个失败的例子，不赞成在`type=number`输入上支持`text-security: disc`。)

如果我们可以说一个输入同时具有`password`和`number`或者`tel`的类型，这个问题似乎就可以解决了。

`input`是如何工作的，为什么我们不能这样设计浏览器？