# 如何获取比特币地址及其私钥？

> 原文：<https://dev.to/ishwar/how-to-get-bitcoin-address-privatekey-6bp>

我已经使用 bitcoinjs 库生成了比特币地址，代码如下

返回 bit coin . payments . p2pkh({ pubkey:node . public key，network })。地址

以上代码返回地址见下图
1 fytpnuzz 2 zjl 2 FZ 6 xehgtkhfujuhkqk3t

但是为了签署原始交易，我需要这个地址的 privatekey

我应该做什么来获得地址和它的私钥？

我现在如何从助记符中使用派生路径生成地址。
对于派生路径 m/44'/0/0/0 I 生成 1 fytpnuzz 2 zjl 2 FZ 6 xehgtkhfujuhkqk3t 地址。
现在我需要现在如何才能为这个派生路径 m/44'/0/0/0 获取 privatekey