# 简单的 MATLAB CFD 模拟教程-绕圆柱体的流动

> 原文：<https://dev.to/featool/easy-matlab-cfd-simulation-tutorial---flow-around-a-cylinder-5g2g>

本教程模型示例说明了如何使用 MATLAB 的 [FEATool Multiphysics FEM 和 CFD 工具箱](https://www.featool.com/matlab-cfd-toolbox)轻松快速地设置和解决计算流体动力学问题。

[![MATLAB GUI for CFD simulations](img/d1aa38b7efb3dbc064c13d9c675f5a2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qoe7IzZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8dd9xhg1cg6ea9psm2am.jpg)

[YouTube 圆柱绕流 CFD 基准 MATLAB 教程](https://youtu.be/ZnnXl7ryBMI)

该模型示例研究 Re=20 的通道中圆柱形障碍物周围的静态和层流。将计算出的压降、阻力和升力系数与已建立的基准参考进行比较，以确定模拟的准确性。

尽管本教程中没有介绍，但除了内置的静态、非线性和瞬态流动解算器之外，FEATool 还为 FEniCS 和 OpenFOAM CFD 解算器提供了内置的 GUI 界面。已经进行了一项基准研究，比较了 OpenFOAM、FEniCS 和 FEATool 在该流体流动测试案例中的表现，并说明了如何在 Matlab 中执行 [CFD bencmark 和模拟。](https://www.featool.com/cfd/2018/07/02/fenics-openfoam-matlab-cfd-flow-solver-benchmark)

该模型也可在 FEATool Multiphysics 用户指南的教程和示例部分找到:

[FEATool 用户指南:圆柱绕流 CFD 基准 MATLAB 教程](https://www.featool.com/doc/tutorials.html#ex_ns1)

参考资料:

[1] John V，Matthies G .不可压缩流基准问题中的高阶有限元离散化。2001 年国际流体数值方法杂志。

[2] Nabh G .关于稳态不可压缩纳维尔-斯托克斯方程的高阶方法。博士论文，海德堡大学，1998 年。