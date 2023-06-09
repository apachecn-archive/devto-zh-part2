# 点击使用 JavaScript 在 WhatsApp 上发送

> 原文：<https://dev.to/mandaputtra/click-to-send-on-whatsapp-with-javascript-2anm>

> 重要，请编码组件()
> 
> TL；博士:你可以用下面的链接，把它放在你的按钮上，或者别的什么地方。它只需在用户手机上打开 whatsapp。但它不会自动发送。
> 
> [https://api.whatsapp.com/send?phone=+*YOURNUMBER*&text = % 20 *你的消息*](https://api.whatsapp.com/send?phone=+*YOURNUMBER*&text=%20*YOURMESSAGE*)

我们在这里建立的是一个动态发送到 WhatsApp 的 JavaScript 链接方法，你可以使用上面的链接并尝试一下

好的，我将使用 JSBin，首先打开，然后很容易你将使用 JavaScript 上的`split()`和`join()`字符串属性，这里是我的脚本。

```
// https://api.whatsapp.com/send?phone=+{{ *YOURNUMBER* }}&text=%20{{ *YOUR MESSAGE* }}

var yourNumber = "{{ your number in string}}"
var yourMessage = "{{ your message in string }}"

// %20 mean space in link
// If you already had an array then you just join them with '%20'
// easy right

function getLinkWhastapp(number, message) {
  number = yourNumber
  message = yourMessage.split('  ').join('%20')

  return console.log('https://api.whatsapp.com/send?phone=' + number + '&text=%20' + message)
}

getLinkWhastapp() 
```

Enter fullscreen mode Exit fullscreen mode

在 jsbin 上复制，给出你想要的电话号码和消息，确保你使用 WhatsApp，它会在你的手机上打开 WhatsApp，显示你写的消息。

延伸阅读:

*   [split()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split)
*   [join()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join)

* * *

## 重要

写这篇文章是为了学习 JS `split`和`join`用于生产目的，你应该使用`encodeURIComponent()`T3】

```
function getLinkWhastapp(number, message) {
  var url = 'https://api.whatsapp.com/send?phone=' 
     + number 
     + '&text=' 
     + encodeURIComponent(message)

  return url
} 
```

Enter fullscreen mode Exit fullscreen mode