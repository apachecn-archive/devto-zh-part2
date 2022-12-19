# JPA 代码示例(Ep.1)

> 原文：<https://dev.to/slumboy/jpa-code-example-ep1-4dgi>

#### 开始无限期

* * *

本章可能看起来有点混乱，先生，但没有问题我们会坚持什么，因为本章将使用构建基础。

`class`
`method`
`getter`

我们先从第一章开始

#### 我们需要创造的是这样的:

实体或 POJO 1 是一个在数据库中对表执行映射的实体。

*   Employee.java

服务是关于创意人、更新、芬兰人、德莱特人的。

*   CreateEmployee.java
*   DeleteEmployee.java
*   FindEmployee.java
*   UpdateEmployee.java

Persistence 是我们将在一天中简单调用的，它是一个文件，它使用的是 config，类似于 poperties 文件，通常在`META-INF`中已经存在。

*   Persistence.xml

* * *

#### 会得到一个大概的样子

[![alt text](../Images/bd24029fe46c35633dc1d9ad42b7cf8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XcJvlaBb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ynzakmbdaf8oit2heyq8.PNG)

* * *

### 如何构造

事实上，我们可以按照自己的方式来做，但如果我照下面的例子来做，t 0㈱1。建立预先包装

`คลิกขวาที่ src -> new --> Package`

## T2】

2.命名包
样品`com.ชื่อโปรเจค.ชื่อสำหรับบอกการใช้งาน`是我自己的包，但它并没有死，我们可以照我们所理解的做它。

* * *

1.  创建类〔t0〕t1㎡t1㎡

##### 观察

`Package : มันจะชี้ไปที่ๆเราสร้างไว้ เพื่อบอกว่า class ที่เรากำลังสร้างอยู่ตอนนี้จะอยู่ภายใต้ package นี้`
T1】

[![alt text](../Images/5c8dd7f9737773aae0bae8aa2d524269.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eJ4u0kap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ss8uwhwo79gxp6akbg8.PNG)

* * *

4.服务包中剩下 4 个，下面有 4 个类。我们可以自己创建它们。
[![alt text](../Images/d0a78417bc57598b181dd514f0eae119.png)![alt text](../Images/d0a78417bc57598b181dd514f0eae119.png)。](https://res.cloudinary.com/practicaldev/image/fetch/s--MBoKtLhy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ltro6qzqjtb3dl4ku4m.PNG)