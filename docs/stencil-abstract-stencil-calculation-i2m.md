# 蜡纸；抽象模板计算

> 原文：<https://dev.to/termoshtt/stencil-abstract-stencil-calculation-i2m>

我正在开发一个用于 Rust 模板计算的库。

[https://github.com/termoshtt/stencil](https://github.com/termoshtt/stencil)
抽象模板计算

stencil v0.2 发布在 [crates.io](https://crates.io/crates/stencil) 上，可以通过货物:
使用

```
[dependencies]
stencil = "0.2" 
```

Enter fullscreen mode Exit fullscreen mode

完整文档可在 [docs.rs](https://docs.rs/stencil/0.2.0/stencil/) 上获得。

## StencilArray 特征

这个库通过`StencilArray`特征:
抽象出一个模板计算

```
/// Array with stencil calculations
pub trait StencilArray<St>: NdArray
where
    St: Stencil<Elem = Self::Elem, Dim = Self::Dim>,
{
    /// Execute a stencil calculation
    fn stencil_map<Output, Func>(&self, out: &mut Output, Func)
    where
        Output: NdArray<Dim = Self::Dim>,
        Func: Fn(St) -> Output::Elem;
} 
```

Enter fullscreen mode Exit fullscreen mode

功能是这个机箱的核心。基本例子在这里:

```
let mut a = torus::Torus::<f64, Ix1>::zeros(n);
let mut b = a.clone();
a.coordinate_fill(|x| x.sin());
let dx = a.dx();
a.stencil_map(&mut b, |n: N1D1<f64>| (n.r - n.l) / (2.0 * dx)); 
```

Enter fullscreen mode Exit fullscreen mode

这个板条箱定义了`Torus<A, D>`，代表 N 维环面。`a`表示一维环面，我们可以把它看作一个具有周期边界条件的区域。`stencil_map`的第二个自变量，λ函数`|n: N1D1<f64>| (n.r - n.l) / (2.0 * dx)`表示中心差分。`N1D1`意为一维空间上的 1-邻居。因此，本例描述了`sin(x)`功能的一个主要区别，即`cos(x)`被设置为`b`。

`N1D1<A>`是一个小结构体:

```
/// one-neighbor, one-dimensional stencil
#[derive(Clone, Copy)]
pub struct N1D1<A: LinalgScalar> {
    /// left
    pub l: A,
    /// right
    pub r: A,
    /// center
    pub c: A,
} 
```

Enter fullscreen mode Exit fullscreen mode

并实现特征`Stencil`。它具有围绕“我们关注什么”的价值观；当我们使用`a`更新`b[i]`时，`n`的`a[i]`为`n.c`，它的邻居`a[i-1]`为`n.l`，而`a[i+1]`为`n.r`。这个板条箱以这种方式抽象模板计算。
对`Torus<A, D>`的`StencilArray`的实现描述了如何从数组`a`中获取`N1D1`。目前有三个`Stencil`:`N1D1<A>`、`N2D1<A>`、`N1D2<A>`，两个`StencilArray`:`Torus<A, D>`、`Line<A, P: Padding, E: Edge>`。`Line`在线的每条边上都有一个填充，可以看作是一个固定的边界条件。

## 多样特质

模版箱引入了一个更有用的特性，`Manifold` :

```
/// Uniformly coordinated array
pub trait Manifold: NdArray {
    /// Type of coordinate
    type Coordinate;

    /// Increment of coordinate
    fn dx(&self) -> Self::Coordinate;

    /// Fill manifold by a function
    fn coordinate_fill<F>(&mut self, F)
    where
        F: Fn(Self::Coordinate) -> Self::Elem;

    /// Map values on manifold using a function
    fn coordinate_map<F>(&mut self, F)
    where
        F: Fn(Self::Coordinate, Self::Elem) -> Self::Elem;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个特性给了 array 一个坐标。这有助于通过数学函数设置数组的值，如上面的例子
所示

```
a.coordinate_fill(|x| x.sin()); 
```

Enter fullscreen mode Exit fullscreen mode

`x`是由`Manifold`的实现为每个对象设置的。

## 例题 1:扩散方程

让我们求解周期性和固定边界条件的扩散方程:

```
fn periodic(r: Sender<Vec<f64>>) {
    let dt = 1e-3;
    let mut t = torus::Torus::<f64, Ix1>::zeros(32);
    t.coordinate_fill(|x| x.sin());
    let dx = t.dx();
    let mut s = t.clone();
    for _step in 0..3000 {
        t.stencil_map(&mut s, |n: N1D1<f64>| {
            let d2 = (n.l + n.r - 2.0 * n.c) / (dx.powi(2));
            n.c + dt * d2
        });
        r.send(s.as_view().to_vec()).unwrap();
        swap(&mut t, &mut s);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![heat-periodic](img/614e83c66b3ae174dd5861e50eb77c1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rFxX7vvn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t7ybxihlame7zx4kxk3u.png)T3】

```
fn fixed(r: Sender<Vec<f64>>) {
    let dt = 1e-3;
    let mut t = Line::<f64, P1, Closed>::new(64, 0.0, 1.0, 2.5 * PI);
    t.coordinate_fill(|x| x.sin());
    let dx = t.dx();
    let mut s = t.clone();
    for _step in 0..10000 {
        t.stencil_map(&mut s, |n: N1D1<f64>| {
            let d2 = (n.l + n.r - 2.0 * n.c) / (dx.powi(2));
            n.c + dt * d2
        });
        r.send(s.as_view().to_vec()).unwrap();
        swap(&mut t, &mut s);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![heat-fixed](img/68fe3592ac8c6a9e7c3871bca563d223.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tWl9sLv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9bigc0yi6cewzq0devsb.png)

本例使用 [asink](https://github.com/termoshtt/asink) 存储数据。请看[上一篇](https://dev.to/termoshtt/asink-async-sink-for-time-series-data--2nl)。

## 例题 2: KdV 方程

最后，我们用 ZK 格式求解 KdV 方程:

```
fn kdv(r: Sender<Vec<f64>>) {
    let dt = 1e-5;
    let mut t = torus::Torus::<f64, Ix1>::zeros(128);
    t.coordinate_fill(|x| x.sin());
    let dx = t.dx();
    let mut b = t.clone();
    let mut n = t.clone();
    for step in 0..1_000_000 {
        t.stencil_map(&mut n, |n: N2D1<f64>| {
            // ZK scheme
            let uux = (n.l + n.c + n.r) * (n.r - n.l) / (3.0 * dx);
            let u3 = (n.rr - 2.0 * n.r + 2.0 * n.l - n.ll) / dx.powi(3);
            uux + 0.01 * u3
        });
        azip!(mut n, b in { *n = b - dt * *n });
        if step % 1000 == 0 {
            r.send(n.as_view().to_vec()).unwrap();
        }
        swap(&mut t, &mut b);
        swap(&mut t, &mut n);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![kdv](img/f278652752ad9c70f6bbce778d274fb4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ba3GQRMo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xggcqb6tceorgsubdmeq.png)

蛙跳算法也很容易写。

完整示例请参见[示例/目录](https://github.com/termoshtt/stencil/tree/master/examples)。
我想得到你的反馈。谢了。