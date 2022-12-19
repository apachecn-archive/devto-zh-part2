# 我学了足够多的网络加密技术

> 原文：<https://dev.to/subterrane/i-learned-enough-web-crypto-to-be-dangerous-5b5j>

*免责声明:*安全很难。保持您的私钥私密。我不是安全专家，有理由不这样做。我不确定它们都是什么，所以你要自担风险。

我被要求在将数据发送到服务器之前，在浏览器中加密一些数据。听起来很简单:我想我可以得到某人的公钥，用它加密一些数据，然后发送出去。他们会用自己的私钥解密，很容易。没有。

我很快了解到非对称密钥对(通常)用于加密对称密钥，而对称密钥用于加密数据。这是由于速度的原因，可以加密的数据量取决于密钥长度和 zzzzzz...抱歉，我睡着了。

所以，你自己编一把钥匙。这解释了为什么 Web Crypto 给了你这个便利的功能: [generateKey()](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/generateKey)

下面是一个加密一些数据的示例函数:

```
 // encrypt form input
    let cypher = await encrypt(input.value);
    console.dir('cyphertext: ' + cypher.data);

    async function encrypt(data) {
        const key = await window.crypto.subtle.generateKey({ name: 'AES-GCM', length: 256 }, true, ['encrypt', 'decrypt']);
        const iv = window.crypto.getRandomValues(new Uint8Array(12));
        const cypher = ab2str(await window.crypto.subtle.encrypt({ name: 'AES-GCM', iv: iv }, key, str2ab(data)));

        return {
            data: cypher,
            iv: iv,
            key: key
        };
    } 
```

Enter fullscreen mode Exit fullscreen mode

并且要解密:

```
 async function decrypt(data, key, iv) {
        return ab2str(await window.crypto.subtle.decrypt({ name: 'AES-GCM', iv: iv }, key, str2ab(data)))
    } 
```

Enter fullscreen mode Exit fullscreen mode

你学到的第二件事是网络加密功能在`BufferSource`上工作，而不是在`string`上。有 API 可以将字符串编码和解码到缓冲区( [TextEncoder](https://developer.mozilla.org/en-US/docs/Web/API/TextEncoder/encode) )，但我使用它们有些困难，所以我使用了几个 [Renato Mangini](https://developers.google.com/web/updates/2012/06/How-to-convert-ArrayBuffer-to-and-from-String) 的函数。

```
 function ab2str(buf) {
        return String.fromCharCode.apply(null, new Uint16Array(buf));
    }

    function str2ab(str) {
        let buf = new ArrayBuffer(str.length * 2);
        let bufView = new Uint16Array(buf);
        for (let i = 0, strLen = str.length; i < strLen; i++) {
            bufView[i] = str.charCodeAt(i);
        }
        return buf;
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一些加密数据、一个密钥和一个初始化向量(专业提示:在会议中说‘初始化向量’会让你听起来很聪明)。我们需要用别人的公钥加密我们制作的密钥。这样，我们可以将加密的数据、初始化向量和加密的对称密钥发送给他们，只有他们可以解密对称密钥来解密数据。

这就像你的房地产经纪人能够进入你想看的房子一样。房子有一把钥匙，钥匙放在前门的锁箱里。你的房地产经纪人知道锁箱的密码，所以她/他打开锁箱，拿到钥匙，打开房子，带你参观。你决定你真的更喜欢一个开放式厨房和一个主套房，所以你离开了，房地产经纪人把钥匙放在锁箱里。锁箱对于公钥/私钥对来说是一个可怕的类比，但你会认为打开房子的钥匙以某种方式得到了保护。

有趣的是，我们可以用命令行工具制作自己的密钥对。为了更有趣，我们可以将其转换为 JSON Web Key 格式，以便于处理。Web Crypto API 提供了允许您以 JWK 格式创建和导出密钥对的方法。我用上面的`generateKey`方法制作了一个对称密钥。但是我需要能够使用别人创建的公钥，所以我经历了这些步骤，看看我是否能让它工作。

我用的是 [dannycoates](https://github.com/dannycoates/pem-jwk) 的这个包。首先，制作一把钥匙:

```
openssl genrsa 2048 | pem-jwk > private_key.jwk 
```

Enter fullscreen mode Exit fullscreen mode

然后转换成。pem:

```
pem-jwk private_key.jwk > private_key.pem 
```

Enter fullscreen mode Exit fullscreen mode

从私钥中导出公钥:

```
openssl rsa -pubout -in private_key.pem -out public_key.pem 
```

Enter fullscreen mode Exit fullscreen mode

然后将公钥转换成 jwk 格式:

```
pem-jwk public_key.pem > public_key.jwk 
```

Enter fullscreen mode Exit fullscreen mode

您最终得到 4 个文件:

*   private _ key.jwk
*   private_key.pem
*   公共密钥. jwk
*   public_key.pem

我又写了几个函数

```
 async function importPublicKey() {
        const key = /* contents of public_key.jwk */ ;
        const algo = {
            name: 'RSA-OAEP',
            hash: { name: 'SHA-256' }
        };
        return await window.crypto.subtle.importKey('jwk', key, algo, false, ['wrapKey']);
    }

    async function importPrivateKey() {
        const key = /* contents of private_key.jwk */;
        const algo = {
            name: 'RSA-OAEP',
            hash: { name: 'SHA-256' }
        };
        return await window.crypto.subtle.importKey('jwk', key, algo, false, ['unwrapKey']);
    } 
```

Enter fullscreen mode Exit fullscreen mode

*免责声明:*还是那句话，保持你的私钥私密。这只是为了好玩，伙计，现实生活中不要这么做。

Web Crypto 给你加密和解密密钥的工具: [wrapKey](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/wrapKey) 和 [unwrapKey](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/unwrapKey) ，有了密钥，你可以[解密](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/decrypt)你的`BufferSource` :

```
 // import public key
        const publicKey = await importPublicKey();

        // wrap symmetric key
        const wrappedKey =  ab2str(await window.crypto.subtle.wrapKey('raw', cypher.key, publicKey, { name: 'RSA-OAEP' }));
        console.log('wrappedKey: ' + wrappedKey);

        // import private key
        const privateKey = await importPrivateKey();

        // unwrap symmetric key
        const unwrappedKey =  await window.crypto.subtle.unwrapKey('raw', str2ab(wrappedKey), privateKey, { name: 'RSA-OAEP' }, { name: 'AES-GCM', length: 256 }, false, ['decrypt']);
        console.log('unwrappedKey: ' + unwrappedKey);

        // decrypt encrypted data
        let plaintext = await decrypt(cypher.data, unwrappedKey, cypher.iv);
        console.log('plaintext: ' + plaintext); 
```

Enter fullscreen mode Exit fullscreen mode