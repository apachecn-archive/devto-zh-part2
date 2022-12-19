# 深度隐形寻址[1/3]

> 原文：<https://dev.to/marpme/stealth-addressing-in-depth-13-7jk>

[![](../Images/b66c3fc3598abea53eb96c7b2fe82fb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BjskBKhp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pv0s55gwtubqsnm1lxin.jpg)

首先，我们将从每天重复发生的正常程序开始。这是一个简单的用例，两个人想要私下交易，这是他们的权利。要做到这一点，我们需要一个秘密地址，使接收者能够从付款人那里接收资金。让我们称付款人为鲍勃。在今天的课文中，我们将讲述如何创建一个秘密地址，它是由什么组成的，它的用途是什么。

## 如何生成隐形地址？

[![SHA256 workflow](../Images/5db322ec5110ac43bae1c8911780afe8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O-u8vbOh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AKvKbvq-1AIzxYRukfvMw_g.png)

首先，你需要数学，大量的数学。我们基本上需要一些加密算法来完成整个过程，包括 **SHA256** 、 **SECP256k1** (椭圆曲线加密，方程 y = x + 7)和 **Base58 编码**。

我们的秘密地址的数据结构:

```
 // [version] [options] [scan_key] [N] ... [Nsigs] [prefix_length] 
```

Enter fullscreen mode Exit fullscreen mode

正常隐藏地址的总体结构如下。它基于当前隐藏地址的版本，该版本简单地指定了它可以用于哪种货币。如果版本改变，区块链会拒绝你的地址。该选项是一个简单的无符号整数，可以指定任何选项。你可以一次扫描公钥和多次花费公钥(其中 N =密钥数；NSigs =所有支出键)来发送资金。前缀长度默认为零。

Verge 目前版本的隐身地址是 0x28。

### 逐步创建基于边界的隐藏地址

1.  生成两个长度为 32 字节的私有随机数(又名。安全随机数)
2.  一个将被声明为花费秘密，另一个将被声明为扫描秘密
3.  每一个，scan 和 spend secret，将被用于通过利用 SECP256k1(长度应该是 33 字节)来生成公钥。确保不要溢出 SECP256k1 算法。
4.  现在按上述顺序将所有内容放入一个缓冲区。
5.  通过使用 SHA256 对当前地址缓冲区进行双重哈希运算，生成该缓冲区的哈希值，并将校验和的前 4 个字节附加到地址缓冲区。
6.  最后，base58 编码你的地址，如果你做的一切都正确，你的地址应该以 smY 开头。

## 用 JavaScript 隐身寻址

```
const { randomBytes } = require('crypto');
const secp256k1 = require('secp256k1');
const SxAddress = require('./StealthAddress');

// generates a private key from a secure/random source
generatePrivateKey = () => {
  // generate privKey
  let privKey;
  do {
    privKey = randomBytes(32);
    // check if the seed is within the secp256k1 range
  } while (!secp256k1.privateKeyVerify(privKey));

  return privKey;
};

// generate a public key based on the current seeds
generatePublicKey = privKey => {
  // get the public key in a compressed format
  return secp256k1.publicKeyCreate(privKey);
};

const scanPriv = generatePrivateKey();
const spendPriv = generatePrivateKey();
// pass all the data into the stealth address class
const address = new SxAddress(
  scanPriv,
  generatePublicKey(scanPriv),
  spendPriv,
  generatePublicKey(spendPriv)
);

console.log(address.toJson()); 
```

Enter fullscreen mode Exit fullscreen mode

完成此过程后，我们已经收集了创建隐藏地址所需的基本要求，并且收集了所需的所有输入。即具有两对代表扫描和花费密钥的密钥(公共和私有)。这些是验证交易和在新块中签名所需要的。我们稍后将深入讨论这一点。首先，我们将讲述隐形寻址的基础知识。

也就是说，让我们通过查看隐藏地址类来最终创建一个编码地址:

```
const stealth_version_byte = 0x28;
const crypto = require('crypto');
const bs58 = require('bs58');

module.exports = class StealthAddress {
  constructor(scanPriv, scanPub, spendPriv, spendPub) {
    this.scanPub = scanPub;
    this.spendPub = spendPub;
    this.scanPriv = scanPriv;
    this.spendPriv = spendPriv;
    this.options = 0;
  }

  encode() {
    const address = new Buffer.from([
      stealth_version_byte,
      this.options,
      ...this.scanPub,
      1, // size of public keys
      ...this.spendPub,
      0, // size of signatures
      0, // ??
    ]);

    const result = Buffer.concat([address, this.generateChecksum(address)]);
    return bs58.encode(result);
  }

  generateChecksum(data) {
    return crypto
      .createHash('sha256')
      .update(
        crypto
          .createHash('sha256')
          .update(data)
          .digest()
      )
      .digest()
      .slice(0, 4);
  }

  validateChecksum(modules) {
    const buffered = new Buffer.from(modules);
    const checksumModule = buffered.slice(
      buffered.byteLength - 4,
      buffered.byteLength
    );

    const informationModules = buffered.slice(0, buffered.byteLength - 4);
    const informationChecksum = this.generateChecksum(informationModules);
    return {
      valid: Buffer.compare(informationChecksum, checksumModule) === 0,
      checksum: informationChecksum.toString('hex'),
    };
  }

  isStealth(bs58address) {
    const modules = bs58.decode(bs58address);
    let checks = this.validateChecksum(modules);
    if (!checks.valid) {
      return {
        valid: false,
      };
    }

    if (modules.length < 1 + 1 + 33 + 1 + 33 + 1 + 1 + 4) {
      return {
        valid: false,
      };
    }

    checks = { ...checks, length: modules.length };

    if (modules[0] !== stealth_version_byte) {
      return {
        valid: false,
      };
    }

    checks = {
      ...checks,
      stealthVersion: `0x${modules[0].toString('16')}`,
    };

    return checks;
  }

  toJsonPrivate() {
    return JSON.stringify(
      {
        scanPub: this.scanPub.toString('hex'),
        spendPub: this.spendPub.toString('hex'),
        scanPriv: this.scanPriv.toString('hex'),
        spendPriv: this.spendPriv.toString('hex'),
        options: this.options,
        address: this.encode(),
        isStealth: this.isStealth(this.encode()),
      },
      null,
      2
    );
  }

  toJson() {
    return JSON.stringify(
      {
        scanPub: this.scanPub.toString('hex'),
        spendPub: this.spendPub.toString('hex'),
        scanPriv: 'hidden',
        spendPriv: 'hidden',
        options: this.options,
        address: this.encode(),
        isStealth: this.isStealth(this.encode()),
      },
      null,
      2
    );
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

好了，这对于通读来说是一大块内容，请关注 encode()方法的路径，它将所有重要的缓冲区合并到一个简单的地址缓冲区中。

最后，我们将看到一个基于地址缓冲区的校验和。然后，这将与地址缓冲区合并，并提供验证地址是否被手动修改或由于网络故障等而被破坏可能性。

…未完待续: )

以上解释的所有细节都是基于这个隐形寻址工作流程的最初想法:
//版权所有 2014 The shadow coin developers
//根据 MIT/X11 软件许可证分发，见随附