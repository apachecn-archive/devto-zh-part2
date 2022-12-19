# 这就是 HTTPS 的工作方式

> 原文：<https://dev.to/isaacdlyman/this-is-how-https-works-223d>

大多数人都熟悉网站 URL 开头的`http://`。有些人可能已经注意到，任何带有登录表单或敏感信息的网站都以`https://`开头。“S”代表“安全”，让它工作的算法既疯狂又奇妙。为了理解为什么 HTTPS 如此神奇，让我们打个比方。

假设你召集了三个有成就的程序员:Janice、Natalia 和 Mario。这三个人从未以任何方式见过面或交流过。你把他们放在一个房间里，坐成一排，娜塔莉亚在中间。

```
[ Janice | Natalia | Mario ] 
```

Enter fullscreen mode Exit fullscreen mode

然后你给他们一个挑战。事情是这样的:

1.  你要给贾尼斯一个秘密消息，她需要把这个消息带给马里奥。
2.  如果娜塔莉亚发现了秘密信息，她会得到一份奖励，但是贾尼斯和马里奥什么也得不到。
3.  如果马里奥在娜塔莉亚没有发现的情况下收到了秘密信息，马里奥和贾妮斯都将得到一份奖励，而娜塔莉亚什么也得不到。
4.  他们只能通过来回传递纸条来交流。娜塔莉亚可以读任何她想读的笔记。

这似乎是一个不可能的难题。如果娜塔莉亚正在阅读他们来回传递的所有笔记，贾妮斯和马里奥怎么能秘密交流呢？这是我们在网络上面临的同样问题。您的 web 浏览器总是与远程服务器通信，而它们可能以前从未通信过。通过互联网发送的任何信息都有可能被第三方截获和阅读，那么我们如何对这些信息保密，以避免您的密码和银行账号被盗呢？答案是 HTTPS。

花一分钟思考这个难题。你可能会想到，如果贾尼斯和马里奥能够秘密地交换一点点信息(一个数字)，他们就可以用这些信息来编码其余的信息。他们可以使用数字替代密码(A = 1，B = 2，C = 3，等等)，然后将每个数字和秘密数字一起插入一个等式中，以便更难猜测哪个数字等于哪个字母。娜塔莉亚可能知道这个方程式，但是如果她不知道这个秘密数字，她就无法逆转这个方程式并解码这条信息。

这个想法被称为[对称密钥算法](https://en.wikipedia.org/wiki/Symmetric-key_algorithm)，我们一直在使用它！这是编码和解码秘密信息的好方法。

剩下的就是想办法交换密码了。贾尼斯知道一些关于质数的很酷的事情，并且知道如何去做。

首先，贾尼斯挑选两个质数，称为 *p* 和 *g* 。她把它们送给马里奥；娜塔莉亚看到他们没关系。

```
var p = 23;
var g = 5; 
```

Enter fullscreen mode Exit fullscreen mode

然后珍妮丝选了一个秘密号码，任何她想要的号码。假设这个数字是 3。

```
var janiceSecret = 3; 
```

Enter fullscreen mode Exit fullscreen mode

她创建了以下函数:

```
function computeExchange(g, secret, p) {
  var exchange = Math.pow(g, secret) % p;
  return exchange;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以她会将`g`提升到`secret`的幂，除以`p`，然后返回该除法的余数。

```
computeExchange(g, janiceSecret, p);
> 10 
```

Enter fullscreen mode Exit fullscreen mode

答案是 10。贾尼斯将这个号码发送给马里奥。

```
var numberFromJanice = 10; 
```

Enter fullscreen mode Exit fullscreen mode

然后马里奥选择一个秘密数字，比如 8，并运行相同的功能。

```
var marioSecret = 8;
computeExchange(g, marioSecret, p);
> 16 
```

Enter fullscreen mode Exit fullscreen mode

马里奥得到 16，并将这个数字发送给贾尼斯。

```
var numberFromMario = 16; 
```

Enter fullscreen mode Exit fullscreen mode

只差一步了。Janice 再次运行该函数，但是她没有使用`g`，而是使用 Mario 刚刚发送给她的号码。

```
computeExchange(numberFromMario, janiceSecret, p);
> 2 
```

Enter fullscreen mode Exit fullscreen mode

Mario 做了同样的事情:他再次运行这个函数，但是用 Janice 发给他的号码代替了`g`。

```
computeExchange(numberFromJanice, marioSecret, p);
> 2 
```

Enter fullscreen mode Exit fullscreen mode

他们俩都得了 2 号！这是巧合吗？不，不是的。事实上，这是数学上的必然。不管他们选择什么质数作为`p`和`g`，也不管他们选择什么作为他们的秘密数字，他们最终都会得到同一个数字——不一定是 2。在这种情况下，他们将使用 2 作为秘密数字来编码和解码他们的消息。这被称为[迪菲-赫尔曼密钥交换](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)。让我们挑选一些不同的数字再来一遍:

```
function computeExchange(g, secret, p) {
  var exchange = Math.pow(g, secret) % p;
  return exchange;
}

var p = 29;
var g = 7;
console.log('p and g are:', p, g)

var janiceSecret = 8;
var marioSecret = 5;

var numberFromJanice = computeExchange(g, janiceSecret, p);
console.log('Janice sends Mario', numberFromJanice);
var numberFromMario = computeExchange(g, marioSecret, p);
console.log('Mario sends Janice', numberFromMario);

var janiceKey = computeExchange(numberFromMario, janiceSecret, p);
var marioKey = computeExchange(numberFromJanice, marioSecret, p);

console.log('Their shared key should match:', janiceKey, marioKey); 
```

Enter fullscreen mode Exit fullscreen mode

(注意，这对稍微大一点的值`p`、`g`、`janiceSecret`和`marioSecret`不起作用，因为 JavaScript 不能很好地处理大数字。它在 Python 中工作得很好。)

`janiceKey`和`marioKey`的值都是 16。那个数字很特别。它从来没有在一张纸条上交换过。娜塔莉亚从未见过。娜塔莉亚不能逆转方程，因为她不知道贾尼斯或马里奥的秘密号码。她能做的最好的事情就是猜测他们的秘密号码，并试图根据这个号码来计算出编码/解码密钥。对于像我们使用的小数字，她可能可以做到这一点。但是如果 Janice 和 Mario 的秘密密钥是非常大的数字，比如 115 792 089 237 316 195 423 570 985 008 687 907 853 269 984 665 640 564 039 457 584 007 913 129 639 935(一个 256 位的数字)，那么这将变得非常困难。在挑战结束，你失去耐心，或者有人老死之前，Natalia 绝对不可能找到他们的共享密钥并解码信息。

每次你连接到使用 HTTPS 的服务器时，你的浏览器都会这么做。这叫做“TLS 握手”有人可能会嗅探互联网连接，从一开始就阅读您的浏览器发送和接收的每条消息，并且一旦确定了共享密钥，仍然无法解码您的消息。很疯狂，是吧？

少了一件。假设娜塔莉亚特别狡猾，想出了一个欺骗挑战的计划:当每条信息到来时，她会用她秘密创建的不同信息来替换它。这样，她可以分别与贾尼斯和马里奥握手，他们都不会知道。当一条信息来自马里奥时，她将使用她和他一起创建的共享密钥(但他*认为*是他和贾尼斯创建的)来解码，然后使用她和贾尼斯创建的共享密钥来编码(贾尼斯*认为*是她和马里奥创建的)。这是一种[中间人攻击](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)。

幸运的是，这个问题有一个解决方案。想象一下，贾尼斯和马里奥特别有洞察力，他们预测这可能会发生。他们在你开始挑战之前接近你，并指出这可能是作弊。为了防止这种情况，他们每个人都在一张纸条上签上自己的名字，并要求你把它贴在每个人都能看到的地方。这样，他们可以签署他们发送的信息，除非娜塔莉亚知道如何快速伪造签名，否则他们将通过比较每张纸条上的签名和公开可见的签名来知道这些信息是真实的。

计算机使用数字签名来签署信息，这是一个更复杂的数学魔术，我不会在这里讨论。但简而言之，一方提供公钥、消息和签名，另一方用数学证明消息与公钥匹配。在网络上，这个公钥通常由一个可信的来源提供，称为[认证机构](https://en.wikipedia.org/wiki/Certificate_authority)，并预先安装在您的操作系统或网络浏览器上。这样，您的电脑可以使用它已经拥有的信息来验证来自不确定来源的信息的真实性，并确保它与目标服务器建立了安全的通信。

所有这些都是为了防止人们知道你在看哪些猫的视频。我猜，还有你的信用卡号码。