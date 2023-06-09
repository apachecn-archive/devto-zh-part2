# 挑战:从字符串中解析简单和复杂的类型

> 原文：<https://dev.to/joelnet/challenge-parse-simple-and-complex-types-from-a-string-1hff>

# 挑战

创建一个要解析的函数，将签名解析为一个类型数组。有两种类型，一种是简单的像`Apple`，另一种是复杂的像`(Banana -> Grape)`。可能有任意数量的箭头，甚至在复杂类型中。

```
"Apple" //=> ["Apple"]
"Apple -> Banana -> Grape" //=> ["Apple", "Banana", "Grape"]
"(Apple -> Banana) -> Grape" //=> ["(Apple -> Banana)", "Grape"]
"Apple -> (Banana -> Grape)" //=> ["Apple", "(Banana -> Grape)"]
"Apple -> (Banana -> Grape) -> Cherry" //=> ["Apple", "(Banana -> Grape)", "Cherry"] 
```

```
function parseSignature(signature) {
  /* insert code here */
}

parseSignature("Apple -> (Banana -> Grape) -> Cherry")
//=> ["Apple", "(Banana -> Grape)",  "Cherry"] 
```

有趣的提示:这有一个真实的用例，因为它是 Haskell 或 [Fantasy Land](https://github.com/fantasyland/fantasy-land#type-signature-notation) 规范使用的 hindley milner 签名模式的一部分。