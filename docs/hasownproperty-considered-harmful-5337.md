# 拥有被认为有害的财产

> 原文：<https://dev.to/dexygen/hasownproperty-considered-harmful-5337>

许多 Javascript 开发人员在用 for 循环遍历一个对象时，会自动到达`hasOwnProperty`...在；最近我在 reddit 上读到的一个例子:

```
 for (var k in this.generalKeyHandling) {
        if (this.generalKeyHandling.hasOwnProperty(k) && this.down.indexOf(k) > -1) {
        // etc. 
```

Enter fullscreen mode Exit fullscreen mode

然而，该对象刚刚被定义在一个直接在前的文字中，因此它不会有任何继承的属性，这就是使用`hasOwnProperty`的要点。不仅在这种情况下使用它是不必要的，而且 JSPerf 似乎表明它可以慢 10 倍。

如果你定义的对象(文字)不知何故被劫持了，以至于当你想迭代它们的时候它们已经有了继承的属性，那么使用`hasOwnProperty`仅仅是屏蔽就有更大的问题了。所以，在考虑使用它的时候，引用《安迪·格里菲斯秀》中一集丢失的建议:“Bea 阿姨，我要告诉你一个烹饪技巧:不要！"