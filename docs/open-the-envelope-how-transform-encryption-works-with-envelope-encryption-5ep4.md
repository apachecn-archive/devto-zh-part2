# 打开信封:变换加密如何与信封加密一起工作。

> 原文：<https://dev.to/mkerndt/open-the-envelope-how-transform-encryption-works-with-envelope-encryption-5ep4>

# 打开信封

## 变换加密与信封加密的工作原理。

*这篇文章最初发表在 Medium 上，看一看[这里](https://blog.ironcorelabs.com/open-the-envelope-1c6e4feecf22)。尽情享受吧！*

[点击此处下载信封加密+转换加密信息图。](https://docs.ironcorelabs.cimg/envelope-encryption/infographic-envelope-encryption.jpg)

### 信封加密

信封加密是一种结合对称和非对称加密来提高性能的技术。

[![AES-Encrypts-Doc](img/c0ed1e065feaf789430cec38ec4859b4.png "Random AES Key Encrypts Document")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QKA7u4ND--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/79axwo38khljznczhzgz.jpg)

随机选择适用于对称加密算法(如 AES)的密钥值，然后用它来加密明文。这种明文称为文档，密钥称为文档加密密钥(DEK)。

[![EncryptDEK](img/ff1f2ffd8ea469aad218266b4f57d27e.png "User public key encrypts document encryption key")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Aa40580j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0uocl3sl76g1zmzr4d47.jpg)

然后用用户的公钥对 DEK 本身进行非对称加密。产生的加密 DEK 可以与数据一起存储或存储在其他地方。

[![DecryptDEK](img/b0a4f6e7fa8abde777fcba9e2d66c412.png "User private key decrypts the document encryption key")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W61NKekC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uisq63z5c1j4hlg5nvl7.jpg)

为了恢复文档，首先使用用户的私钥对加密的 DEK 进行解密。

[![DEKDecryptsDoc](img/81eeea21263cda0d2eceb9996f37a8bb.png "Document encryption key decrypts document")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9_6ONJhF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ptlrcvh47pug877kln4.jpg)

一旦恢复了 DEK，就可以用它来解密底层文档数据。

### 加密到组:转换加密

公钥加密的缺点是，在文档被加密之前，每个共享文档的用户都必须是已知的。如果数据对多个用户加密，则必须用每个用户的公钥分别加密。要撤销用户访问，我们必须拥有并更改底层数据(及其所有副本)。

为了解决这些缺点，在对多个用户进行加密时，请将转换加密与公钥加密结合起来。转换加密将 DEK 加密到一个称为组的任意实体，然后将加密的 DEK 从组转换到单个用户。

转换服务使用转换密钥执行“加密的 DEK —组”到“加密的 DEK —用户”的映射，DEK 在此过程中不解密，它是一个密文到密文的转换。

[![TransformKey](img/fbfd31da3e69d40ee9f116fc91caa6d7.png "Key transformed from group to user")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--39n-fzHZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m4eih1bal09uawhctixw.jpg)

在加密的 DEK 从组转换到用户后，用户的私钥可以用来解密加密的 DEK。然后，使用 DEK 对文档进行解密。

### 词汇术语:

#### 对称加密

对称加密使用一个密钥进行加密和解密。它又快又简单。主要缺点是用户必须找到一种安全共享对称密钥的方法。

#### 非对称加密

不对称加密使用两个在数学上相关的密钥(通常称为密钥对)。明文或文档数据用公钥加密，用相应的私钥解密。

#### 公钥加密

公钥加密是非对称加密的另一个名称。它被称为公钥加密，因为加密密钥可以公开共享，而解密密钥必须保密。