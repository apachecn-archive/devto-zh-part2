# #nil 之间的区别？，#空？，#空白？，还有#present？

> 原文：<https://dev.to/appsignal/differences-between-nil-empty-blank-and-present-5bne>

Ruby 和 Rails 有几种方法可以用来检查对象的值或状态是否存在。Ruby 提供了`#nil?`和`#empty?`，Rails 的 ActiveSupport 增加了`#blank?`和`#present?`。所有这些都以自己的方式工作，了解它们如何评估数据很重要，因为在代码中使用错误的方法可能会导致意想不到的结果。

在本文中，我们将更新您对这些方法的了解。我们将(重新)学习当每个方法被使用时什么条件通过或者失败，以及每个方法可以被使用的对象的类型。我们甚至会在最后附上一张方便的小抄！

## #无？

`#nil?`是`Object`类上的 Ruby 方法。因为所有的类都继承自这个类，`#nil?`可以用在任何对象上。对于`nil`(类`NilClass`的一个实例)，它返回`true`，对于其他所有的东西，它返回 false。

```
nil.nil?
=> true

true.nil?
=> false

5.nil?
=> false

"".nil?
=> false

[].nil?
=> false 
```

Enter fullscreen mode Exit fullscreen mode

## #空？

`#empty?`是一种可以在字符串、数组、哈希和集合上使用的方法。如果对象的实例长度为零，它将返回`true`。

当用于字符串时，它返回空字符串的`true`。

```
"".empty?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

注意空白字符是字符，所以在只包含空白的字符串上使用`#empty?`将返回`false`。因此，这可能不是验证文本时使用的最佳方法。例如，如果您只使用这个方法来验证用户输入，那么您可能最终会处理只包含空白的输入。

```
" ".empty?
=> false

"\t\n".empty?
=> false 
```

Enter fullscreen mode Exit fullscreen mode

对于数组、散列和集合，如果它们没有元素，它将返回`true`。

```
[].empty?
=> true

{}.empty?
=> true

require 'set'
Set.new.empty?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

当`#empty?`被用于`nil`或任何其他没有该方法的类时，将会引发一个`NoMethodError`异常。所以要用它来检查你的数据，你必须进行更多的检查来确保你的程序永远不会崩溃。

```
if !myVar.nil? && !myVar.empty?
  # make use of myVar
end 
```

Enter fullscreen mode Exit fullscreen mode

一个更好的方法是 Rails `#blank?`方法，我们现在来看看。

## #空白？

`#blank?`是一个 Rails 方法(在 ActiveSupport 中)。它可以作用于任何物体。

对于字符串，它将为空字符串以及只包含空白字符的字符串返回`true`。

```
"".blank?
=> true

" ".blank?
=> true

"\n\t".blank?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

对于数组、散列和集合，它就像`#empty?`一样工作，如果它们没有元素，它就返回`true`。

```
[].blank?
=> true

{}.blank?
=> true

Set.new.blank?
=> true

[nil].blank?
=> false

["", ""].blank?
=> false

person = {:firstName => "John", :lastName => "Doe"}
person.blank?
=> false 
```

Enter fullscreen mode Exit fullscreen mode

对于`true`返回`false`，对于任何虚假条件(即`nil`和`false`)返回`true`。

```
true.blank?
=> false

false.blank?
=> true

nil.blank?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，在验证数据时最好使用`#blank?`而不是`#empty?`,因为当它用于某些对象时不会导致`NoMethodErrors`,因为它适用于所有对象。它也不适用于只包含空白的字符串。

## #目前？

`#present?`也是 Rails 方法。它的作用与`#blank?`相反。

```
!myVar.blank? == myVar.present?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

下面是一些例子:

```
"".present?
=> false

" ".present?
=> false

[].present?
=> false

nil.present?
=> false

true.present?
=> true

false.present?
=> false

{}.present?
=> false

person = {:firstName => "John", :lastName => "Doe"}
person.present?
=> true

5.present?
=> true 
```

Enter fullscreen mode Exit fullscreen mode

这也是一个比`#empty?`更好的验证方法，原因和我们讨论`#blank?`的原因一样。

## 总之

由于这些方法的命名容易混淆，这里有一个方便的备忘单供您添加书签。

|  | `#nil?` | `#empty?` | `#blank?` | `#present?` |
| --- | --- | --- | --- | --- |
| `5` | `false` | `NoMethodError` | `false` | `true` |
| `""` | `false` | `true` | `true` | `false` |
| `" "` | `false` | `false` | `true` | `false` |
| `"\t\n"` | `false` | `false` | `true` | `false` |
| `[]` | `false` | `true` | `true` | `false` |
| `["a"]` | `false` | `false` | `false` | `true` |
| `{}` | `false` | `true` | `true` | `false` |
| `{a: "b"}` | `false` | `false` | `false` | `true` |
| `Set.new` | `false` | `true` | `true` | `false` |
| `nil` | `true` | `NoMethodError` | `true` | `false` |
| `true` | `false` | `NoMethodError` | `false` | `true` |
| `false` | `false` | `NoMethodError` | `true` | `true` |

和所有事情一样，在`#nil?`、`#empty?`、`#blank?`和`#present?`之间的选择取决于具体情况。例如，为了检查用户是否有名字来称呼他们，`#present?`可能是一个很好的选择。然而`#blank?`和`#present`为所有对象返回一个布尔值，这可能会隐藏代码中的错误，因为当值属于错误类型时不会引发错误。

我们希望我们刷新了您在 Ruby 中检查值的记忆。如果您有任何问题或评论，请不要犹豫，留下您的评论。

本文由客座作者[乔伊斯·埃切萨](https://twitter.com/joyceechessa)撰写。Joyce 是一名全栈开发人员。