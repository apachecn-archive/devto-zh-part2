# 欧拉、费马和素性检验

> 原文：<https://dev.to/pemtajo/euler-fermat-and-primality-test-2dc8>

[![](../Images/977bc290c9484b4af6ff3427a18e3a54.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FQVk4y4---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AgyD2i1OZWaKSDWB6rj8nFw.jpeg)

在数论中，**欧拉的全等函数**，计算小于 m 且与 m 互质的正整数的个数。对于一个素数 *p* ， *φ(p) = p-1* 。

它可以更正式地定义为范围 *1 ≤ k ≤ n* 中最大公约数 *gcd(n，k)* 等于 1 的整数 *k* 的个数。

### 什么是**费马小定理**

[**费马小定理**](https://en.wikipedia.org/wiki/Fermat's_little_theorem) 说如果 *p* 是素数 *a* 不是 *p* 的倍数，那么

[![](../Images/e516c65757b7e31dbb6fe18d21d03c78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LtrNR985--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/173/1%2ACotGjw6Dd51xqzfvJvSNFA.jpeg)

**欧拉对费马小定理的推广**说，如果 *a* 与 *m* 互质，那么

[![](../Images/12c7922401bf199033bccf48676e8c5f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kNKHflHD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/225/1%2ABPy80qeKAXp309Edp0qolA.jpeg)

> 欧拉的全等函数是**乘法**，即如果 *a* 和 *b* 互质，那么φ(*ab*)=φ(*a*)φ(*b*)。我们将在其他讨论中用到这个事实。

### 一般化的证明

Be *r=φ(n)* 和 *b₀、b₁、…、bᵣ、*整数，素数相对于二为二，所有素数相对于 *n* 。所以 *ab₀，ab₁，…，abᵣ，*仍然全等*mod(n)*for

*I = 1，2，…，r.*

集合 *b₀，b₁，…，bᵣ* 和 *ab₀，ab₁，…，abᵣ* 等于 *mod(n)* 。所以让乘法所有

[![](../Images/53f63cb467c87e20f975f26dc7a3b3d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lTVNJ5Es--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/279/1%2A2nRFSnPCtON2oJT0g7jawQ.png)

所以，

[![](../Images/fbf707b4d2658a46c819fad3a2c8cd96.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YmAEE7MO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/493/1%2AR5O59TzgeqQVCMkcx2roAQ.png)

总之， *(aʳ-1) ≡ 0 (mod n)* 和如何 *aʳ ≡ 1(mod n)* 和

*r=φ(n)* ，那么

[![](../Images/8f06583cd653dc26280812b36a018c91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CfFBTPbL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/255/1%2AOk_DVBZh52E-ES2yp3AOgQ.png)

### 素性测试

这个定理最好的一点是素性测试。

费马小定理的**逆命题**是有用的:如果同余式*a*ᵖ⁻*≦*1(mod*p*不*真，那么要么 *p* 是*不是*素数要么 *a* 是 *p* 的倍数。实际上， *a* 比 *p* 小得多，因此可以断定 *p* 不是素数。*

从技术上讲，这是一个非素数的测试:它只能证明一个数不是素数。例如，如果 2ᵖ⁻ ≢ 1 (mod *p* )那么我们知道 *p* 不是素数。但是如果 2ᵖ⁻*≦*1(mod*p*)那么我们所知道的就是我们没有测试失败；我们不确定 p 是否是质数。所以我们尝试 *a* 的另一个值，比如 5，看看 5ᵖ⁻*≦*1(mod*p*)。

理论上看起来很完美，所以所有的密码理论都被毁了？当然不是，因为即使很容易理解，从计算的角度来看，这也是有问题的，例如，对于像 223 这样的小数字，对于值为 2 的 *a* ,我们有；

[![](../Images/2973840f7c4ce3afc4357e1ae033cd03.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4I8LMD3Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/406/1%2Al1R6OLprvEaE8bu_C-Ulrw.png)

我们知道 223 是质数，但是 2 即使在健壮的计算机中也很难计算，所以像 2321412341243123423413263466567678352323 这样的数字更难确定，但是所有的理论都是有用的，在密码学和数论中有许多含义。我将在其他帖子中讨论这个问题。