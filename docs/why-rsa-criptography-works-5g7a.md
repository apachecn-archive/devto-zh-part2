# 为什么 RSA 密码算法有效？

> 原文：<https://dev.to/pemtajo/why-rsa-criptography-works-5g7a>

[![](img/da9a5adee6993180766fa8b2a5d9c9a6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pJGLLk7S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AY3_lZTDC3ARsNpGSbyg0Pg.jpeg)

将数字相乘比分解容易得多。这是 RSA 加密的基础。

特别是，RSA 加密使用两个大素数 *p* 和 *q* 工作，可以快速轻松地找到乘积 *pq* ，但是从 *n* 中恢复因子 *p* 和 *q* 就比较困难。一般来说 *p* 和 *q* 都是几百位数的数字，对这种数量级的数字进行因式分解非常慢。

### 算法

为了对消息进行编码，我们需要 *n = pq* 和一个正整数 *e* 即
T5】是可逆模φ (n)。[我们之前讨论过欧拉的广义函数。](https://dev.to/pemtajo/euler-fermat-and-primality-test-2dc8)

我们将调用 RSA 系统的**编码密钥**对 *(n，e)* 。我们将分别对每个消息块进行编码，最终的消息将是编码块序列。

重要提示:已经编码的块不能组合成新的数。这应该使解码不可能，我们将在后面看到！

现在让我们展示如何对每个块 *b* 进行编码。我们将把编码块称为 *C(b)* 。首先记住 *b* 是比 *n* 小的整数。我们计算 *C(b)* 如下:

[![](img/97a1e704af84b67df29ccc6a39e45839.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mlM2teNy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/198/1%2A4zDFCGccZ8DMo1umjGetXg.png)

其中 *0≤C (b) < n* 。

让我们来看看如何解码一个块的编码信息。我们需要能够解码的信息由两个数组成: *n* 和 *e* 模 *φ(n)* 的倒数，我们表示为 *d* 。我们将调用**解码密钥的对 *(n，d)* 。**被编码的消息的一个块。那么 *D(a)* 将是解码过程的结果。计算 *D(a)* 的方法是:

[![](img/1a30489f6fe732db82eef431b14297bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kTe5zyKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/212/1%2Apx6aU2jIUGYE5TpsUQfKhQ.png)

其中 *0 ≤ D (a) < n.*

首先，计算 *d* 是非常容易的，如果我们有 *φ(n)* 和 *e* 的话:简单应用扩展的欧几里德算法。另一方面，很明显，如果 *b* 是原始消息的一个块，那么我们期望 *D(C(b)) = b* ，否则我们将没有一个有用的代码。

最后，从笔记开始我们就一直坚持用 *n* 编码，用 *p* 和 *q* 解码；所以你需要因式 *n* 来解码。在这一点上，我们看到这并不完全正确。除了 *n* 本身，我们只需要知道 *e* 模 *φ(n)* 的逆 *d* 就可以解码了。原来我们只知道如何将扩展算法应用到 *e* 和 *φ(n)* 上来计算 *d* 。

[![Alt Text](img/76953ea54e1b9132d8b5947e3e1abce0.png)T2】](https://i.giphy.com/media/hyPsnhMnltMhq/giphy.gif)

### 证明 RSA 有效

现在出现的明显问题是: *D(C(b)) = b* ？

即通过解码一个编码的消息块，我们能找到一个原始消息块吗？因为否则我们所有的努力都是没有意义的…

想想看 *n = pq* 。我们将证明 *D(C(b)) ≡ b mod (n)* 。

如何 *1≤ b，D (C (b)) ≤ n-1* 。

根据定义，我们有:

[![](img/9bc914e0b3cbf6d4de6d367008735726.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZYXK5Umz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/335/1%2ASYMXQkVQAvE7ZIrnsrKljQ.png)

但是 *d* 是 *e* 模 *φ(n)* 的逆。然后有一个整数 *k* 使得 *ed=1 + kφ (n)。*

不久，

[![](img/9dfc5074e3894be2cdcab4971a3cc16e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Jz5NRPt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/282/1%2Ao_rMOeSCjobzqpDrqgH47Q.png)

如果 *b* 和 *n* 是亲戚素数，那么我们可以用[欧拉定理](https://dev.to/pemtajo/euler-fermat-and-primality-test-2dc8):

[![](img/7759732f4275b6e2914e2ef3c6ed682a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wl3I1LTl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/313/1%2AVBny99LCYz9khl8KHta3Cw.png)

假设 *b* 和 *n* 互不质数。由于 *n = pq* ， *p* 和 *q* 是不同的素数，那么 *φ(n) = (p-1)(q-1)* 。

不久，

[![](img/d8fb58e2b0376b63bf65f2df8c5dc81d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q838YBWS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/215/1%2AzIXL3s2Xuv69nQ9K0PXk8g.png)

如果 *b* 和 *p* 是相对质数，那么我们可以用费马定理，我们有 bᵖ⁻ ≡ 1 mod (p)。

因此， *bᵉᵈ ≡ b mod (p)* ，取其一 *b* 。

我们对质数 q 做同样的处理，我们得到， *bᵉᵈ ≡ b mod (q)。*

因此，

[![](img/4ab6d8b2573efc0b092f6b363699db14.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---TOEF31x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/196/1%2AM-3_xtH9Jv4B8iC72TYrqw.png)

如我们所愿。

### 实现

作为一名开发人员，我有必要把代码放在任何地方，即使在不需要的时候，所以我在这里放一个这个算法的简单实现，以后会有用的

[![](img/6876209553efc5eaee6dfcfcd75b65ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qE8T4Dy5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzL2la0u4YBEtTofwqbDBtw.png) 

<figcaption>这里的代号是</figcaption>