# 使用 setTimeout 执行 Javascript 字符串

> 原文：<https://dev.to/sandeepkamboj12/execute-javascript-string-using-settimeout-jp5>

```
 window.sum=0;

window.ExecuteJavascriptString = function()
{

    var data = "var a=3;"+
       "var b=4;"+
       "sum=a+b;"+
       "ShowResult()";
    setTimeout(data, 1);
}
ExecuteJavascriptString();  

window.ShowResult = function(){
    console.log(sum);
    alert(sum);
} 
```