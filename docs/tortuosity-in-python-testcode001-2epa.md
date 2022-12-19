# Python 中的曲折性，test_code_#001

> 原文：<https://dev.to/elem3ntal/tortuosity-in-python-testcode001-2epa>

曲折度可以定义为*“它有不规则的弯曲、曲线和起伏，并且以不同的方式出现。”*所以计算一个描述符*(和一个指数)*可以代表那个样本*(人)*产生曲折度*(一般一个算一个均值和一个标准差，我就不深究那些计算了)*。另一个*(个人)*我喜欢的定义是“如果我取这个点，在不改变他的方向的情况下，可以移动多远”*(也许，这个定义被实现了)*。

一般来说，这个解决方案需要一个二进制图像*(手写)*。这个过程是创建一个复制层*(我称之为草稿)*，然后，根据连通性，以递归方式发送计算二进制矩阵。

这段代码考虑了 4 倍和 8 倍的连接性，我认为这可能是 16 倍连接性的改进。

*   乘 4:水平和垂直。
*   乘 8: 4 加上它们的对角线(在 45)。
*   *(按 16，每隔 22 )*

但是对于这个代码所产生的问题，测试集中的结果并没有表明需要扩展连接性

#### 代码

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 
import math
import numpy as np

def __conect(image, pos, vect):
    limits = np.shape(image)
    if pos[0] < 0 or pos[0] == limits[0] or pos[1]< 0 or pos[1] == limits[1]:
        return 0
    elif image[pos]:
        return 1+__conect(image,(pos[0]+vect[0],pos[1]+vect[1]),vect)
    return 0

def conect(image, label=4):
    draft = np.zeros(np.shape(image)).astype(int)
    image = image.astype(bool)
    pos=np.where(image==True)
    for i in range(len(pos[0])):
        weights = []
        if label in [4, 8]:
            weights.append(__conect(image,(pos[0][i],pos[1][i]+1),(0,1))+__conect(image,(pos[0][i],pos[1][i]-1),(0,-1))) #vertical
            weights.append(__conect(image,(pos[0][i]+1,pos[1][i]),(1,0))+__conect(image,(pos[0][i]-1,pos[1][i]),(-1,0))) #horizontal
        if label in [8]:
            weights.append(__conect(image,(pos[0][i]+1,pos[1][i]+1),(1,1))+__conect(image,(pos[0][i]-1,pos[1][i]-1),(-1,-1))) #slash
            weights.append(__conect(image,(pos[0][i]-1,pos[1][i]+1),(-1,1))+__conect(image,(pos[0][i]+1,pos[1][i]-1),(+1,-1))) #backsalsh
        draft[pos[0][i],pos[1][i]]= 1+max(weights)
    return draft 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是一个命名为*(可选)*的参数、递归和移动二进制矩阵的例子，没有 **的*double * *。*

#### 输入

```
image = np.array([[0,0,0,0,0,0,0],
[0,1,0,0,0,0,0],
[0,1,1,1,1,1,0],
[0,1,1,1,1,1,0],
[0,1,1,1,1,1,0],
[0,1,1,1,1,1,0],
[0,0,0,0,0,1,1]])
print("Original: \n", image)
print("Connect 4 (by default):\n", conect(image))
print("Connect set in 8:\n", conect(image, label=8)) 
```

Enter fullscreen mode Exit fullscreen mode

#### 输出

```
Original: 
 [[0 0 0 0 0 0 0]
  [0 1 0 0 0 0 0]
  [0 1 1 1 1 1 0]
  [0 1 1 1 1 1 0]
  [0 1 1 1 1 1 0]
  [0 1 1 1 1 1 0]
  [0 0 0 0 0 1 1]]
Connect 4 (by default):
 [[0 0 0 0 0 0 0]
  [0 5 0 0 0 0 0]
  [0 5 5 5 5 5 0]
  [0 5 5 5 5 5 0]
  [0 5 5 5 5 5 0]
  [0 5 5 5 5 5 0]
  [0 0 0 0 0 5 2]]
Connect set in 8:
[[0 0 0 0 0 0 0]
 [0 6 0 0 0 0 0]
 [0 5 6 5 5 5 0]
 [0 5 5 6 5 5 0]
 [0 5 5 5 6 5 0]
 [0 5 5 5 5 6 0]
 [0 0 0 0 0 5 6]] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

> 我的*‘测试代码’*
> 
> 它们是一些小段代码，用于尝试一些我可能不知道的东西，或者我不想在主项目中进行测试，因为它们是很好工作所必需的。通常都是与当前的一些项目相关的*(工作)*，有时也是为了改进别的东西*(调试、打印、算法、效率等)...)*。我有许多这样的文件*(称为 test#。py 或 delete#。因此，我将试着解释为什么我认为它们是有趣的，我希望对你也是如此。如果你认为我的英语不好，我道歉，那不是我的母语。*