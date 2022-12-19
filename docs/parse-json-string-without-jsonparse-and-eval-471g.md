# 不使用 JSON.parse 和 eval()解析 JSON 字符串

> 原文：<https://dev.to/sandeepkamboj12/parse-json-string-without-jsonparse-and-eval-471g>

```
 var jsonStr="{name:'abc',age:20}";
    var jsonObject = (new Function( "return " + jsonStr ) )() ;
    console.log(jsonObject); 
```

Enter fullscreen mode Exit fullscreen mode