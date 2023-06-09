# ELI5:量子网络应用程序(Javascript)

> 原文：<https://dev.to/prnthh/imagine-a-quantum-web-app-in-javascript-l2d>

我和一个正在学习量子编程的朋友聊天，量子编程是他硕士学位的一部分。当他向我描述状态纠缠的概念时，我开始觉得这个概念非常接近 Redux 和 web 上的状态管理。所以，我写了这个帖子来阐述我的一些想法。

## 什么是量子编程语言？

量子编程语言实际上是针对量子计算机的特定类型的机器代码，与经典力学相比，量子计算机是基于量子力学的。

经典计算机的内存由位组成，每一位用 0 或 1 表示。

另一方面，量子计算机维护一系列量子位，可以表示 0、1 或这两种量子位状态的任何量子叠加。
[![A classical bit vs. a qubit](img/4c9382c01c83389dd4e39233836787cf.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--x6KYE0Wr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.prowesscorp.com/wp-content/uploads/2017/06/Prowess_Quantum_bit_qbit.jpg)

量子计算机可能能够有效地解决在经典计算机上不可行的问题，由于丘奇-图灵论题，我们知道经典计算机可以在技术上模拟量子算法。

## 为什么要量子编程？

IBM 推出了供公众使用的云量子计算机，微软披露了 Q#量子编程语言，D-Wave Systems 与美国宇航局合作发布了 D-Wave-2。看起来量子计算正在成为现实！

由于量子编程基于量子力学，它受益于一些与之相关的酷现象。

**量子纠缠**是一种物理现象，当一组粒子以某种方式相互作用时，即使粒子相距很远，每个粒子的量子状态也无法独立于其他粒子的状态进行描述。

[![Two particles share their state through quantum entanglement.](img/249fc656c23b6ebdd5b9689c14a44130.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0UotuHhT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s1fzeyd9220kj4ngrm6f.gif)

假设我们有两个粒子 A 和 b。这两个粒子都处于可能状态的叠加状态——在这种情况下，它们同时变成红色和蓝色。如果我们测量 A 的颜色，它选择红色，而其他人马上测量 B——B 将永远是蓝色。

纠缠意味着不管 A 和 B 之间的距离有多远，B 在瞬间就知道 A 选了什么。

## Web 应用中的状态

[![State management in a web application](img/1f61a87f6bb7cdefcb4b0b21a46bdd13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sdJZq6jV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3cc8hx482owdee9uz84o.jpg) 
在今天的 web 应用中，我们面临着在前端和后端维护相同状态的类似挑战。随着 Redux/Flux 等各种现代工具的使用越来越多，我们显然需要一种更优雅的解决方案来跨越物理距离进行状态管理。

## 量子态纠缠

实际数据是如何表示的？这是基于一种叫做超密集编码的过程来完成的。超密集编码是一种使用单个量子位发送两个传统信息位(00、01、10 或 11)的方法。

让我们假设我们的 web 应用程序的状态被编码到一个这样的量子位数组中。我们的目标是将服务器端的状态与客户端的状态结合起来。

1.  为了增加叠加特性，我们增加了一个量子比特的哈达玛门(H)。
2.  我们增加了受控非门(CX)，这是一个两量子位的门，如果控制处于状态 1，它就会翻转目标量子位。这个门产生纠缠。![Attaining quantum entanglement of states.](img/db3998b47346d5b1d0affdb0a2a11d51.png)

这两种量子逻辑运算的结合使我们的量子比特对达到了贝尔态。

### 铃响状态

贝尔态是代表量子纠缠最简单例子的两个量子位的特定量子态。

## 代码

下面这段代码真的只能在量子计算机上运行:P

```
# Assuming we have two qubits, qr[0] and qr[1]
# Add the H gate in the Qubit 1, putting this qubit in superposition.
qc.h(qr[1])

# Add the CX gate on control qubit 1 and target qubit 0, putting the qubits in a Bell state i.e entanglement
qc.cx(qr[1], qr[0])

# Add a Measure gate to see the state.
qc.measure(qr[0],cr[0])
qc.measure(qr[1],cr[1])

# Compile and execute the Quantum Program, since it needs to be simulated
results = qp.execute(['HelloWorldCircuit'], backend,timeout=2400) 
```

量子编程仍然是一个高度理论化的领域，在我们建立一个更加实用的量子处理器之前，我们不会真正看到它的性能改进。然而，我希望更多地了解范例会让您在未来的工作和项目中有更多的担心。:D