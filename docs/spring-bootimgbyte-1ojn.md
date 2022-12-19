# 以 Byte 数组返回[Spring Boot]Img 标签中显示的图像

> 原文：<https://dev.to/kentama7/spring-bootimgbyte-1ojn>

*   我的锅
*   Spring Boot 2.0.4
*   百里香叶

* * *

要返回 Img 标签中显示的图像的字节数组，请使用 ResponseEntity。

Controllerの実装

```
@GetMapping("image")
fun image(): ResponseEntity<ByteArray> {
    val bytes = .. // 画像のByte 配列を取得
    return ResponseEntity.ok(bytes)
} 
```

HTML 的实现

```
<img th:src="@{/image}"> 
```