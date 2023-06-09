# Haskell 字符串字段指南

> 原文：<https://dev.to/chrismwendt/haskell-strings-field-guide-2j24>

下面是如何在 Haskell 中进行字节、字符串和字符类型之间的转换:

| 功能 | 类型 |
| --- | --- |
| encodeUtf8 | 文本->字节字符串 |
| 解除…的负担 | 文本->字符串 |
| 解码蛋白 1 | 字节串->文本 |
| 从宽处理 | 字节串->文本 |
| BC .解包 | 字节字符串->字符串 |
| UTF8.toString | 字节字符串->字符串 |
| b .打开包装 | 字节字符串-> [Word8] |
| UTF8.fromString | 字符串->字节字符串 |
| 包装 | 字符串->文本 |
| w2c | Word8 -> Char |
| 打包行李 | [Word8] ->字节字符串 |
| c2w ❌分部 | Char -> Word8 |
| BC.pack ❌部分 | 字符串->字节字符串 |
| 弦❌停止流 | 字符串-> BSC。字节字符串 |

注意 Char 是有限的:

```
toEnum 10000000 :: Char
*** Exception: Prelude.chr: bad argument: 10000000 
```

Enter fullscreen mode Exit fullscreen mode