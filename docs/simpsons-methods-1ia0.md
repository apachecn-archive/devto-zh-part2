# 辛普森方法

> 原文：<https://dev.to/lindaeliza/simpsons-methods-1ia0>

在解释辛普森方法的用途和举例之前，有必要给出数值方法的定义。

数值方法是一种数学技术，用于解决无法解析解决或难以解析解决的数学问题。解析地解决一个问题就是以数学表达式的形式给出一个精确的答案。

换句话说，数值方法是一种算法，它收敛到接近精确答案的解。这个解叫做*数值解*。

所以，先说正题。辛普森方法用于使用二次(辛普森的 1/3 方法)和三次(辛普森的 3/8 方法)多项式来逼近函数 *f* ( *x* )在从 *a* 到 *b* 的区间上的积分 I( *f* )的值。当解析积分困难或不可能时，以及当被积函数是一组离散点时，使用这些方法。

> 辛普森的 1/3 方法使用二次多项式来近似被积函数。我们需要三个点来确定这个多项式的系数。这些点分别是 *x* <sub>1</sub> = *a* 、 *x* <sub>3</sub> = *b* 和*x*<sub>2</sub>=(*a*+*b*)/2
> 
> [![1/3](../Images/753efa50735c1321ecbc0614996d0c97.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qyltl_nn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/milyzy904t8vhkj8v0u3.png)
> 
> > 方法中的 1/3 这个名字来源于表达式中的因子。
> 
> 如果您想用这种方法更精确地评估积分，您可以使用复合辛普森 1/3 方法，其中您必须使用偶数将整个区间分成 *n* 个子区间，因为辛普森 1/3 方法需要三个点来定义二次多项式，这意味着这种方法一次应用两个相邻的子区间。
> 
> [![1/3C](../Images/8b3b796d6e5184520f2efb6f504d1adf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aSiLCSp_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4a0qsfv9wczvdvxl1g6e.png)
> 
> > 其中，子间隔 *n* 必须等间距。以及*h*=(*b*-*a*)/*n*

* * *

> 辛普森的 3/8 方法使用三次多项式来逼近被积函数。我们需要四个点来确定这个多项式的系数。这些点分别是 *x* <sub>1</sub> = *a* ，*x*<sub>2</sub>=*a*+*h*，*x*T16】3=*a*+2*h*和*x*<sub>4</sub>=*b*
> 
> [![3/8](../Images/c735b866f93230b0e3d4125cbf97d86c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h8XmDdhN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pin7tseoulymiru9eonm.png)
> 
> > 方法中的 3/8 这个名字来源于表达式中的因子。
> 
> 如果你想用这种方法更精确地计算积分，你可以使用复合辛普森的 3/8 方法，其中你必须将整个区间分成可被 3 整除的 n 个子区间，因为辛普森的 3/8 方法需要四个点来构造一个三次多项式，这意味着这种方法一次应用三个相邻的子区间。
> 
> [![3/8C](../Images/e0e62330b9204b9db5988e61ee0da8ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iOWuyzZH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b3rsjarh9ku9ekovbyxr.png)
> 
> > 其中，子间隔 *n* 必须等间距。以及*h*=(*b*-*a*)/*n*

这些方法在现实世界中应用于计算面积、体积、曲线长度和其他与积分相关的问题。

例如:ECO 公司想要排水并填充一个深度为 5 英尺的受污染沼泽(见下图)。生态公司的首席执行官想知道排干沼泽后需要多少立方英尺的土地来填充该地区。

[![Marsh](../Images/9e9c57c0c171d875678e51a2debe5497.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YO3cA2kw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r89dpf5gxg0853j18syq.png)

为了解决这个问题，我使用了复合辛普森的 1/3 方法。

```
fn simpson(a: f64, b: f64, n: i32) -> f64 {
    let mut y: f64 = funcion(a) + funcion(b);
    let mut x: f64 = a;
    let h: f64 = (b-a)/(n as f64);

    for i in 1..n {
      x = x + h;
      if i % 2 == 0{
        y= y + 2.0*funcion(x);
      }else{
        y= y + 4.0*funcion(x);
      }
    }

    return (b-a) * y / (3.0*(n as f64));
} 
```

Enter fullscreen mode Exit fullscreen mode

解决方案:要计算沼泽的体积，我们必须首先使用复合辛普森 1/3 法估算表面积。

```
let mut resultado: i32 = simpson(vec![146, 122, 76, 54, 40, 30, 13], 20);

fn simpson(v: Vec<i32>, h: i32) -> i32 {
    let mut y: i32 = 0;
    let mut con: i32 = 0;
    let size: usize = v.len()-1;

    for i in v {
      if con == 0{
        y = y + i;
      }else if con == (size as i32) {
        y = y + i;
      }else{
        if con % 2 == 0{
          y= y + 2*i;
        }else{
          y= y + 4*i;
        }
      }
      con = con +1;
    }

    return h*y/3;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后乘以 5。

```
resultado = resultado * 5; 
```

Enter fullscreen mode Exit fullscreen mode

得出近似体积为`40 500 cubic feet`。

总之，当我们试图积分一个复杂的函数或者如果我们只有列表数据时，用数值方法积分是一种有用的技术。利用辛普森方法，我们可以将一个复杂的积分近似为一个多项式的积分，并得到一个近似精确解的解，甚至在某些情况下我们可以得到精确解。

### 参考文献

*   弗吉尼亚州苏布拉马年的吉拉特(2013 年)。工程师和科学家的数值方法。威利。第三版。
*   heath m .(2002 年)。科学计算:介绍性综述。麦格劳·希尔。第二版。
*   帽子，s .，通道，r。(2011 年)。工程师的数值方法。麦格劳希尔。第六版。

### 附加资源

*   LindaEliza 的 GitHub 中的[数值方法](https://github.com/LindaEliza/NumericalMethods)库。