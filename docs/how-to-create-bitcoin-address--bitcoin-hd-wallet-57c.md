# 创建比特币地址|比特币高清钱包

> 原文：<https://dev.to/ishwar/how-to-create-bitcoin-address--bitcoin-hd-wallet-57c>

/*
BIP39:用于生成确定性密钥的助记码，bip 39——用于管理您的恢复种子和恢复字。抽象。这个 BIP 描述了助记码或助记句(一组易于记忆的单词)的实现，用于确定性钱包的产生。它由两部分组成:生成助记符，并将其转换为二进制种子
*/
let bip 39 = require(" bip 39 ")；

/*
一个轻量级的钱包实现。目前，它支持密钥创建和各种格式之间的转换。
要使用 BIP32 高清钱包，首先要包含 hdkey 子模块，
为种子我建议使用 bip39。
*/
let HD key = require(' ether eumjs-wallet ')；

/*
bitcoinjs 是一个使用 javascript 开发比特币的成熟库和生态系统。
*/
让比特币= require(' bitcoinjs-lib ')；

```
let wallet_hdpath = "m/44'/0'/0'/0/0";
hdwallet =  bitcoin.HDNode.fromSeedBuffer(bip39.mnemonicToSeed(seed));
let wallet = hdwallet.derivePath(wallet_hdpath + index) 
```

Enter fullscreen mode Exit fullscreen mode

现在从钱包你可以得到私人密钥和地址