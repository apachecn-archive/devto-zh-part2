# 使用 3D 打印解决问题

> 原文：<https://dev.to/jumpalottahigh/solving-problems-using-3d-printing-5afe>

最近，我遇到了一个非常小的问题，不过这是使用 3D 打印解决的最佳选择。我对 3D 打印相当陌生，几周前才拿到我的打印机，我确实有一些 3D 建模经验(主要来自几年前的业余爱好/学校游戏项目，如[迷宫爬虫](https://blog.georgi-yanev.com/eternal-archives/game-dev-videos/)或[翻滚的鲨鱼](https://blog.georgi-yanev.com/eternal-archives/game-dev-videos/))。我使用令人敬畏的开源软件 [Blender](https://www.blender.org/) 进行我的 3D 建模。所以很明显，我不是大师，但非常渴望学习，我一头扎了进去。

### ❓问题

我儿子用这些宜家盒子存放他的玩具。在过去的几年里，它们得到了如此多的使用，以至于有些危险和松散。有几次，底板掉了出来，盒子折了进去。这已经越来越成为所有 4 个盒子的问题。

 [<source type="image/webp">
<source type="image/jpeg">
![Picture of IKEA box](../Images/5624a7ce0370f8b1d30e5bf790e21628.png "Picture of IKEA box")](///static/2236c720e9ecc1e6527e95ea9d5ec87d/9c5b7/solving-problems-using-3d-printing-1.jpg) 

底板也是将整个箱子固定在一起的东西，如果固定得很好，它会被支撑在侧边上，然后用两个支架固定住。

 [<source type="image/webp">
<source type="image/jpeg">
![Bottom of IKEA box](../Images/5f06177e39de410f04167d643d5d97e8.png "Bottom of IKEA box")](///static/729b9231d49b0d4166f6398da7acd216/9c5b7/solving-problems-using-3d-printing-2.jpg) 

 [<source type="image/webp">
<source type="image/jpeg">
![Bracket from the side](../Images/d70df5f8b25bb28e94fa4d32a96aed79.png "Bracket from the side")](///static/796904a4143516cc2c668e5fd6d77803/9c5b7/solving-problems-using-3d-printing-3.jpg) 

 [<source type="image/webp">
<source type="image/jpeg">
![Bracket from top down](../Images/2fa202389cef2d097bf9bbb35f619818.png "Bracket from top down")](///static/921618a82c1ce17d14c5f1e7ea097458/9c5b7/solving-problems-using-3d-printing-4.jpg) 

支架一开始总是有点短，但到目前为止完成了工作，直到盒子变得太松。要是我们能有稍微长一点的支架就好了。*回车，3D 打印！*

### 👨🏼‍🎨️原型

作为 3D 打印解决方案的绝佳候选人，我很快就把它分解了。以下是我想象我需要做的事情:

#### 1。用卡尺测量当前支架

 [<source type="image/webp">
<source type="image/jpeg">
![Measuring with a caliper 1](../Images/584268e78dd69d50156c882ff96986f9.png "Measuring with a caliper 1")](///static/e4fca9513d47b3d9daabc938b77759e7/9c5b7/solving-problems-using-3d-printing-5.jpg) 

 [<source type="image/webp">
<source type="image/jpeg">
![Measuring with a caliper 2](../Images/a3103fa34c7b530ed8bb5bd2c48d028f.png "Measuring with a caliper 2")](///static/b993caff33786eb610e79e5683903683/9c5b7/solving-problems-using-3d-printing-6.jpg) 

#### 2。在 Blender 中制作一个 3D 模型，然后将 X 轴上较长的部分延长大约 33%

 [<source type="image/webp">
<source type="image/png">
![3D model in Blender](../Images/a8ab4479a01bef2c23fc335d7e3dd662.png "3D model in Blender")](///static/3999b7499d17479ca26c6b507dd88431/cb8e1/solving-problems-using-3d-printing-7.png) 

 [<source type="image/webp">
<source type="image/png">
![3D model in Blender with a cylinder through](../Images/e654cf6423a9a74ea95cb4b65b350887.png "3D model in Blender with a cylinder through")](///static/d561c9a9283aa2aece87594df5f0aa18/cb8e1/solving-problems-using-3d-printing-8.png) 

 [<source type="image/webp">
<source type="image/png">
![3D model in Blender with a circle shaped hole cutout](../Images/7df381e930f79100b8a54988d1526e67.png "3D model in Blender with a circle shaped hole cutout")](///static/5046e4ef7604283b6036f235744524e2/cb8e1/solving-problems-using-3d-printing-9.png) 

#### 3。导出为`.STL`文件并在切片程序中打开( [Cura](https://ultimaker.com/en/products/ultimaker-cura-software)

 [<source type="image/webp">
<source type="image/png">
![Configuring the 3D print in Cura](../Images/0f91e734e25ced848ab6948206912f0c.png "Configuring the 3D print in Cura")](///static/418db159f456f1e82c906cd963734df1/83649/solving-problems-using-3d-printing-10.png) 

#### 4。打印

 [<source type="image/webp">
<source type="image/jpeg">
![The final 3D printed bracket](../Images/5a0d381943f3900078525282de29b02c.png "The final 3D printed bracket")](///static/bdc1f557cf863ac74e7bdd6b09cdf25a/9c5b7/solving-problems-using-3d-printing-11.jpg) 

打印很快，大约 10 分钟，质量非常好。

#### 5。测试配件

 [<source type="image/webp">
<source type="image/jpeg">
![Testing the 3D print in the IKEA box](../Images/c3edbe9a9309d3322f2a1f62abc4bc38.png "Testing the 3D print in the IKEA box")](///static/604f56bb328160825bc13d00daf5a7de/9c5b7/solving-problems-using-3d-printing-12.jpg) 

老实说，我实际上有点惊讶我第一次尝试就完全正确了，我以为这需要几次迭代。支架非常合适，绝对比股票期权更好地支撑着盒子的底部面板。

### 🔩制造业

所以，很自然地，我去了 Cura 并增加了这个项目，所以我可以多印 7 个。

 [<source type="image/webp">
<source type="image/png">
![Multiply item in Cura](../Images/220e319a80c86a4337b0d1fb964c7065.png "Multiply item in Cura")](///static/3620a3801410bb6e21e4cb19ed250585/83649/solving-problems-using-3d-printing-13.png) 

 [<source type="image/webp">
<source type="image/jpeg">
![Printing 7 items at the same time](../Images/1726b9a82dbf01eb839bb0e7cb466e63.png "Printing 7 items at the same time")](///static/7fc55dae989659daef9484ba791d1110/9c5b7/solving-problems-using-3d-printing-14.jpg) 

 [<source type="image/webp">
<source type="image/jpeg">
![3D printer touch screen](../Images/af8516f0754658a696a6cb0625e9f7cd.png "3D printer touch screen")](///static/9f51ddecc77d61095bcc053dc9814976/9c5b7/solving-problems-using-3d-printing-15.jpg) 

只需 45 分钟即可完成 7 张照片。

 [<source type="image/webp">
<source type="image/jpeg">
![Half way through the print](../Images/67b3e9b860e721c8616e314e91d9e3a9.png "Half way through the print")](///static/ce8ae66d946f731619a847b73cacc7a6/9c5b7/solving-problems-using-3d-printing-16.jpg) 

中途通过

 [<source type="image/webp">
<source type="image/jpeg">
![Print done](../Images/ee0843db6ae6cb1a2a28e9d99796b708.png "Print done")](///static/e797993f2ad77befc67d0223008f99fb/9c5b7/solving-problems-using-3d-printing-17.jpg) 

准备好了。

 [<source type="image/webp">
<source type="image/jpeg">
![7 3D printed brackets](../Images/d32ee2fcfd93cf445a8d806b9e109968.png "7 3D printed brackets")](///static/d5d1a4e3693ca56aa4f576cc1aed97a3/9c5b7/solving-problems-using-3d-printing-18.jpg) 

最后一项任务是更换所有箱子上的支架。

### 🏁最后

我知道我没有解决一些重大的世界问题，但是，你从能够使用 3D 打印和软件解决问题中获得的满足感是巨大的。我只能想象这种技术的光明前景，只能打赌它很快就会成为每个家庭的一部分。