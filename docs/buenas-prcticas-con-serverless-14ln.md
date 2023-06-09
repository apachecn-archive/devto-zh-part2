# 无服务器的最佳做法

> 原文：<https://dev.to/yorodm/buenas-prcticas-con-serverless-14ln>

# 无服务器框架的一些良好做法

虽然我用[【无服务器】](http://serverless.com)的时间很短，但我一直尝试着去写一套写模板的练习。我在这里分享给像我一样刚起步的人。

## 减少对外部价值的依赖。

在我发现的几个例子中(包括在官方网站上)，在整个模板中使用外部值引用是常见的。

```
functions:
  hello:
    name: ${env:FUNC_PREFIX}-hello
    handler: handler.hello
  world:
    name: ${env:FUNC_PREFIX}-world
    handler: handler.world 
```

Enter fullscreen mode Exit fullscreen mode

虽然这本身并不是一个坏做法，但它使我们的功能依赖于一个“T0”只我们将从环境中获得的值。∞如果明天我们决定该值来自另一批的输出呢？

幸运的是，我们可以做到这一点:

```
custom:
    func_prefix: # cualquier fuente para el valor
functions:
    hello:
        name: ${self:custom.func_prefix} 
```

Enter fullscreen mode Exit fullscreen mode

这样，外部值从属关系发生在一个点上，并传播到模板的其馀部分。

## Usar 插件。

模板可以使用插件，在某些方面添加了从新语法到访问我们使用的供应商特殊功能的功能。开始制作脚本或[伤害山羊](https://en.wikipedia.org/wiki/Animal_sacrifice)之前，你应该咨询一下是否有人 a [制作了插件](https://www.npmjs.com/search?q=serverless)

## 按条件包含在模板中。

Serverless 不支持有条件的模板评估，因此，如果您打算创建基于部署的“T0”阶段的内容，则可以执行以下操作:

```
# stage_properties-qa.yml
functions:
    qa_function:
        handler: MyQaHandler

# stage_properties-prod.yml
functions:
    prod_function:
        handler: MyProdHandler

# serverless.yml
custom:
    stage: ${opt:stage, "qa"}
functions: ${file(stage_properties-${self:custom.stage}.yml):functions} 
```

Enter fullscreen mode Exit fullscreen mode

你现在有了符合**阶段价值的`functions`内容，如果你使用多种配置环境，这种结构可以节省你大量的工作和潜在的并发症。**

 **## 其他线索或技巧。

其他线索或伎俩？请在评论中分享**