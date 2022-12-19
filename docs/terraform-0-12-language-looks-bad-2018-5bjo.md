# Terraform 0.12 语言看起来很糟糕(2018)

> 原文：<https://dev.to/ilya_sher_prog/terraform-0-12-language-looks-bad-2018-5bjo>

我曾希望聪明人对抗糟糕的情况会有另一个结果，但是 0.12 版本的地球语言看起来很糟糕…作为傀儡和不可理解的语言。

我并不是说制作木偶和木偶的人不聪明。那就是我们可以从他们所犯的错误中学习……除非我们不认为那些是错误。

Puppet 和 Ansible 经历了非常相似的困境。他们把自己限制在一个声明性的格式，然后他们试图适应现实生活。Terraform 现在就有这种情况。

情况是:

*   正在使用的声明格式
*   人们需要更强大的东西，比如编程语言，因为…在现实生活中，条件、循环和数据转换比解决声明性语言的限制更有意义。

有趣的是，他们都没有转换到合适的编程语言。也许因为这将至少部分地承认**产品首先应该是一个库**？

Terraform 实际上处于非常蹩脚的境地，因为即使他们决定将所有东西都公开为库作为主接口，我也没有看到人们开始将 Go 用于“基础设施作为代码”。反正没有 Ruby 或者 Python 流畅。

各位编码快乐！

**更新(2018-07-21):**

更积极的一点是，[新的 splat 操作符](https://www.hashicorp.com/blog/terraform-0-12-generalized-splat-operator)看起来是一个改进。

**更新(2018-07-27):**

Terraform 看起来更像是一种“普通”语言，有了[条件运算符改进](https://www.hashicorp.com/blog/terraform-0-12-conditional-operator-improvements)和`null`值。条件操作符修复了以前的奇怪之处。

**更新(2018-08-02):**

Terraform 得到了[型系统](https://www.hashicorp.com/blog/terraform-0-12-rich-value-types)。看起来很强大。只需要看到 Terraform 没有进化到 Scala [![🙂](../Images/2ea3454d48c385c4b31b2c45b77b4229.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zCyXRrdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f642.png)

**更新(2018-08-11):**

新的[模板语法](https://www.hashicorp.com/blog/terraform-0-12-template-syntax)带来了更多的原始能力。看起来不错。

**更新(2018-08-26):**

*   HCL 到 JSON [一对一映射](https://www.hashicorp.com/blog/terraform-0-12-reliable-json-syntax)。当我读到“在 HCL 和 JSON 之间有一个清晰的 1:1 映射，并确保 HCL 的每一个特性在 JSON 中都得到支持”时，我立刻想到，那时一定有转换工具…并且没有失望![🙂](../Images/2ea3454d48c385c4b31b2c45b77b4229.png)“在 Terraform 的未来版本中，我们还将支持本机工具，以将 HCL 转换为 JSON，并将 JSON 转换为 HCL(包括注释)”
*   “JSON 中的评论”——不错！