# 多个活动记录关联

> 原文：<https://dev.to/savyounts/multiple-activerecord-associations-4o4o>

嘿，我知道有了 ActiveRecord，我就可以拥有像 belongs_to、has_many 和 has_many through 这样的关联，但是如果我有一个模型与同一个第二个模型有两个关联，那该如何工作呢？

例如，如果我有三个类模型 Foo、Bar 和 Baz。如果

类 Foo
has _ many:baz
has _ many:bar
has _ many:bar，通过::baz
end

基本上，我希望我的 foo 概念能够创建一个酒吧，但也有酒吧通过巴兹。我可以按照我在那里设置的方式使用它吗(与设置的其他关联相匹配)，如果可以，我能打电话吗？foo 对象上的 bar 从两个关联中获取所有的 bar？