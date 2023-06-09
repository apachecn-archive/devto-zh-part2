# 生产数据从不说谎

> 原文：<https://dev.to/danlebrero/production-data-never-lies-16db>

*本文原载于 [Akvo 的博客](https://akvo.org/blog/production-data-never-lies/)*

Akvo 工程团队已经接受了将[不可变数据日志](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying)作为我们架构主干的想法，我们希望这将使集成 Akvo 系统和构建新功能变得更加容易。更重要的是，它会把数据当成一等公民；因为它是真正有价值的东西。

## 自动模式生成

首要任务之一是将 [Akvo 流](https://akvo.org/products/akvoflow/#overview)中的所有数据从其当前存储，Google Cloud Datastore，移动到不可变的数据日志，在我们的例子中是 [Kafka](https://kafka.apache.org/) 。

考虑到数据的价值，以及我们预计 Akvo 将存在很多年，并且知道我们的软件将会发展的事实，我们希望对数据实施某种程度的质量控制。因此，我们决定使用[汇合模式注册表](https://www.confluent.io/blog/schema-registry-kafka-stream-processing-yes-virginia-you-really-need-one/)，而不是将[JSON blob](https://www.reddit.com/r/javascript/comments/10st04/what_is_a_json_blob/)放入 Kafka。

Schema Registry 将确保添加到 Kafka 的任何数据都符合一个模式，并且随着数据的发展，新的模式将保持与旧的模式兼容，这样旧的数据总是可以被读取。由于 Schema Registry 只支持 [Avro schemas](https://avro.apache.org/) ，在开始迁移之前，我们需要为 Akvo 流中的每个现有类生成一个 Schema。

幸运的是，Java Avro 库附带了 ReflectData 实用程序，它能够从 Java 类生成 Avro 模式，因此我们不需要手动完成这项工作。

如果我们在 Akvo Flow 的一个类 [SurveyedLocale](https://github.com/akvo/akvo-flow/blob/79d5cebe7e985e93ed7c3a732d8360869bfc651b/GAE/src/com/gallatinsystems/surveyal/app/web/SurveyalRestServlet.java) 上进行尝试，ReflectData 会生成以下内容:

```
{
  "type": "record",
  "name": "SurveyedLocale",
  "namespace": "com.gallatinsystems.surveyal.domain",
  "fields": [
    {
      "name": "systemIdentifier",
      "type": "string"
    },
    ...
    {
      "name": "surveyalValues",
      "type": {
        "type": "array",
        "items": {
          "type": "record",
          "name": "SurveyalValue",
          "fields": [
            ...
            {
              "name": "stringValueText",
              "type": {
                "type": "record",
                "name": "Text",
                "namespace": "com.google.appengine.api.datastore",
                "fields": [
                  {
                    "name": "value",
                    "type": "string"
                  }
                ]
              }
            }
          ]
        },
        "java-class": "java.util.List"
      }
    },
    ...
    {
      "name": "key",
      "type": "com.google.appengine.api.datastore.Key"
    },
    {
      "name": "ancestorIds",
      "type": {
        "type": "array",
        "items": "long",
        "java-class": "java.util.List"
      }
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 [Github](https://gist.github.com/dlebrero/f7c2bda8477112f5a8bb2f7d3ad91f62#file-reflectdata-json) 上找到完整的模式。

这是一个很好的起点，我们可以通过让 Avro 以某种方式支持标记为@NotPersistent 的[字段来进一步改进它，这样它们就不会包含在最终的模式中。](https://github.com/akvo/akvo-flow/blob/4fb17f24c758a595427a6109b1ce7609163544b0/GAE/src/com/gallatinsystems/surveyal/domain/SurveyedLocale.java#L71)

但是，这是一个很大的但是，没有提示哪些字段是强制性的，哪些是可选的，也没有提示哪些值对这些字符串、长整型和双精度型有效。

我们可以挖掘代码来找到验证规则，但即使我们对每个类都这样做，Google Cloud Datastore 也不会强制任何模式，所以即使这个类是数据的当前视图，这些规则也可能会随着时间的推移而改变，我们并不真正知道数据存储中有什么。

或者，您可以四处打听，希望有人已经存在多年，并且有惊人的记忆力，此外，从来没有任何错误允许无效数据到达数据存储库，此外，任何业务变化都反映在完美的数据迁移中。

## 数据驱动的方法

但是有一个更明显的事实来源:实际生产数据。为什么不直接从生产数据中生成 Avro 模式呢？最终，这就是我们需要支持的，并且包含了实际使用的所有可能值的例子。

为了帮助我们完成这项任务，我们决定使用[规范提供者](https://github.com/stathissideris/spec-provider)，这是一个 Clojure 库，当给定一个对象列表时，它将生成一个 [Clojure 规范](https://clojure.org/about/spec)。如果你不熟悉 Clojure 规范，你可以把它想象成一个类固醇上的模式 DSL。

但是 Clojure 规范与本文的讨论无关，因为规范提供者有两个不同的阶段:一个阶段生成关于数据类型的统计信息，另一个阶段根据这些统计信息生成 Clojure 规范。我们将只使用第一步，自己完成向 Avro 的转换。

因此，在从 Google Cloud Datastore 中提取所有数据并将[实体](https://cloud.google.com/appengine/docs/standard/java/javadoc/com/google/appengine/api/datastore/Entity)转换为 [Clojure 地图](https://github.com/akvo/akvo-flow-initial-kafka-import/blob/0291f923f08c2b3df4a8467aa9a74e4c772fd7d7/src/gae_to_kafka_initial_import/gae_entity.clj)之后，Spec-Provider 为我们提供了以下统计数据:

```
{:pred-map        {map? {:max-length   27,
                          :min-length   21,
                          :sample-count 463417}}
 :sample-count    463417
 :map             {:keys  {:ambiguous   {:distinct-values #{false}, 
                                         :pred-map        {boolean? {:sample-count 463417}},
                                         :sample-count    463417},
                           :ancestorIds {:distinct-values #{nil},
                                         :pred-map        {nil? {:sample-count 440212}},
                                         :sample-count    440212},
                           :countryCode {:distinct-values           #{nil
                                                                      "RU"
                                                                      "MN"
                                                                      "UG"},
                                         :hit-distinct-values-limit true,
                                         :pred-map                  {string? {:max-length   2,
                                                                              :min-length   2,
                                                                              :sample-count 165067},
                                                                     nil?    {:sample-count 298350}},
                                         :sample-count              463417},
                           ...
                          }
                   }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 Github 上找到完整的数据。

前几行告诉我们，所有实体都是拥有 21 到 27 个字段的映射。他们还告诉我们，我们对近 50 万个实体进行了采样。按照 Spec-Provider 的工作方式，您可以添加任意多的实体，而不会有耗尽内存的风险。然后 Spec-Provider stats 列出所有字段及其类型、可能值的子集、字符串和列表的最小和最大长度。

通过一小段代码，我们可以将这些统计数据转换成 Avro 模式:

```
{:type "record",
 :name "SurveyedLocale",
 :fields ({:name "ambiguous", :type "boolean", :doc "It is always '<false>'. "}
           {:name "ancestorIds", :type "null", :doc "Always nil"}
           {:name "countryCode", :type ("null" "string"), :doc "Examples: <RU>, <MN>, <TD>. Size [2,2]. Nil 64.38%"}
           {:name "surveyInstanceContrib",
            :type ("null"
                    {:type "array",
                     :items {:type "long", :doc "Examples: <1520050>, <2310465>, <1840061>. Range [1520050,2043500042]. "}}),
            :doc "Size [1,88]. Nil 3.33%"}
           ...
           ),
 :doc "Size [21,27]. ",
 :namespace "org.akvo.flow"} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 [Github](https://gist.github.com/dlebrero/f7c2bda8477112f5a8bb2f7d3ad91f62#file-final-schema-edn) 上找到完整的模式。

看着这个模式，我们看到了一些非常有趣的东西:

*   “模糊”字段始终为假。不是一个非常有用的标志。
*   有 11 个字段始终为空。我们为什么需要它们？
*   在 27 个字段中，有 22 个字段可以为空。这让我想起了 Rich Hickey 在 [Maybe type](https://www.youtube.com/watch?v=2V1FtfBDsLU&feature=youtu.be&t=49m57s) 上的最后一次咆哮。
*   0.003%的实体的“localeType”字段为空。如果你问团队这个字段是不是必填的，你就能猜到答案。
*   国家代码始终是两个字母的字符串。
*   显示名称可以是空字符串。
*   surveyInstanceContrib 数组最多可以包含 88 个项目。
*   纬度/经度坐标有无效数据，因为范围是[-1307822.0，698259771924]，或者数据可能不是来自这个星球。
*   我们不需要处理@NotPersistent 字段。
*   文档中有一些真实的例子，会非常有用。
*   这些信息和模式为我们提供了一个比之前更好的起点。

这些信息和模式为我们提供了一个比之前更好的起点。

## 接下来是什么？

当然，这种方法并不专门用于产生 Avro 模式。您还可以生成其他类型的模式，比如[协议缓冲区](https://developers.google.com/protocol-buffers/)，或者甚至可以直接生成您的类。

Avro 型系统不是很丰富。对于所有额外的信息，我们能做的最好的事情就是把它放在一个文档中，但是我们可以通过生成一些代码来进行额外的检查来补充它。

在 Akvo 流的例子中，我们有几十个实体，每个实体有几十个字段。有了这些统计数据，我们可以很容易地创建一个可疑字段的列表:要么是 null 百分比很小的字段(比如 0.003%的字段)，这可能意味着一个 bug 或数据损坏，要么是 null 百分比很大的字段，这可能意味着我们可能会删除一个未使用的特性。

为了能够使用这种方法，您需要一个足够大的数据集，即使这样，它也可能不能代表未来的数据。对我们来说，五年多的数据和几百万行数据比挖掘代码、询问专家或做出假设给了我们更多的信心。

也许你不喜欢它告诉你的东西，但是生产数据从来不会说谎。