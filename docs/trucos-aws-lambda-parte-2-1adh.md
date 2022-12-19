# Aws Lambda 技巧(第 2 部分)

> 原文：<https://dev.to/yorodm/trucos-aws-lambda-parte-2-1adh>

# 用 AWS Lambda (II)耍花样。

## 把戏 1。使用标记。

AWS 中的标签使我们能够:

1.  每个资源最多有 50 个合作伙伴。
2.  最多 128 个 Unicode 字符的密钥。
3.  哈斯塔 256 字符 Unicode。
4.  关键字和值区分大小写。

基本上我们需要做的就是藏起来:

```
lambda_cient = boto3.client('lambda')
def save_cache(tags): # tags is a dict of string:string
    lambda_client.tag_resource(
        Resource=self_arn, # get it from handler context
        Tags=json.dumps(tags)
    )

def get_cache():
    return lambda_client.list_tags(
        Resource=self_arn # get it from handler context
    )

def invalidate_cache(keys):
    return lambda_client.untag_resource(
        Resource=self_arn,
        Tags=json.dumps(keys)
    ) 
```

## 把戏 2。用其他语言发展你的舔嘴。

AWS Lambda 正式支持的语言是(撰写本文时):

1.  Javascript (Node.js)。
2.  Python (2 y 3)。
3.  Java。
4.  C#。
5.  走吧。

你喜欢的语言是什么？别担心，这份名单可以扩大到:

1.  Kotlin、Groovy、Ruby Clojure 以及 JVM 支持的所有内容。
2.  . NET 平台支持的任何语言，包括 [PHP](https://www.peachpie.io) 。
3.  拉斯特关于或[皇冠酒吧](https://github.com/ilianaw/rust-crowbar)，即使与[无服务器](https://github.com/softprops/serverless-rust)集成也是如此。
4.  [任何产生 wam 的东西](https://blog.scottlogic.com/2018/10/18/serverless-rust.html)

## 更多技巧

除了共享之外还有其他人吗？把你的评论留在这里或放在 dev。