# 在 JS 中循环往复，帮帮我！

> 原文：<https://dev.to/brendalimon/cycles-while-and-for-in-js-help-me-3oim>

嗨！我正在学习如何在画布上画画，作为一个练习来学习 JS 中的循环，这个练习是我们必须使用 **while** 来创建一个循环并画出这个:

[![Alt text of image](img/93215178ff489e58bc40fce1f3bf690a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R4KjunlP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vzeeu3li0wh73tnwashm.png)

一切正常，我的代码是这样的:

```
**var d= document.getElementById("dibujito");**
**var lienzo= d.getContext("2d");**
**var lineas = 30;**
**var l = 0;**
**var yi, xf;**
**var colorcito = "pink";**

**while(l < lineas)**
**{**
 ** yi = 10 * l;**
  **xf = 10 * (l+1);**
  **dibujarLinea(colorcito, 0,yi,xf,300);**
  **console.log("Linea " + l)**
  **l = l + 1;**
**}**

**dibujarLinea(colorcito,299,1,299,299);**
**dibujarLinea(colorcito,1,1,299,1);**
**dibujarLinea(colorcito,1,1,1,299);**
**dibujarLinea(colorcito,1,299,299,299);**

**function dibujarLinea(color,xinicial,yinicial,xfinal,yfinal)**
**{**
  **lienzo.beginPath();**
  **lienzo.strokeStyle = color;**
  **lienzo.moveTo(xinicial,yinicial);**
  **lienzo.lineTo(xfinal,yfinal);**
  **lienzo.stroke();**
  **lienzo.closePath();**
**}** 
```

Enter fullscreen mode Exit fullscreen mode

但当我不得不像这样用**换**时，问题就来了

[![Alt text of image](img/5a70df3bb10e9408454fd7ca265190e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B9vjZTyO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ljnikv3f7od044u017uu.png)

我只是不明白如何使用**来表示**，我已经尝试过让代码工作，但我不能，你能给我解释一下如何让它工作吗？谢谢！

拥抱&哈士奇的爱！🐶👩🏻‍💻