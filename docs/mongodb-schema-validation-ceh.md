# MongoDB 模式验证

> 原文：<https://dev.to/delbussoweb/mongodb-schema-validation-ceh>

你们好吗？今天在阿雷格里港，在蒙戈布训练了一周之后。由于其中一个主题是文档验证，所以我想与大家分享一个关于此功能的观点，它正变得越来越重要。

## Porque validar？

好吧，我得承认，我第一次听到关于方案验证的消息时，我想:伙计，为什么要把它委托给一家以业绩为主要特征的银行？

是的，因为当我们想到 NoSQL 银行时，第一个想到的就是:我会录下任何东西，太好了！实际上，在使用 NoSQL 启动的大多数项目中，这是事实上的情况，但是随着应用程序的增长以及数据和访问量的增加，需要的组织也越来越少。这就是模式验证！

## 验证在蒙戈布，这是新的吗？

不，不。文档验证最初是在 3.2 版中引入的，此后发生了很大变化。一开始害羞地支持几个运营商，如今却有了一个独一无二而且相当强大的运营商！

从 3.6 版开始，我们有$jsonSchema，它基于[JSON schema](http://json-schema.org/)的 draft 4。

指定后，将在使用*validator*选项创建的集合中的每次插入或更新时进行验证，该选项将具有验证规则或表达式。

与所有可能影响银行性能的函数一样，方案验证可以随时打开或关闭，并且在这些情况下具有某些行为。

## 验证 e 级验证动作

我们可以对现有的包含文档的集合使用模式验证，方法是使用命令 colmod，也可以在创建新集合时使用命令[中的 *validator* 选项指定规则](https://docs.mongodb.com/v3.2/reference/method/db.createCollection/#db.createCollection)

*validationLevel* 确定 mongodb 在更新过程中如何对现有文档应用验证规则。已有*验证*选项决定 mongodb 是否会传回**错误**并拒绝违反验证规则的文件，或将 **warn** 与这些违规记录在日志中，并允许这些文件

## 文档存在

与 MongoDB generica OS documents exists 的 Podemos controlar como 和 a op*validation level*。

默认情况下，*validation level*为**strict**，mongodb 将在所有插入和更新中应用验证规则。将 *validationLevel* 指向 **moderate** ，将使规则适用于符合所有验证要求的插入和更新以及现有文档。不会检查集合中不符合这些规则的现有文档。

## 应用简单验证

在此示例中，我将创建一个名为 customers 的集合，在其中我将请求“文档”、“名称”和“邮政编码”字段。让我们看看它是什么样子:

```
db.createCollection("clientes", {
    validator: {
       $jsonSchema: {
          bsonType: "object",
          required: [ "documento", "nome", "endereco.cep" ],
          properties: {
                "documento":{
                    bsonType: "string",
                    description: "O Documento é um atributo requerido e contém o CPF do cliente",
                    maxLength: 11,
                    minLength: 11,
                },
                "nome":{
                    bsonType: "string",
                    description: "O Nome é um atributo requerido"
                },
                "endereco.cep": {
                    bsonType: "string",
                    description: "O CEP é um atributo requerido",
                    maxLength: 8,
                    minLength: 8
                }
            }
        }
    }
}); 
```

让我们更详细地了解一下我在验证器中指定的每个选项:

### $jsonSchema

这是使用模式验证和访问更多资源的主要对象。在其中，我们为*bsod 类型指定了*对象类型，然后宣告*阵列*，该阵列将包含文档中所需属性的名称。

### 属性

在此级别上，我们为每个属性设置验证属性，或者更准确地说，我们按属性深入了解属性，而不仅仅是属性的必需属性。

请注意，对于每个属性，我们都放置了不同的属性，可以设置*bsod 类型*，提供说明，甚至可以设置最小和最大大小。

属性中允许使用多个*关键字*，并允许我们进行大量的验证组合。详见这些 [*关键词*在此](https://docs.mongodb.com/manual/reference/operator/query/jsonSchema/#op._S_jsonSchema)

这些是在集合中启用验证的最小属性。

## 接受或拒绝无效文件

正如在验证中控制此“ok”或“não ok”过程的人所说的那样，“T0”验证。

根据本例中定义的方案，使用验证箭头**错误**，将拒绝所有不在规则范围内的文档，mongodb 将返回错误消息。

现在对于动作**警告**文档将被接受(插入或更新)，但将生成日志记录，请看以下示例:

```
db.clientes.insert( { nome: "Leandro", status: "Atualizado" } ) 
```

```
2017-12-01T12:31:23.738-0500 W STORAGE  [conn1] Document would fail validation collection: testeSchema.clientes doc: { _id: ObjectId('5a2191ebacbbfc2bdc4dcffc'), nome: "Leandro", status: "Atualizado" } 
```

## 限制和旁路

我们无法指定对银行集合 **admin** 、 **local** 、 **config** 的验证。也不适用于收藏**系统。** *

一些*角色*可能被允许在模式验证中给出*旁路*，它们是:**[【db admin】](https://docs.mongodb.com/manual/reference/built-in-roles/#dbAdmin)**和 **[恢复](https://docs.mongodb.com/manual/reference/built-in-roles/#restore)在启用身份验证的环境中，验证时必须使用选项，但提示:不要将此选项告诉很多人！rssss(消歧义)**

## 结论

好了，伙计们，就这样-我...。我希望，随着我们的数据增长，我可以帮助您了解更多有关此功能的信息。如需更多资讯，请参阅 mongodb 的官方文件页[此处](https://docs.mongodb.com/manual/core/schema-validation/#behavior)

下次见-我...。

PS:我开始在阿雷格里港写作，结束在里约热内卢！；）