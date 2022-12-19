# Ruby Web APIs 的快速类型检查序列化程序

> 原文：<https://dev.to/jgaskins/fast-type-checked-serializers-for-ruby-web-apis-2o19>

当您需要两个不同的进程能够相互对话时，它们需要能够连接并使用同一种语言。如果其中一个是 web 应用程序，那么你使用的是 HTTP(以及它所基于的所有东西)，所以它涵盖了大部分内容。如果您发送请求并接收序列化为 JSON 的响应，那就更接近了。但是我们仍然需要弄清楚最后一英里——我们将发送和接收哪些 JSON 对象？

如果我们的 web 服务器运行在 Ruby 上，我们只需要将模型转换成散列，将集合转换成数组。到 JSON 的转换是一个简单的`to_json`调用。我们如何将它们转换成那些散列和数组是要做的选择。

## 转换选项

有很多 gem 可以将任意的 Ruby 对象转换成 JSON 可序列化的格式。其中最著名的是 [`active_model_serializers`](https://github.com/rails-api/active_model_serializers) ，但是几周前网飞宣布[他们自己的](https://medium.com/netflix-techblog/fast-json-api-serialization-with-ruby-on-rails-7c06578ad17f)系列宝石叫做`fast_jsonapi`。

对于这两种 gem，您可以像这样声明您的序列化器:

```
class OrderSerializer < ActiveModel::Serializer
  attributes(
    :id,
    :customer_name,
    :customer_id,
    :line_item_ids,
    :delivery_address,
    :delivery_instructions,
    :total_price,
  )
end 
```

Enter fullscreen mode Exit fullscreen mode

## 那些选项的不利方面

这两种宝石都很棒。通过查看这个序列化器，您可以确切地知道它将发送什么字段。他们缺少的是对他们发出的数据类型的验证。`delivery_address`是一个字符串还是被分解成一个散列，每个地址都有一部分？这些字段中哪些可以是`nil`？`total_price`是作为一个字符串发送以用于表示，还是作为一个浮点数发送以用于计算，还是作为一个整数发送以避免浮点错误？这些不可能通过查看序列化程序中的属性声明来判断。

您可能会想“为什么我希望在动态语言中进行类型检查？!"虽然我可以看到静态类型的好处，但我真的很享受动态类型给我们构建应用程序带来的自由。然而，当您有两个不同的应用程序，它们必须都同意如何相互通信时，您需要 100%确定所有的细节，就像在咨询协议中得到合同细节一样。添加单元测试来验证序列化程序发出正确的数据类型需要做大量的工作。如果序列化程序为我们做到了这一点，甚至让我们知道我们的数据在生产中是否不正确，那会怎么样？

如果我没有解决方案，我就不会写这个了。进入 [`primalize`](https://github.com/jgaskins/primalize) ，一个带有开箱类型检查的序列化 gem。Primalize 之所以如此命名，是因为它将更高级的对象转换为它们的原始对应物，但它是以一种智能的方式完成的。

## 它是如何工作的？

让我们从声明一个序列化器开始:

```
class OrderSerializer < Primalize::Single
  attributes(
    id: string, # UUIDs for primary keys are great
    customer_name: string,
    customer_id: string,
    line_item_ids: array(string),
    delivery_address: primalize(AddressSerializer),
    delivery_instructions: optional(string),
    total_price: integer,
  )
end 
```

Enter fullscreen mode Exit fullscreen mode

注意我们声明的属性都有类型。我们说`id`是一个`string`，`line_item_ids`是一个包含`string`值的`array`。`total_price`是一个`integer`。这些都很容易理解，它们确切地告诉我们我们得到了什么。

请注意，`delivery_instructions`属性被标记为`optional`。这意味着它可以是`nil`。如果一个属性没有被标记为`optional`，它就不可能是`nil`。

这里列出了`Primalize`支持的模型序列化程序的各种类型:

*   `integer`:整数
*   `float`:浮点数
*   `number`:任意数值
*   `string`:文字
*   `boolean`:显式`true`或`false`(非“真”或“假”值)
*   `array(*types)`:包含指定类型值的数组
    *   示例:`array(string, integer)`
*   `optional(*types)`:任何指定类型或`nil`
    *   例如:`optional(string)`、`"foo"`和`nil`都是可接受的值
*   `enum(*values)`:必须是指定值之一
    *   示例:`enum('requested', 'shipped', 'delivered')`
*   `timestamp`:一个`Date`、`Time`或`DateTime`值
*   `any(*types)`:给定类型的任意值
    *   示例:`any(string, integer)`将只匹配字符串和整数
    *   如果没有指定类型，任何值都将匹配
*   `primalize(YourPrimalizerClass)`:用给定的`Primalize::Single`子类初始化指定的属性
    *   示例:`primalize(OrderSerializer)`
*   `object(**types)`:指定结构的哈希
    *   示例:`object(id: integer, name: string)`
    *   只需要指定所需的键。散列的其余部分将通过。
    *   如果没有指定键，所有键都是可选的，它将匹配任何散列。
    *   Ruby 对象已经定义了一个名为`hash`的方法，用于解析散列键和确定`Set`包含，所以我们不得不使用一个更加与语言无关的名字`object`。如果我们使用了`hash`，那么实际上就不可能使用序列化类作为散列键或者将它存储在`Set`中。

这些类型声明是可组合的，所以如果我们的 API 需要，我们可以设置一些非常复杂的类型声明:

```
attributes(
  user: object(
    name: optional(string),
    email: string,
    nicknames: array(string),
    role: optional(enum('user', 'agent', 'manager', 'admin')),
  ),
) 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您发现自己编写了许多嵌套对象，那么可能值得将它们提取到另一个序列化器中并使用`primalize(ThatSerializer)`。

## 如果类型检查失败会怎样？

在 Ruby 中，我们不能强制我们的模型没有错误的属性类型，因为任何变量都可以保存任何值，但是我们可以在序列化的时候运行类型检查。

序列化器有一个默认的类型不匹配处理程序，它是一个能够接收`serializer_class, attribute, type, value`的`call`对象(比如，响应`call`)。默认情况下，它会引发一个异常，这对于开发环境来说非常好。但是，在生产中，最好是让它通过，同时仍然发送警报。

通过将`MySerializerClass.type_mismatch_handler`设置为您喜欢的处理方法，您可以定制当单个序列化程序类出现类型不匹配时您应该做什么。要为所有型号的串行器*设置它，使用`Primalize::Single.type_mismatch_handler`。比如:* 

```
MySerializerClass.type_mismatch_handler = proc do |serializer, attr, type, value|
  message = "#{serializer}##{attr} is specified as #{type.inspect}, but is #{value.inspect}"

  Slack.notify '#bugs', message
  BugTracker.notify message

  # the return value of the block is the value to be used for that attribute
  value
end 
```

Enter fullscreen mode Exit fullscreen mode

## 属性转换

如果一个属性不是您所期望的类型，您可以为它的类型声明提供一个块，以指定如何将它强制转换为该类型。例如，如果一个`Address#city`返回一个`City`对象而不仅仅是名称，我们可以这样序列化它:

```
class AddressSerializer < Primalize::Single
  attributes(
    city: string { |city| city.name },
    # ...
  )
end 
```

Enter fullscreen mode Exit fullscreen mode

这表明我们将对地址的`city`属性中的城市调用`name`。这里仍然会进行类型检查。如果该块的结果不是字符串，我们将触发类型不匹配。

## 虚拟属性

有时，您希望提供在被序列化的模型中实际上不存在的属性。就像我们的服务器端域模型不需要匹配数据库模式一样，客户端也不需要知道服务器端模型没有特定的属性。对于那些“虚拟”属性，您可以定义一个方法，该方法将从模型*拥有的*中计算属性:

```
class AddressSerializer < Primalize::Single
  attributes(
    latitude: float,
    longitude: float,
    # ...
  )

  def latitude
    object.coordinates.latitude
  end

  def longitude
    object.coordinates.longitude
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 复合串行化器

那只是个别模型序列化器。还有对在单个响应中返回相关对象的一流支持，而不用用`primalize(AnotherSerializer)` :
嵌套它们

```
class OrderResponse < Primalize::Many
  attributes(
    order: OrderSerializer,
    line_items: enumerable(LineItemSerializer),
    address: AddressSerializer,
    customer: CustomerSerializer,

    # Only required for corporate accounts
    purchase_order: optional(PurchaseOrderSerializer),
  )
end 
```

Enter fullscreen mode Exit fullscreen mode

我通常将它们称为“响应序列化器”，虽然可以在 API 响应中只序列化域模型，但我几乎总是将它们包装在这样的序列化器中，以防将来需要返回相关的模型。如果您有不受您控制的 API 消费者，一旦您开始将模型作为顶级对象返回，大多数时间您都会被它所困扰。

要使用这个序列化器，您可以用您给它的键来实例化它:

```
OrderResponse.new(
  order: order,
  line_items: order.line_items,
  address: order.delivery_address,
  customer: order.customer,
  purchase_order: order.purchase_order,
) 
```

Enter fullscreen mode Exit fullscreen mode

不为您遍历对象图，这可能会觉得不方便，但这是预期的功能。它确保您可以自定义您要发送的内容。例如，如果您的`order`是一个 ActiveRecord 模型，您可能不想将`line_items`的所有一起发送给*。出于某种商业原因，你可能会把它们分成`taxable_line_items`和`non_taxable_line_items`两部分。如果序列化程序为您遍历了关联，您可能无法在不在`Order`模型上添加方法的情况下指定它。不过，使用 Primalize，您可以像这样设置您的序列化程序:* 

```
class OrderResponse < Primalize::Many
  attributes(
    # ...
    taxable_line_items: enumerable(LineItemSerializer),
    non_taxable_line_items: enumerable(LineItemSerializer),
  )
end

OrderResponse.new(
  # ...
  taxable_line_items: order.line_items.taxable,
  non_taxable_line_items: order.line_items.non_taxable,
) 
```

Enter fullscreen mode Exit fullscreen mode

类型检查也在响应序列化器中发挥作用。例如，如果我们在指定了`enumerable(LineItemSerializer)`的地方传递了一个行项目，我们会得到一个错误。如果我们为一个字段传递了`nil`而没有将它指定为`optional`，我们也会得到一个错误。

所有这些都有助于创建更健壮的 API 端点。

## JSONAPI

一些客户端应用程序可能以更适合 JSONAPI 响应结构的方式编写。对于这样的应用，有一个 [JSONAPI 包装器](https://github.com/jgaskins/primalize-jsonapi)用于 Primalize。

## 表现

我在题目中提到这些连载器速度很快，但我还没有触及到那部分。当我[对 AMS 和网飞的`fast_jsonapi` gem](https://github.com/Netflix/fast_jsonapi/pull/42#issuecomment-364693269) 进行基准测试时，它的速度是 AMS 的 1200%以上，大约是网飞 gem 的一半。当你考虑到`fast_jsonapi`的卖点是…嗯…速度很快，和 Primalize 连载 1000 个以上的模型相差 12ms(每个不到 12 s)，还是够接近的。在典型的有效负载上(几十种型号，也许 100 种)，垃圾收集暂停的时间会比它们之间的差异长。

此外，该基准测试使用了 JSONAPI 包装器，它做了相当多的工作(包括它自己对相关模型的简单遍历)。如果你不需要那种特定的格式，你可以坚持使用`Primalize::Many`来节省大约 25%的时间。

我不知道你怎么想，但是我当然愿意用 12 毫秒(在典型的有效载荷上更少)来换取我的响应有效载荷与客户期望相匹配的安心。

## 未来发展

Primalize 是一个基于我在以前的雇主那里实现的模式的宝石。它已经在生产中使用了大约两年，并且显著地稳定了他们的 API 和它的消费者之间的通信，同时也提高了它的性能。尽管如此，仍有改进的方法。

该雇主的一位同事提出的一个想法是能够生成 RAML 或 Swagger 文档。例如，如果一个 API 端点返回一个`OrderResponse`，那么您应该能够为该端点生成一个客户所期望的精确结构。RAML 文档可以[导入到 Postman](http://blog.getpostman.com/2015/11/04/supporting-raml-folders-in-postman/) 中，以便于测试和使用 REST APIs。

无论是在基线`Primalize`类还是在`JSONAPI`包装器中，性能都有可能进一步提高。

如果你愿意投稿，我随时欢迎你的对话( [gitter](https://gitter.im/jgaskins/primalize) / [twitter](https://twitter.com/jamie_gaskins) /对这篇文章的评论)[建议](https://github.com/jgaskins/primalize/issues)，以及[拉动请求](https://github.com/jgaskins/primalize/pulls)。谢谢大家！❤️