# 用克里斯特解释恒星

> 原文：<https://dev.to/jillesvangurp/explaining-stellar-using-cliste-3men>

# 气候的起源

几个月前，我们决定在 [Inbot](//inbot.io) 的 Stellar 上创建一个令牌。因此，我开始阅读、探索 SDK，并开始试图弄清楚恒星世界中的一切是如何运作的。

因为这些天我主要使用 Kotlin，所以我决定改编官方的 stellar sdk for Java，让它对 kotlin 更友好一些。Kotlin 和 Java 配合得非常好，所以这基本上可以归结为我创建了一个名为[in bot-stellar-kot Lin-wrapper](https://github.com/Inbot/inbot-stellar-kotlin-wrapper)的新 Github 项目。

最初，我只是四处闲逛，试图弄清楚 Horizon API 的不同部分。我想出了如何运行一个独立的链，写了一些代码，添加了一些测试，等等。几天后我意识到

1.  这东西很简单
2.  我真的希望使用命令行，而不是一直写代码和测试。

因此，我在 googled 上搜索适合 Kotlin 的命令行参数解析器，无意中发现了 com . xenomachina . kot Lin-arg parser。对于分析命令行来说，这是一个非常好的 kotlin dsl。

接下来的步骤基本上是，想出一个好听的名字:STellar 的命令行界面，又名。克里斯特。在这一点上，我一直在这个项目上断断续续地工作了大约 2 个月。它已经到了非常有用的地步。

# 用克里斯特解释恒星

现在我有了 cliste，我可以展示一些简单的例子来解释 stellar 是如何工作的。我不会给你常见的营销陈词滥调、糟糕的隐喻和其他冗长的交流方式，而是使用 cliste 突出一些关键特性:

## 运行着独立的恒星

如果你想鬼混一下，启动一个独立的链是最好的方法。

```
docker run --rm --name stellarstandalone -p "8000:8000"  stellar/quickstart --standalone 
```

Enter fullscreen mode Exit fullscreen mode

启动需要一秒钟。因为我们通过了`--rf`如果你杀死了容器，你所有的数据都会丢失。所以，你可以用这个作为沙箱。您可以将- testnet 或- public 换成- standalone，以 testnet 或 public stellar net 为目标。

# 创建账户

让我们创建一个帐户，给自己一些 XLM。在 stellar，账户必须有 0.5 XLM 的最低余额。在快速入门图像中的独立链上，它仍然是 20 XLM。所以为了安全起见。我们给了爱丽丝很多。

因为我们在一个独立的网络上，我们将不得不从头创建一个帐户。其工作原理如下。

```
$ ./cliste createAccount alice 1000
17:35:56.715 [main] INFO io.inbot.kotlinstellar.KotlinStellarWrapper - using standalone network
17:35:58.166 [main] INFO org.stellar.sdk.KotlinExtensions - 7 07067baf191a516c6434c84c6232c3672dd179883451641b417fb3090bc82d08 success:true fee:100 CREATE_ACCOUNT
17:35:58.166 [main] INFO io.inbot.kotlinstellar.KotlinStellarWrapper - created GBJR3JH4ZC5LZKGM2PPSVMJFPPWREVEKD4TQJL2RMUHI75P35N4JVABC
created account with secret key SCBB6IXATC2RFKCFIFUUIXGD5QDHRYAEQZBDDDMPRO3PVYXEMETCC67F 
```

Enter fullscreen mode Exit fullscreen mode

这里刚刚发生了什么:

*   我们生成了一个新的密钥对
*   它存储在 alice 键下的 keys.properties 中。这允许我们在将来的命令中使用它作为别名。
*   我们给了自己一些 XLM。我们可以这样做，因为我们知道链条的种子。
*   它记录了一些细节，比如私钥

这在 testnet 上不起作用，但是你可以使用 [friendbot](https://www.stellar.org/laboratory/#account-creator?network=test) 来代替。在公共网络上，为新账户提供资金的唯一方式是通过交易所或现有账户。

## 爱丽丝现在可以创建更多账户了

现在，alice 有了一个有效帐户，她可以为 bob 创建一个帐户，并为 bob 自己的基本 XLM 余额提供资金。

```
$ ./cliste -a alice createAccount bob 50
17:40:07.888 [main] INFO org.stellar.sdk.KotlinExtensions - 57 e580f9e898a7151000e0228500d17846165aebcfed405411167cf5b1f41dd6b4 success:true fee:100 CREATE_ACCOUNT
17:40:07.892 [main] INFO io.inbot.kotlinstellar.KotlinStellarWrapper - created GAURP3FQ56BOF2PXF5DCLFS4QOJPTO5XM62G2AAMAQXB4PZWYNSS4RKA
created account with secret key SCYD6JC5GHFW63T5KTNVOT5FFJIW2R67VBEHFYIQZCMS2VLLBAKUI4YW 
```

Enter fullscreen mode Exit fullscreen mode

所以我们简单地用-a 将 alice 的密钥添加到命令中，并定义一个新的密钥。

## 管理账户

如果你想知道你有什么钥匙，你可以列出如下:

```
$ ./cliste listKeys
Defined keys (2):
alice: secretKey SCBB6I.... accountId: GBJR3JH4ZC5LZKGM2PPSVMJFPPWREVEKD4TQJL2RMUHI75P35N4JVABC
bob: secretKey SCYD6J.... accountId: GAURP3FQ56BOF2PXF5DCLFS4QOJPTO5XM62G2AAMAQXB4PZWYNSS4RKA 
```

Enter fullscreen mode Exit fullscreen mode

这里缩写了密钥。

注意，您也可以在 keys.properties 中手动添加密钥。您也可以在此混合使用公钥和私钥。所以，你可以像使用公共密钥的地址簿一样使用它。如果你要为账户进行交易，你只需要私人密钥。

**小心在这里存放你关心的私钥**。该工具仅用作开发工具。我可能会在未来添加一些更合适的保护，但现在都是纯文本。简而言之，不要做任何我也不会做的事情。

# 收支平衡

让我们给鲍勃更多的 XLM:

```
$ ./cliste -a alice pay bob 10 XLM
17:44:12.535 [main] INFO org.stellar.sdk.KotlinExtensions - 106 703c34d98162f5223805cdd54a48261ae73e0dae8d5fccc76caba59d492f228a success:true fee:100 PAYMENT 
```

Enter fullscreen mode Exit fullscreen mode

鲍勃和爱丽丝可以各自检查他们的余额:

```
$ ./cliste -a alice balance
accountId: GBJR3JH4ZC5LZKGM2PPSVMJFPPWREVEKD4TQJL2RMUHI75P35N4JVABC subEntryCount: 0 home domain: null

thresholds: 0 0 0
signers:
    GBJR3JH4ZC5LZKGM2PPSVMJFPPWREVEKD4TQJL2RMUHI75P35N4JVABC 1
authRequired: false
authRevocable: false

Balances:
XLM b:939.9999800 l:- - sl: - - bl: -

$ ./cliste -a bob balance
accountId: GAURP3FQ56BOF2PXF5DCLFS4QOJPTO5XM62G2AAMAQXB4PZWYNSS4RKA subEntryCount: 0 home domain: null

thresholds: 0 0 0
signers:
    GAURP3FQ56BOF2PXF5DCLFS4QOJPTO5XM62G2AAMAQXB4PZWYNSS4RKA 1
authRequired: false
authRevocable: false

Balances:
XLM b:60.0000000 l:- - sl: - - bl: - 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一点元数据和一个余额列表，当前只包括 XLM。

那么，鲍勃，现在正好有 60 个 XLM。爱丽丝已经支付了 50 英镑和 10 英镑 XLM 以及这两笔交易的一些费用:2x 100 英镑。斯特鲁普是 XLM 的十万分之一。这意味着你只用 1 XLM 就可以为 10 万笔交易提供资金，在撰写本文时，这大约是 20 欧分。

# 发放自己的令牌

要发行新令牌，我们需要一个发行帐户。让我们创建它以及一个分发帐户，我们将使用它来分发我们的 FOO 令牌。爱丽丝也要为这些账户买单。

```
$ ./cliste -a alice createAccount issuing 100
$ ./cliste -a alice createAccount distribution 100 
```

Enter fullscreen mode Exit fullscreen mode

# 令牌管理

现在让我们定义我们的令牌:

```
$ ./cliste defineAsset issuing FOO

$ ./cliste listAssets
Defined assets (1):
FOO     GBSR46WQNCDK7SPUW3XR3C663AE3QDG3MGG5S66GI6XQINQYGAZH7CF5 
```

Enter fullscreen mode Exit fullscreen mode

这仅仅是在一个名为 assets.properties 的文件中添加了另一个别名。

我们将从我们的发行账户向我们的分销账户发行 FOO。

# 信任额度

要做到这一点，需要有一条信任线。在 stellar，你只能持有你信任的代币。因此，为了让我们的分发帐户能够保存 FOO，它需要信任 FOO

```
$ ./cliste -a distribution trust FOO
17:58:07.565 [main] INFO org.stellar.sdk.KotlinExtensions - 273 f5ad81be61734ebfaef89233e5203a9f24cad1538312b17607b047da9de15a62 success:true fee:100 CHANGE_TRUST 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在检查分销账户的余额，我们将在余额下看到一个新条目:

```
$ ./cliste -a distribution balance
accountId: GADRJ4IDLVMOFE5DNQF7UZGWAUABKO7MYM2FAKGEGVPZA2W4RFQFF6ZQ subEntryCount: 1 home domain: null

thresholds: 0 0 0
signers:
    GADRJ4IDLVMOFE5DNQF7UZGWAUABKO7MYM2FAKGEGVPZA2W4RFQFF6ZQ 1
authRequired: false
authRevocable: false

Balances:
FOO (GBSR46WQNCDK7SPUW3XR3C663AE3QDG3MGG5S66GI6XQINQYGAZH7CF5) b:0.0000000 l:922337203685.4775807 - sl: - - bl: -
XLM b:99.9999900 l:- - sl: - - bl: - 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们的分销账户在很大程度上“信任”FOO。一英尺的最大限度或大约 92233720 英尺。53637.66363636367 这是你能放入 stellar 的 64 位平衡的最大值。如果你愿意，你可以把你的信任限制在更小的范围内。

## 魔法发生了...

现在让我们变个魔术，让我们的分销账户所有者成为亿万富翁

```
$ ./cliste -a issuing pay distribution 10000000000 FOO
18:01:57.355 [main] INFO org.stellar.sdk.KotlinExtensions - 319 11d1f4533656ee7b7e009f6db8ad9c61159bd4012790ffc46eff7ed03000a85d success:true fee:100 PAYMENT 
```

Enter fullscreen mode Exit fullscreen mode

简单地从发行帐户支付一些 FOO 实际上导致 FOO 硬币的出现。如果我们检查余额，我们会看到从发行到分发的简单支付创建了令牌:

```
$ ./cliste -a distribution balance
accountId: GADRJ4IDLVMOFE5DNQF7UZGWAUABKO7MYM2FAKGEGVPZA2W4RFQFF6ZQ subEntryCount: 1 home domain: null

thresholds: 0 0 0
signers:
    GADRJ4IDLVMOFE5DNQF7UZGWAUABKO7MYM2FAKGEGVPZA2W4RFQFF6ZQ 1
authRequired: false
authRevocable: false

Balances:
FOO (GBSR46WQNCDK7SPUW3XR3C663AE3QDG3MGG5S66GI6XQINQYGAZH7CF5) b:10000000000.0000000 l:922337203685.4775807 - sl: - - bl: -
XLM b:99.9999900 l:- - sl: - - bl: - 
```

Enter fullscreen mode Exit fullscreen mode

# 多重签名

当然，在实践中，你可能想锁定一些东西。为此，我们可以修改帐户选项。一个好的做法是用多重签名来保护重要的帐户。

## 向发卡账户添加签名人

让我们将 alice 和 bob 添加为发行帐户的签署人。

```
$ ./cliste -a issuing setOptions --signer-key alice --signer-weight 5
18:07:07.987 [main] INFO org.stellar.sdk.KotlinExtensions - 381 78d4572ef51900f088babf87a4e878771391950a24ca6f710a1deacde0967ad7 success:true fee:100 SET_OPTIONS

$ ./cliste -a issuing setOptions --signer-key bob --signer-weight 5
18:07:17.933 [main] INFO org.stellar.sdk.KotlinExtensions - 383 5c7b0ee0747c57fbe456781888b322ef7d93270af9db8daf010c263ad64b5c38 success:true fee:100 SET_OPTIONS 
```

Enter fullscreen mode Exit fullscreen mode

这将 alice 和 bob 添加为签名人。他们的键的权重为 5。如果你看看上面的余额，你会看到它默认为 0。所以爱丽丝或鲍勃有足够的重量做所有的事情。

## 管理阈值和权重

锁定发卡行账户:

```
$ ./cliste -a issuing setOptions --low-threshold 8 --medium-threshold 8 --high-threshold 8 --master-key-weight 0
18:16:02.379 [main] INFO org.stellar.sdk.KotlinExtensions - 488 f1a347698c2b8ee6d9c891d6da7ae1c86fc367785b4f0dc1e71c54d78a7726e9 success:true fee:100 SET_OPTIONS

$ ./cliste -a issuing balance
accountId: GBSR46WQNCDK7SPUW3XR3C663AE3QDG3MGG5S66GI6XQINQYGAZH7CF5 subEntryCount: 2 home domain: null

thresholds: 8 8 8
signers:
    GBJR3JH4ZC5LZKGM2PPSVMJFPPWREVEKD4TQJL2RMUHI75P35N4JVABC 5
    GAURP3FQ56BOF2PXF5DCLFS4QOJPTO5XM62G2AAMAQXB4PZWYNSS4RKA 5
    GBSR46WQNCDK7SPUW3XR3C663AE3QDG3MGG5S66GI6XQINQYGAZH7CF5 0
authRequired: false
authRevocable: false

Balances:
XLM b:99.9999500 l:- - sl: - - bl: - 
```

Enter fullscreen mode Exit fullscreen mode

因此，这证实了我们的发行账户现在有 3 把钥匙。我们将主密钥设置为 0；所以不能再用来发出 FOO:

```
$ ./cliste -a issuing pay distribution 10000000000 FOO
java.lang.IllegalStateException: failure after 0 transaction failed tx_bad_auth - null
    at org.stellar.sdk.KotlinExtensionsKt.doTransactionInternal(KotlinExtensions.kt:180)
    at org.stellar.sdk.KotlinExtensionsKt.doTransaction(KotlinExtensions.kt:142)
    at io.inbot.kotlinstellar.KotlinStellarWrapper.pay(KotlinStellarWrapper.kt:322)
    at io.inbot.kotlinstellar.KotlinStellarWrapper.pay$default(KotlinStellarWrapper.kt:314)
    at io.inbot.kotlinstellar.cli.CommandsKt$doPay$1.invoke(Commands.kt:194)
    at io.inbot.kotlinstellar.cli.CommandsKt$doPay$1.invoke(Commands.kt)
    at io.inbot.kotlinstellar.cli.CommandContext.run(CommandContext.kt:54)
    at io.inbot.kotlinstellar.cli.CliSteMainKt.main(CliSteMain.kt:88)
com.xenomachina.argparser.SystemExitException: Problem running 'pay'. failure after 0 transaction failed tx_bad_auth - null
    at io.inbot.kotlinstellar.cli.CommandContext.run(CommandContext.kt:62)
    at io.inbot.kotlinstellar.cli.CliSteMainKt.main(CliSteMain.kt:88)
cliste: Problem running 'pay'. failure after 0 transaction failed tx_bad_auth - null 
```

Enter fullscreen mode Exit fullscreen mode

## 签约交易

为了完成这项工作，Alice 和 Bob 都需要在交易上签名。因此，让我们创建一个未签名的事务:

```
$ ./cliste -a issuing preparePaymentTX distribution 10000000000 FOO
Transaction envelope xdr:
tx hash: zf+uG/7ePiTLuoqLbqgMMyQDq+PlxEsJkVEKq/jEixs=
tx envelope xdr: AAAAAGUeetBohq/J9LbvHYve2Am4DNthjdl7xkevBDYYMDJ/AAAAZAAAAMYAAAAGAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAABxTxA11Y4pOjbAv6ZNYFABU77MM0UCjENV+QatyJYFIAAAABRk9PAAAAAABlHnrQaIavyfS27x2L3tgJuAzbYY3Ze8ZHrwQ2GDAyfwFjRXhdigAAAAAAAAAAAAA= 
```

Enter fullscreen mode Exit fullscreen mode

这创建了一个事务，但是它不是提交给 stellar，而是以一种叫做 XDR 的格式输出序列化的二进制表示。这其实就是 stellar 内部储存的东西。

## 添加签名

Stellar 要求此交易有两个签名，因为我们已在之前的步骤中对此进行了配置。所以 alice 和 bob 都必须在我们提交交易之前签名。

首先，爱丽丝在交易上签名:

```
$ ./cliste -a alice signTx AAAAAGUeetBohq/J9LbvHYve2Am4DNthjdl7xkevBDYYMDJ/AAAAZAAAAMYAAAAGAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAABxTxA11Y4pOjbAv6ZNYFABU77MM0UCjENV+QatyJYFIAAAABRk9PAAAAAABlHnrQaIavyfS27x2L3tgJuAzbYY3Ze8ZHrwQ2GDAyfwFjRXhdigAAAAAAAAAAAAA=
tx hash: zf+uG/7ePiTLuoqLbqgMMyQDq+PlxEsJkVEKq/jEixs=
tx envelope xdr: AAAAAGUeetBohq/J9LbvHYve2Am4DNthjdl7xkevBDYYMDJ/AAAAZAAAAMYAAAAGAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAABxTxA11Y4pOjbAv6ZNYFABU77MM0UCjENV+QatyJYFIAAAABRk9PAAAAAABlHnrQaIavyfS27x2L3tgJuAzbYY3Ze8ZHrwQ2GDAyfwFjRXhdigAAAAAAAAAAAAH763iaAAAAQKZ+gYmDIqv9hUdZdC9+C4bUuX4RWmT8BnCI9wnb35IZ7IZIg5U8NIMvtodGEr4uv3NNB5/tbABEaNtDygihcws= 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们得到的是不同的 XDR。它现在包括了爱丽丝的签名。一个签名是不够的。因此，Alice 现在可以通过电子邮件/slack/等将她签名的 XDR 发送给 Bob。谁也可以签名:

```
./cliste -a bob signTx AAAAAGUeetBohq/J9LbvHYve2Am4DNthjdl7xkevBDYYMDJ/AAAAZAAAAMYAAAAGAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAABxTxA11Y4pOjbAv6ZNYFABU77MM0UCjENV+QatyJYFIAAAABRk9PAAAAAABlHnrQaIavyfS27x2L3tgJuAzbYY3Ze8ZHrwQ2GDAyfwFjRXhdigAAAAAAAAAAAAH763iaAAAAQKZ+gYmDIqv9hUdZdC9+C4bUuX4RWmT8BnCI9wnb35IZ7IZIg5U8NIMvtodGEr4uv3NNB5/tbABEaNtDygihcws=
tx hash: zf+uG/7ePiTLuoqLbqgMMyQDq+PlxEsJkVEKq/jEixs=
tx envelope xdr: AAAAAGUeetBohq/J9LbvHYve2Am4DNthjdl7xkevBDYYMDJ/AAAAZAAAAMYAAAAGAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAABxTxA11Y4pOjbAv6ZNYFABU77MM0UCjENV+QatyJYFIAAAABRk9PAAAAAABlHnrQaIavyfS27x2L3tgJuAzbYY3Ze8ZHrwQ2GDAyfwFjRXhdigAAAAAAAAAAAAL763iaAAAAQKZ+gYmDIqv9hUdZdC9+C4bUuX4RWmT8BnCI9wnb35IZ7IZIg5U8NIMvtodGEr4uv3NNB5/tbABEaNtDygihcws2w2UuAAAAQOboMFKz6sOnFPio17cuaOBLrHYN7k/DpFSGAaYVgYKg25YCMqZug2brTkh7LXaubChpFBYJHkF4vN/tUQNSCwM= 
```

Enter fullscreen mode Exit fullscreen mode

鲍勃得到了一个更大的 XDR。

## 考察 XDR

在提交之前，最好先检查一下里面有什么:

```
$ ./cliste txInfo AAAAAGUeetBohq/J9LbvHYve2Am4DNthjdl7xkevBDYYMDJ/AAAAZAAAAMYAAAAGAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAABxTxA11Y4pOjbAv6ZNYFABU77MM0UCjENV+QatyJYFIAAAABRk9PAAAAAABlHnrQaIavyfS27x2L3tgJuAzbYY3Ze8ZHrwQ2GDAyfwFjRXhdigAAAAAAAAAAAAL763iaAAAAQKZ+gYmDIqv9hUdZdC9+C4bUuX4RWmT8BnCI9wnb35IZ7IZIg5U8NIMvtodGEr4uv3NNB5/tbABEaNtDygihcws2w2UuAAAAQOboMFKz6sOnFPio17cuaOBLrHYN7k/DpFSGAaYVgYKg25YCMqZug2brTkh7LXaubChpFBYJHkF4vN/tUQNSCwM=
850403524614 operations:
source account: GBSR46WQNCDK7SPUW3XR3C663AE3QDG3MGG5S66GI6XQINQYGAZH7CF5
10000000000.0000000 FOO to GADRJ4IDLVMOFE5DNQF7UZGWAUABKO7MYM2FAKGEGVPZA2W4RFQFF6ZQ
Signatures:
pn6BiYMiq/2FR1l0L34LhtS5fhFaZPwGcIj3CdvfkhnshkiDlTw0gy+2h0YSvi6/c00Hn+1sAERo20PKCKFzCw==
5ugwUrPqw6cU+KjXty5o4Eusdg3uT8OkVIYBphWBgqDblgIypm6DZutOSHstdq5sKGkUFgkeQXi83+1RA1ILAw== 
```

Enter fullscreen mode Exit fullscreen mode

## 提交签约交易

现在让我们提交交易:

```
$ ./cliste submitTx AAAAAGUeetBohq/J9LbvHYve2Am4DNthjdl7xkevBDYYMDJ/AAAAZAAAAMYAAAAGAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAABxTxA11Y4pOjbAv6ZNYFABU77MM0UCjENV+QatyJYFIAAAABRk9PAAAAAABlHnrQaIavyfS27x2L3tgJuAzbYY3Ze8ZHrwQ2GDAyfwFjRXhdigAAAAAAAAAAAAL763iaAAAAQKZ+gYmDIqv9hUdZdC9+C4bUuX4RWmT8BnCI9wnb35IZ7IZIg5U8NIMvtodGEr4uv3NNB5/tbABEaNtDygihcws2w2UuAAAAQOboMFKz6sOnFPio17cuaOBLrHYN7k/DpFSGAaYVgYKg25YCMqZug2brTkh7LXaubChpFBYJHkF4vN/tUQNSCwM=
OK 
```

Enter fullscreen mode Exit fullscreen mode

并再次检查我们的分销账户:

```
$ ./cliste -a distribution balance
accountId: GADRJ4IDLVMOFE5DNQF7UZGWAUABKO7MYM2FAKGEGVPZA2W4RFQFF6ZQ subEntryCount: 1 home domain: null

thresholds: 0 0 0
signers:
    GADRJ4IDLVMOFE5DNQF7UZGWAUABKO7MYM2FAKGEGVPZA2W4RFQFF6ZQ 1
authRequired: false
authRevocable: false

Balances:
FOO (GBSR46WQNCDK7SPUW3XR3C663AE3QDG3MGG5S66GI6XQINQYGAZH7CF5) b:20000000000.0000000 l:922337203685.4775807 - sl: - - bl: -
XLM b:99.9999900 l:- - sl: - - bl: 
```

Enter fullscreen mode Exit fullscreen mode

# 爱丽丝和鲍勃现在可以开始使用 FOO 了

```
$ ./cliste -a alice trust FOO
$ ./cliste -a bob trust FOO
$ ./cliste -a distribution pay alice 1000000 FOO
$ ./cliste -a alice pay bob 100000 FOO 
```

Enter fullscreen mode Exit fullscreen mode

# 其他话题

你可以用 cliste 和 stellar 做更多的事情，但这对于一个不那么温和的介绍来说应该足够了。

你可能会喜欢使用 cliste 或玩它来探索恒星。你也可以通过 Stellar Laboratory UI 做任何事情。但是你会发现，大量的点击和浏览肯定会使事情变得更有效率。此外，恒星实验室实际上并不反对独立的链。克里斯特在这方面很棒。

## 灌篮

遵循 Github 上的说明。目前这是一个科特林图书馆项目。我可能会在某个时候将 cliste 转移到它自己的存储库中。

## 使用 cliste 与公共或测试网

以上所有命令都是针对独立网络运行的。

实际上，您可以在公共网络或测试网络上运行，也可以使用一些命令行选项。使用附带的脚本比手动设置这些选项更容易。这就是 clistePublic 脚本的工作方式。请注意，我们对资产和帐户使用了不同的文件名，因此我们可以轻松地进行切换。

```
$ cat clistePublic
#! /bin/bash
export CLISTE_ARGS='--stellar-network public --horizon-url https://horizon.stellar.org/ --key-properties public-keys.properties --asset-properties=public-assets.properties'

./cliste $* 
```

Enter fullscreen mode Exit fullscreen mode

## 贡献于气候

我欢迎拉式请求、问题、反馈等。

**注。**你也可以在 github 库中找到这篇文章的副本，我可能会继续在那里添加更多的文档。