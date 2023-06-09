# 如何序列化$%*#ing 缓冲区

> 原文：<https://dev.to/subterrane/how-to-serialize-that-ing-buffer-12m3>

一直在玩 [TextEncoder](https://developer.mozilla.org/en-US/docs/Web/API/TextEncoder/TextEncoder) 和 TextDecoder。 [Web Crypto API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) 中的方法与 [BufferSource](https://developer.mozilla.org/en-US/docs/Web/API/BufferSource) 一起工作，这很好，但我需要加密一些文本，存储它(实际上发送它，但它被存储)，然后解密。为此，我需要存储和读取缓冲区。对缓冲区进行编码和解码很简单，弄清楚之后序列化和反序列化也很简单。

首先，我们需要一个缓冲。

```
const buffer = new TextEncoder().encode('banana'); 
```

Enter fullscreen mode Exit fullscreen mode

没问题。但是现在我想把它放在 JSON 中(可能还有一堆其他属性),以后再读回来。

```
const testObj = {
    encodedText: buffer.toString()
};

const serializedTestObj = JSON.stringify(testObj); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们把它记录下来，看起来很简单:

```
{"encodedText":"98,97,110,97,110,97"} 
```

Enter fullscreen mode Exit fullscreen mode

获取对象很简单

```
const deserializedTestObj = JSON.parse(serializedTestObj); 
```

Enter fullscreen mode Exit fullscreen mode

但是我们如何将`encodedText`放回缓冲区呢？一旦你不再苦苦思索，这一部分看起来真的很简单。将逗号上的字符串拆分成一个数组，并使用 [TypedArray](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray/from) 的`from`方法得到一个`Uint8Array`。

```
const deserializedBuffer = deserializedTestObj.encodedText.split(',');
const newBuffer = Uint8Array.from(deserializedBuffer); 
```

Enter fullscreen mode Exit fullscreen mode

然后是一个简单的解码来得到我们的字符串

```
const str = new TextDecoder().decode(newBuffer);
console.log(str); // banana 
```

Enter fullscreen mode Exit fullscreen mode