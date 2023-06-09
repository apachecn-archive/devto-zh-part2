# 计算流数据的标准偏差

> 原文：<https://dev.to/nestedsoftware/calculating-standard-deviation-on-streaming-data-253l>

> 这篇文章是关于[移动平均线](https://dev.to/nestedsoftware/calculating-a-moving-average-on-streaming-data-5a7k)的文章的延续，所以先阅读那篇文章可能是个好主意。

在本文中，我们将探索增量计算方差和标准差。想法是提供一种方法，该方法:

*   可以计算数据流的方差，而不需要所有数据从一开始就可用。
*   是“数值稳定的”，也就是说，在使用浮点数时，精度问题较少。

> 我在本文中提出的结果，被称为“威尔福德方法”，有点名气。它最初是由 B. P .威尔福德发明的，并由 T2·克努特在 T4 的《计算机编程的艺术》中推广。，第 232 页。).

这一次推导的数学计算时间要长一点，所以对于不耐烦的人，我决定先展示 JavaScript 代码。

核心逻辑只需要我们将这额外的代码添加到我们的`update`方法:

```
 const dSquaredIncrement = 
            (newValue - newMean) * (newValue - this._mean)

        const newDSquared = this._dSquared + dSquaredIncrement 
```

Enter fullscreen mode Exit fullscreen mode

很有意思吧？在方差公式中，我们通常看到总和σ(值 <sub>i</sub> -均值) <sup>2</sup> 。直观地说，这里我们是在平均值的当前值和前一个值之间进行插值。我认为一个人甚至可以在没有严格推导公式的情况下，通过玩游戏偶然发现这个结果。

> 什么是`dSquared`？这是我为方差* (n-1)，或者只是 n，而这取决于我们是在讨论样本方差还是总体方差(T2)。我们将在本文后面的数学推导部分看到这个术语。也见我的文章[标准差的几何](https://dev.to/nestedsoftware/the-geometry-of-standard-deviation--3m3o)。

下面是一个简单的实现，当我们从数据流中接收值时，它计算平均值、方差和标准差:

```
class RunningStatsCalculator {
    constructor() {
        this.count = 0
        this._mean = 0
        this._dSquared = 0
    }

    update(newValue) {
        this.count++

        const meanDifferential = (newValue - this._mean) / this.count

        const newMean = this._mean + meanDifferential

        const dSquaredIncrement = 
            (newValue - newMean) * (newValue - this._mean)

        const newDSquared = this._dSquared + dSquaredIncrement

        this._mean = newMean

        this._dSquared = newDSquared
    }

    get mean() {
        this.validate()
        return this._mean
    }

    get dSquared() {
        this.validate()
        return this._dSquared
    }

    get populationVariance() {
        return this.dSquared / this.count
    }

    get populationStdev() {
        return Math.sqrt(this.populationVariance)
    }

    get sampleVariance() {
        return this.count > 1 ? this.dSquared / (this.count - 1) : 0
    }

    get sampleStdev() {
        return Math.sqrt(this.sampleVariance)
    }

    validate() {
        if (this.count == 0) {
            throw new StatsError('Mean is undefined')
        }
    }   
}

class StatsError extends Error {
    constructor(...params) {
        super(...params)

        if (Error.captureStackTrace) {
            Error.captureStackTrace(this, StatsError)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们以传统的方式为这些统计数据编写代码，以便进行比较:

```
const sum = values => values.reduce((a,b)=>a+b, 0)

const validate = values =>  {
    if (!values || values.length == 0) {
        throw new StatsError('Mean is undefined')
    }
}

const simpleMean = values => {
    validate(values)

    const mean = sum(values)/values.length

    return mean
}

const simpleStats = values => {
    const mean = simpleMean(values)

    const dSquared = sum(values.map(value=>(value-mean)**2))

    const populationVariance = dSquared / values.length
    const sampleVariance = values.length > 1 
        ? dSquared / (values.length - 1) : 0

    const populationStdev = Math.sqrt(populationVariance)
    const sampleStdev = Math.sqrt(sampleVariance)

    return {
        mean,
        dSquared,
        populationVariance,
        sampleVariance,
        populationStdev,
        sampleStdev
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们用一个简单的演示来比较结果:

```
const simple= simpleStats([1,2,3])

console.log('simple mean = ' + simple.mean)
console.log('simple dSquared = ' + simple.dSquared)
console.log('simple pop variance = ' + simple.populationVariance)
console.log('simple pop stdev = ' + simple.populationStdev)
console.log('simple sample variance = ' + simple.sampleVariance)
console.log('simple sample stdev = ' + simple.sampleStdev)
console.log('')

const running = new RunningStatsCalculator()
running.update(1)
running.update(2)
running.update(3)

console.log('running mean = ' + running.mean)
console.log('running dSquared = ' + running.dSquared)
console.log('running pop variance = ' + running.populationVariance)
console.log('running pop stdev = ' + running.populationStdev)
console.log('running sample variance = ' + running.sampleVariance)
console.log('running sample stdev = ' + running.sampleStdev) 
```

Enter fullscreen mode Exit fullscreen mode

令人高兴的是，结果在意料之中:

```
C:\dev\runningstats>node StatsDemo.js
simple mean = 2
simple dSquared = 2
simple pop variance = 0.6666666666666666
simple pop stdev = 0.816496580927726
simple sample variance = 1
simple sample stdev = 1

running mean = 2
running dSquared = 2
running pop variance = 0.6666666666666666
running pop stdev = 0.816496580927726
running sample variance = 1
running sample stdev = 1 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在让我们继续数学。尽管这次的推导更长，但数学并不比之前的文章更难理解，所以如果你感兴趣的话，我鼓励你继续学习。知道一件事情如何和为什么工作总是很好的！

让我们从方差(标准差的平方)的公式开始:

[![variance formula](img/faf27dbf03749c201e5e06098c6e6dc5.png "variance formula")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aohE_HiH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wzguiyf0c3jtu3api0xi.png)

接下来，我们将两边乘以 n-1(或者在人口方差的情况下乘以 n):

[![variance times n-1](img/b7f0df7781ea6d3e05a1c16da00a9bf9.png "variance times n-1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p598aTsC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5w4k4qf0ltjwruub7ket.png)

我将这个值定义为`d²`(参见我关于标准差的[几何的文章):](https://dev.to/nestedsoftware/the-geometry-of-standard-deviation--3m3o)

[![d squared](img/ce2498663b637d44ae3373e7931b527e.png "d squared")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--21Po9epk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ucyr0v35zg1r5q61ysns.png)

我们可以使用以下标识来扩展它:

[![(a-b)^2](img/f07e055b286d9c48a256bfba8b1ce04f.png "(a-b)^2")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hfUOv0IG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ch48vlwcm90gei6jo3v.png)

应用这个替换，我们得到:

[![expand d squared](img/fe179581bbbd1dae92dd42cfb775a875.png "expand d squared")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lUGEet_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xs6v3nhi14rpnypfzvpo.png)

让我们将总和分成三个独立的部分:

[![separate summation into parts](img/b70cdf187c2df93ac0138e390b170387.png "separate summation into parts")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DnYAYX7u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gyqik7gplkkgc42qic83.png)

现在我们可以分解出常数:

[![factor out constants](img/c542ac83bc0b14ede3d08de407498e58.png "factor out constants")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X0jK_gVH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/akys1qiet14t1ldns7sz.png)

与前一篇文章一样，我们将使用以下恒等式(总数=平均数*计数):

[![total = mean * count](img/037ba1597ef379fd74c1b44051fd537a.png "total = mean * count")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s0BJq_Mz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pe36aaojidy23ziujtl1.png)

将此代入我们之前等式的第二项求和，得出:

[![sustitute mean * count for total](img/979c3d96fd984b9e157d46083b572471.png "sustitute mean * count for total")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OmTBeRd2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q1j2dfa8nmzmr9c2bsxs.png)

从 i=1 到 i=n 的 1 之和正好是 n:

[![summation of 1](img/eb5266d09df485974c8045a3d2d413e6.png "summation of 1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lBvHv-Ga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ltorvgrtj31xs5pkagx2.png)

因此，我们可以将等式简化如下:

[![simplify previous step](img/79428636cb114c6611e44ae7a7e963f3.png "simplify previous step")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9APlhzk1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ptbihvpuzdu7heh003dj.png)

我们可以将最后两项结合起来，得到以下结果:

[![reduce](img/187f71b391879edd48b532fbe084a233.png "reduce")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rYmaYqFw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hp2klsp3q6s65vi7oq4s.png)

现在我们有了这个结果，我们可以使用相同的等式来获得第一个`n-1`项的`d²`，即除了最近的一个之外的所有值:

[![d^2 for n-1](img/fe466a1d499374d1b09329b4163d6f3e.png "d^2 for n-1")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WuthpbYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fwz2lq5b0fv6emq050gm.png)

让我们减去这两个量:

[![d_n^2 - d_(n-1)^2](img/61ee1ad534def8ed1ff35c2889d8eec6.png "d_n^2 - d_(n-1)^2")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hFI5V7LR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pwgi0ucpef3rzar710br.png)

将-1 乘以括号中的表达式，我们得到:

[![multiply -1 through](img/9a22523530289b28fb74e88c3e268920.png "multiply -1 through")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eNptSRhV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ay9t7p0g014pmxk413pq.png)

当我们减去∑x <sub>i</sub> 直到 n - ∑x <sub>i</sub> 直到 n-1 时，只剩下最后一个值，x<sub>n</sub>T6】2:

[![subtract summations](img/1c5b92672086dc238f9b70a3e8cdf4f4.png "subtract summations")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wcqsLzjs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i92vj2h64mgrc0rbjofy.png)

这样我们就可以去掉两个求和项，简化等式:

[![simplified](img/605c4c8f86d94e8d5b59e90169aebff7.png "simplified")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AqOobjk8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3opp6jsc5lolyy78ljf0.png)

乘以最后一项得出:

[![simplified](img/60a7e1af87c84190e6d9a7878d7569cb.png "simplified")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---FxkVIzq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ma1y8wpoyf8ufk6xg3dq.png)

重新排列顺序，我们得到:

[![rearrange order](img/680d16198ab5ab52eab2db64c8940cc8.png "rearrange order")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tH2qcBpw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8tw3661pf0ah92m9r23s.png)

去掉最后两项中的 n，我们得到:

[![rearrange order](img/8a7be92c02d941a5220fea990113e5fc.png "rearrange order")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RUR-N1Rk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vf03hlzyzr35d9w3mjmq.png)

我们知道:

[![a^2 - b^2 = (a-b)*(a+b)](img/abb1f5eb913ac6871aeb5ee1c10a6aa9.png "a^2 - b^2 = (a-b)*(a+b)")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7fMIk21z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3cafat4hhkq2vvqbsn4k.png)

让我们将此应用于等式括号中的表达式:

[![apply a^2 - b^2 = (a-b)*(a+b)](img/95bd8b62f008c6e9e688cf5a00057680.png "apply a^2 - b^2 = (a-b)*(a+b)")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SmEOZN2W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/827vbfcmpqnmuuiyq6du.png)

我们快到了！现在是时候应用下面的恒等式了，它是在[最后一篇文章](https://dev.to/nestedsoftware/calculating-a-moving-average-on-streaming-data-5a7k)的最后得出的:

[![identity from previous article](img/4dbdfb8f13c7065ea60ae4090fca5cf3.png "identity from previous article")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kGRX-pGT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hrgt967ztsg56xltxyfj.png)

应用这个恒等式，我们得到:

[![apply identity from previous article](img/38b9eb1cc91145cbc140d9be98b5b260.png "apply identity from previous article")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QRg2FpOs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/36kseu0aouinzvchm1vk.png)

通过倍增，我们有:

[![multiply through](img/1c801b7e726a95429d0003bcdbce4f1d.png "multiply through")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---MsqKp2y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t7ttyorx8dc3chbtvp9f.png)

我们可以取消相同值的减法，并重新排列一位以获得以下结果:

[![simplify and rearrange](img/77c66a07f1d4da72f1300e572ed55871.png "simplify and rearrange")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rlG3ehwE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nrmapfrq80udjx20by5r.png)

我们知道:

[![(x-a)*(x-b) = x^2 - bx - ax + ab](img/c9d946bfed99251704b60ba726a544e8.png "(x-a)*(x-b) = x^2 - bx - ax + ab")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SLATYa_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5pwfchg4at8q3itmar2w.png)

这使我们能够很好地简化我们的等式:

[![simplify](img/c51e828d0bb05f7317d9ca9d1b38160c.png "simplify")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---EVbsvqz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/130yhyqnsbyc7cn81ton.png)

我们现在可以将两边的 d <sup>2</sup> <sub>n-1</sub> 相加得到我们的最终结果！

[![final result](img/582d9de4644d9bbb2e0853928dae734a.png "final result")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---CIQGJN6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27ou4zgk6ir2ox8gj2b5.png)

这是一段漫长的跋涉，但我们现在有了我们一直在寻找的宝石。和上一篇文章一样，我们有一个很好的递归关系。这个允许我们通过增加一个增量来计算新的 d <sup>2</sup> 。

为了得到方差，我们只需将 d <sup>2</sup> 除以 n 或 n-1:

[![variance](img/51cdbacfd8cc3524df9f8eb6543ed7f5.png "variance")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aA_GLQPe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/usmpzl2hsr60v5nfbvmh.png)

依次求方差的平方根，得到标准偏差:

[![standard deviation](img/8908ca6ca0aa895e0f2ef5116001fceb.png "standard deviation")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jY4uIo6X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tr8dnk8m18sl8nb235a5.png)

参考资料:

*   [加权平均值和方差的增量计算](http://people.ds.cam.ac.uk/fanf2/hermes/doc/antiforgery/stats.pdf)，作者[托尼·芬奇](http://dotat.at/)
*   [精确计算运行方差](https://www.johndcook.com/blog/standard_deviation/)，作者[约翰·d·库克](https://www.johndcook.com/blog/top/)
*   [比较三种计算标准差的方法](https://www.johndcook.com/blog/2008/09/26/comparing-three-methods-of-computing-standard-deviation/)，作者[约翰·d·库克](https://www.johndcook.com/blog/top/)
*   [数值结果的理论解释](https://www.johndcook.com/blog/2008/09/28/theoretical-explanation-for-numerical-results/)，作者[约翰·d·库克](https://www.johndcook.com/blog/top/)

相关:

*   [计算流数据的移动平均值](https://dev.to/nestedsoftware/calculating-a-moving-average-on-streaming-data-5a7k)
*   [流数据的指数移动平均](https://dev.to/nestedsoftware/exponential-moving-average-on-streaming-data-4hhl)
*   [标准差的几何形状](https://dev.to/nestedsoftware/the-geometry-of-standard-deviation--3m3o)