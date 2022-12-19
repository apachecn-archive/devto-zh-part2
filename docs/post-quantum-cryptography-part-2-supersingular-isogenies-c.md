# 后量子密码术，第 2 部分:超奇异同源

> 原文：<https://dev.to/artis3n/post-quantum-cryptography-part-2-supersingular-isogenies-c>

<small>*最后更新:2018-03-01*</small>

*这篇文章最初发表在[我的博客](https://blog.artis3nal.com/posts/supersingular-isogenies)上。*

在本系列的第一部分中，我讨论了量子力学的一些基础知识，以及它们如何应用于量子计算。在这一部分中，我将继续讨论后量子函数，从超奇异同源的 Diffie-Hellman 开始。

声明:我写这篇文章是为了了解超奇异同源。我想我基本上失败了。相信这些内容，后果自负。

一、[什么是后量子密码学](https://downloads.cloudsecurityalliance.org/assets/research/quantum-safe-security/what-is-post-quantum-cryptography.pdf)？后量子密码(PQC)是使用经典方法(相对于量子力学)来创建能够抵抗已知量子攻击的函数。[量子密码术](https://en.wikipedia.org/wiki/Quantum_cryptography)，另一方面，利用量子力学原理实现密码目标。PQC 的希望是，它将成为当前加密方法的替代方案。正如我在上一篇文章中提到的，[肖尔的算法](https://en.wikipedia.org/wiki/Shor%27s_algorithm)和[格罗弗的算法](https://en.wikipedia.org/wiki/Grover%27s_algorithm)是设计用于在量子计算机上运行的算法。有了足够的量子位，Shor 的算法就可以用来破解非对称加密方案，比如 RSA。Shor 算法的修改版本可用于破解椭圆曲线密码。Grover 的算法不能破解对称加密方案，但确实将暴力破解对称方案所需的工作量减少了一半(AES-256 将具有与今天的 AES-128 相同的安全保证)。Shor 和 Grover 的算法是量子密码分析，但使用 PQC 函数可能会减轻它们的影响。

### 超奇异同源迪菲海尔曼

PQC 函数有几个系列。我们首先要看的是[超奇异同源](https://en.wikipedia.org/wiki/Supersingular_isogeny_key_exchange)，具体来说就是[超奇异同源 Diffie-Hellman](https://www.lvh.io/posts/supersingular-isogeny-diffie-hellman-101.html) (SIDH)。我假设你熟悉[迪菲-海尔曼](https://security.stackexchange.com/a/45971) (DH)。如果你不是，链接栈交换帖子是 DH 如何工作的一个很好的简明解释。SIDH 以抗量子的方式反映了 DH 的功能。我现在不打算详细讨论[椭圆曲线加密](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography/) (ECC)，尽管我可能会在以后的另一篇文章中讨论它。我所链接的这篇文章非常好地解释了 Cloudflare 中椭圆曲线加密的工作原理。然而，我们需要了解一些关于椭圆曲线的知识，因为 SIDH 依赖于应用于椭圆曲线的附加属性。简而言之，椭圆曲线是定义在有限域上的曲线，用 [Weierstrass 形式](https://en.wikipedia.org/wiki/Weierstrass%27s_elliptic_functions)，*y<sup>2</sup>= x<sup>3</sup>+ax+b*表示，并且*非奇异*，这意味着曲线上的每一点都必须有唯一的切线；该曲线不自相交或包含任何尖点。椭圆曲线定义在有限域 *K* 上。所有椭圆曲线在无穷远处都有一个特殊的点叫做[点](https://en.wikipedia.org/wiki/Point_at_infinity)和一个由 *K* 定义的群结构——定义在那个 *K* 域上的有理点。

安是椭圆曲线间[态射](https://en.wikipedia.org/wiki/Morphism)的一种特殊类型。这意味着曲线之间的映射是满射的——第二条曲线上的每个点都被第一条曲线上的至少一个点映射到——并且曲线上的点是在一个有限的空间上定义的，称为同源核。两条椭圆曲线之间的同构是一种有理态射，它将曲线 1 上的无穷远点映射到曲线 2 上的无穷远点。同源可以通过其“核”来唯一地识别，核是源曲线上映射到目标曲线无穷远点的点的子组。同源通常表示为一组公式 <sup id="f1">[1](#velu)</sup> 。

一条 [*超奇异的*椭圆曲线](https://en.wikipedia.org/wiki/Supersingular_elliptic_curve)是一种具有非常高的秩为 4 的自同态环的椭圆曲线。从数学的角度来看，这个属性很重要，但是对于本文来说，您不需要理解这个属性。此外，“超奇异”的使用与上述椭圆曲线的*非奇异性*特性无关。超奇异椭圆曲线是非奇异的。“超奇异”是指椭圆曲线的[j-不变量](https://en.wikipedia.org/wiki/J-invariant)具有奇异值的事实。出于本文的目的，你只需要明白 j-不变量是一个数学函数 <sup id="f2">[2](#j-invariant)</sup> 。这种超奇异性意味着超奇异椭圆曲线的每个 j-不变量都等于一个代数整数。j-不变量的这个性质很重要，我们马上就会看到。

SIDH 研究一族超奇异的椭圆曲线，并且可以在族中任意两条曲线之间定义同源。SIDH 是这样工作的:甲方要和乙方交流，双方在家族中选择两条曲线，各自导出一个秘密值。他们在选择的一条曲线上选择一个点，并创建到第二条曲线的内核映射(第 1 组)。通过应用超出本文范围的同源公式，将它们的秘密值输入到那些函数中，每一方创建新的核和新的点对(组 2)。这些新观点是两党共有的。基于从共享点(组 3)导出的内核创建新的同源。每一方如前所述通过同源公式运行这些组 3 点，再次输入它们各自的秘密值，以便导出新的点和同源/核(组 4)。每一方基于组 4 同源计算新椭圆曲线的新系数。最后，每一方计算他们最新曲线的 j 不变量。如果握手成功，这个 j 不变量对双方都是一样的。类似于在 DH 中确定产生某个大数的质因数的困难，仅知道该大数，给定几乎所有信息的知识，例如组 1 和 2 点以及共享的组 3 点，很难确定组 4 点，因此很难确定 j 不变量，从而保护双方之间的通信。

### SIDH 的优缺点

现在我们已经了解了使用 SIDH 的密钥交换过程，让我们看看使用这个 PQC 函数的优点和缺点。最重要的是，SIDH 的安全保障是什么？当前的研究描述了断裂 SIDH 的难度，其是计算同源超奇异椭圆曲线之间的同源的难度，在:

[![Hardness of breaking SIDH](img/d04ccef5317bfb921076fa41cd82bbb0.png)T2】](///assets/sidh/sidh_hardness-cf073215be321a3943cf24b785546aa480e6d98fa9b11ef0e69223d06e122463.jpg)

其中 *p* 是一些长数，例如 128 位安全的 768 位数字*2 * 2<sup>386</sup>* 3<sup>242</sup>-1*。我们可以看到，经典计算机和量子计算机之间的硬度差异非常小，从而成功缓解了量子计算对安全密钥交换的威胁。

SIDH 最大的缺点是计算量非常大，特别是与其他 PQC 函数族相比，比如基于格的密码系统(我们将在后面的文章中讨论)。这使得这个函数比它的替代品慢得多。SIDH 键非常小，这在一定程度上减轻了这种损失。

如果你想要更多的资源来了解 SIDH，我发现[这个视频讲座](https://www.youtube.com/watch?v=PW5Vsu57o9I)很有用，还有[这个关于 SIDH 利用外星人的愚蠢比喻](https://gist.github.com/defeo/163444a53252ba90cca6a3b550e6dd31)。[这篇文章](https://crypto.anarres.info/2017/sidh)对围绕 SIDH 的数学进行了很好的解释。 [Cloudflare](https://blog.cloudflare.com/sidh-go/) 也有一篇非常好的文章描述了 SIDH 的机制，以及 SIDH 在 Go 中的实现。在本系列的下一部分，我们将研究基于格的密码学以及 RLWE 和 NTRU PQC 函数。

 <small>:这些被称为[韦卢公式](https://eprint.iacr.org/2011/430.pdf)。[↩](#f1 "return")T7】</small>

 <small>:出于好奇，SIDH 使用的所有椭圆曲线都由以下 j 不变量定义: [↩](#f2 "return")</small> [![J-Invariant function](img/98f7bb50371ff313a23a7f62d6e0655a.png)](///assets/sidh/j-invariant_function-a973bf31bcb9acf567d775c7222a4c4fa4b69d30a67a1cb5f0e758640434404b.jpg)