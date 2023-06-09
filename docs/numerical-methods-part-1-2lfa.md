# 数值方法(第一部分)

> 原文：<https://dev.to/rinsama77/numerical-methods-part-1-2lfa>

在开始学习这门课程之前，让我们先看看“什么是数值方法”到底是什么意思。

### 什么是数值方法？

数值方法是将数学问题公式化的技术，使得它们可以用算术运算来解决，产生数值结果而不是封闭形式的结果。大多数数值方法涉及大量的算术计算；大部分都是*试错*。

有时，工程问题不能有效地得到精确的答案。取而代之的是大量的工程问题通过反复试验得以解决(由于快速和先进的计算机的发展)。

我们来看一个例子:)

##### 寻找无理数平方根

[![](img/e7fdbc3dba6b7333b2fd5a6a13bc2ebd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c60FCLeJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yrc5w4rfafovjbrka9gq.png)

> 我们继续如果 x<sup>2</sup>T8】6。但是如果仍然超过，我们继续下一个小数点。但是我们必须在某个地方停下来，而且是在结果*足够接近*的时候；这是我们通过近似。(但是我们必须小心**精度**和**精度**。

一张图来演示什么是准确度和精度:
[![](img/7e6bae43bbcdbcb4f6dde27d3534afc6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_fcaov7E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p4r0iwazptd2ri7n40ac.png) 
让我们继续看我们是如何解决问题的..
[![](img/859b876332233a03abbee13404a12e18.png)T8】](https://res.cloudinary.com/practicaldev/image/fetch/s--k1sUWwfl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z221nlkle5cy7albl0ew.png)

* * *

### 错误

但是，嘿，如果有一个近似值，那就是一个*误差*。有两种类型的错误；*真实误差*和*近似误差*。

##### 真误差

> 真误差(E <sub>t</sub> ) =真值-近似值
> 真关系误差(ɛ<sup>t</sup>)=<sup>真值-近似值</sup><sub>真值</sub> x 100

但是真误差的问题是我们不知道确切的*真值*是多少。所以我们用*代替近似误差*。

##### 近似误差

> ɛ<sub>α</sub>=<sup>近似误差</sup>‵<sub>近似</sub>x 100
> ɛ<sub>α</sub>=<sup>当前近似-先前近似</sup>‵<sub>当前近似</sub> x 100

近似误差有两种类型:

1) **截断误差**是通过截断一个无穷和并用一个有限和近似它而产生的误差。
2) **舍入误差**是由于舍入而导致的一个数字的计算近似值与其精确数学值之间的差异。

好的，那么回到“何时停止”的事情和近似值。我们将如何*有足够的信心*停止计算？

答案是(不完全是，但我们现在用的是)什么时候..|ɛ<sub>α</sub>|<ɛ<sub>s</sub>

> ɛ<sub>α</sub>是近似误差
> ɛ<sub>s</sub>=(0.5×10<sup>2-n</sup>)%
> 
> *   ɛ的 s 已经是百分比了
> *   n 是我们想要的一些有效数字

下面是一个计算√6 的 octave(类似于 matlab，但免费、开源)代码。

```
% take squareroot of 6
m = 6;
true_value = 2.44948974270319;

% nf = the number of the significant figures
nf = 4;
es = 0.5 * 10^(2-nf);
printf('es = %f\n', es);

% set x = the approximation answer
% initialize x = 0
previous_result = 0;
x = 0;

% outer loop to loop for the number of digit
% we want to do this for ndigits
n = 6;

%for d = 0:n;
ea = 100;

d = 0;

while (abs(ea) > es)
    previous_result = x;
    for i = 0:10
      x = previous_result + i/(10^d);
      if (x*x > m)
        break;
      endif
    endfor
    x = x - 1/(10^d);
    ea = ((x - previous_result)/x)*100;
    et = ((true_value - x)/true_value) * 100;
    printf('x = %f, ea = %f, et = %f\n', x, ea, et);
    d++;

endwhile

%printf('x = %f.\n', x); 
```

而下面是上面代码的结果:

```
es = 0.005000
x = 2.000000, ea = 100.000000, et = 18.350342
x = 2.400000, ea = 16.666667, et = 2.020410
x = 2.440000, ea = 1.639344, et = 0.387417
x = 2.449000, ea = 0.367497, et = 0.019994
x = 2.449400, ea = 0.016331, et = 0.003664
x = 2.449480, ea = 0.003266, et = 0.000398 
```

好了，今天我就讲到这里，下一部分再见！无红利